# Keycloak Tutorial — From Zero to a Secured Spring Boot + React App

*Based on Keycloak 26.7.x, Spring Boot 3.x, Spring Security 6.x*

---

## 1. What is Keycloak?

Keycloak is an open-source **Identity and Access Management (IAM)** server, originally built by Red Hat and now a CNCF project. Instead of writing login, registration, password reset, session management, and role checking inside every application you build, you run Keycloak as a separate server and let it handle all of that.

Your application stops asking *"is this password correct?"* and starts asking *"is this token valid, and what does it say about the user?"*

### What it gives you out of the box

| Feature | What it means |
|---|---|
| **Single Sign-On (SSO)** | Log in once, access all your apps. Log out once, logged out everywhere. |
| **Standard protocols** | OpenID Connect, OAuth 2.0, SAML 2.0 |
| **Identity brokering** | "Login with Google / GitHub / Facebook" without writing the integration |
| **User federation** | Connect an existing LDAP or Active Directory as the user source |
| **Two-factor auth** | TOTP/OTP built in, no extra code |
| **Admin console** | Web UI to manage realms, users, roles, clients |
| **Admin REST API** | Automate everything the console does |
| **Login pages** | Ready-made, themeable login/register/reset-password screens |
| **Fine-grained authorization** | Policies, permissions, resource-based access control |

### When you should use it

**Good fit:**
- Microservices — one auth server, many services validating the same tokens
- You need SSO across several apps
- You need social login or enterprise LDAP integration
- You want serious auth (2FA, brute-force detection, token rotation) without building it
- Enterprise or B2B products where customers expect SAML/OIDC

**Probably overkill:**
- A single small app with 50 users and simple email/password login
- A hobby project where a JWT filter and a `users` table is honestly enough
- You have no capacity to run and maintain another server + database

Keycloak is a real piece of infrastructure. It needs a database, memory (~1GB+), TLS in production, and upgrade maintenance. Don't adopt it just because it's popular — adopt it when the identity problem is genuinely bigger than one app.

---

## 2. Core Concepts

You must understand these five words before anything else makes sense.

### Realm
A **realm** is an isolated tenant. It has its own users, its own roles, its own clients, its own signing keys. Users in realm A cannot log into realm B.

- The `master` realm is for administering Keycloak itself. **Never put your application users there.**
- Create a dedicated realm for your project, e.g. `myapp`.
- Multi-tenant SaaS? Either one realm per tenant, or one realm with groups/organizations.

### Client
A **client** is an application that wants Keycloak to authenticate users for it. Two important types:

| Type | Client authentication | Used by | Example |
|---|---|---|---|
| **Public** | Off (no secret) | Browser SPAs, mobile apps | React frontend |
| **Confidential** | On (has a secret) | Backend servers | Spring Boot service, BFF gateway |

**Rule:** a React/Vue/Flutter app can never keep a secret — anyone can open DevTools. So frontends are always **public clients using Authorization Code + PKCE**. Never use the implicit flow, and never put a client secret in frontend code.

A backend REST API that only *validates* tokens (never logs anyone in) is called a **resource server**. It often doesn't need a client registration at all — it just needs to know the realm's issuer URL.

### User
An account in a realm: username, email, credentials, attributes, group memberships, role assignments.

### Role
A named permission label.

- **Realm role** — global to the realm: `admin`, `user`, `manager`
- **Client role** — scoped to one client: for client `orders-api`, roles `order:read`, `order:write`

Realm roles land in the token under `realm_access.roles`. Client roles land under `resource_access.<client-id>.roles`. This distinction matters a lot when you write the Spring Security converter later.

### Group
A collection of users. Assign roles to the group, and every member inherits them. Groups can nest. Manage permissions by group, not user-by-user — your future self will thank you.

### Also worth knowing

- **Client scope** — a reusable bundle of claims/mappers you attach to clients (e.g. `email`, `profile`, or your own `roles-scope`).
- **Protocol mapper** — a rule that puts extra data into the token. Want the user's `department` attribute inside the JWT? That's a mapper.
- **Composite role** — a role that automatically grants other roles. `manager` → grants `user` + `report:read`.

---

## 3. How the Login Flow Actually Works

This is the **Authorization Code Flow with PKCE**, the one you will use 95% of the time.

```
 Browser (React)          Keycloak                 Spring Boot API
      |                       |                            |
 1.   |--- user clicks login ->|                           |
      |   /auth?client_id=...&code_challenge=XYZ           |
      |                       |                            |
 2.   |<-- login page --------|                            |
      |--- username/password ->|                           |
      |                       |                            |
 3.   |<-- redirect back with ?code=ABC                    |
      |                       |                            |
 4.   |--- POST /token  code=ABC + code_verifier ---->     |
      |<-- access_token + refresh_token + id_token ---     |
      |                       |                            |
 5.   |--- GET /api/orders  Authorization: Bearer <token> ->|
      |                       |                            |
 6.   |                       |<-- GET /certs (JWKS, cached)|
      |                       |--- public keys ----------->|
      |                       |          verify signature  |
      |<---------------------------- 200 JSON -------------|
```

**Key insight:** in step 6, the API validates the token *offline* using Keycloak's public key. It does **not** call Keycloak on every request. It downloads the public keys once (JWKS) and caches them. This is why the architecture scales.

### The three tokens

| Token | Purpose | Lifetime | Who reads it |
|---|---|---|---|
| **access_token** | Sent to APIs to prove authorization | short (5 min default) | your backend |
| **id_token** | Describes *who* the user is | short | your frontend |
| **refresh_token** | Used to get a new access token silently | longer (30 min default) | your frontend/backend |

**Never** use the id_token to call your API. **Never** send the refresh_token to your API.

### A Keycloak access token, decoded

```json
{
  "exp": 1757505600,
  "iat": 1757505300,
  "iss": "http://localhost:8080/realms/myapp",
  "aud": "account",
  "sub": "f4b3c1a2-8e7d-4f6a-9b2c-1d3e5f7a9b0c",
  "typ": "Bearer",
  "azp": "myapp-frontend",
  "realm_access": {
    "roles": ["default-roles-myapp", "USER", "ADMIN"]
  },
  "resource_access": {
    "orders-api": {
      "roles": ["order:read", "order:write"]
    }
  },
  "scope": "openid profile email",
  "preferred_username": "mengheak",
  "email": "mengheak@example.com",
  "email_verified": true
}
```

Note `sub` — that UUID is the stable user ID. **Store that in your database as the foreign key**, not the username or email, because those can change.

---

## 4. Running Keycloak Locally

### Fastest way — dev mode (throwaway, in-memory)

```bash
docker run -p 8080:8080 \
  -e KC_BOOTSTRAP_ADMIN_USERNAME=admin \
  -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:26.7.3 \
  start-dev
```

Open http://localhost:8080 → log in with `admin` / `admin`.

`start-dev` uses an in-memory H2 database, disables HTTPS requirements, and disables caching. **All your data disappears when the container stops.** Fine for a first look, useless for real work.

> Older tutorials use `KEYCLOAK_ADMIN` / `KEYCLOAK_ADMIN_PASSWORD`. Those were renamed to `KC_BOOTSTRAP_ADMIN_*` in Keycloak 26.

### Proper local setup — Docker Compose with PostgreSQL

`docker-compose.yml`:

```yaml
services:
  postgres:
    image: postgres:17
    container_name: keycloak-db
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: keycloak_password
    volumes:
      - keycloak_db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U keycloak"]
      interval: 10s
      timeout: 5s
      retries: 5

  keycloak:
    image: quay.io/keycloak/keycloak:26.7.3
    container_name: keycloak
    command: start-dev
    environment:
      KC_BOOTSTRAP_ADMIN_USERNAME: admin
      KC_BOOTSTRAP_ADMIN_PASSWORD: admin
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres:5432/keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: keycloak_password
      KC_HEALTH_ENABLED: "true"
    ports:
      - "8080:8080"
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  keycloak_db:
```

```bash
docker compose up -d
docker compose logs -f keycloak
```

Now your realms and users survive restarts.

---

## 5. Configuring Your First Realm — Step by Step

### Step 1 — Create the realm
1. Top-left dropdown (says `master`) → **Create realm**
2. Realm name: `myapp`
3. **Create**

Everything from here on is inside `myapp`. Check the dropdown before every step — configuring things in `master` by accident is the single most common beginner mistake.

### Step 2 — Create realm roles
**Realm roles** → **Create role**

Create two: `USER` and `ADMIN`.

> Tip: use uppercase names without a prefix. Spring Security adds the `ROLE_` prefix itself; if you name the role `ROLE_ADMIN` in Keycloak you'll end up with `ROLE_ROLE_ADMIN`.

### Step 3 — Create a backend client (confidential)
**Clients** → **Create client**

- Client type: `OpenID Connect`
- Client ID: `orders-api`
- Next → Client authentication: **On**
- Authorization: Off (unless you want fine-grained policies)
- Authentication flow: check **Service accounts roles** (enables machine-to-machine), uncheck Standard flow if the API never logs users in
- Save

Grab the secret from the **Credentials** tab. This is what one backend uses to call another backend.

### Step 4 — Create a frontend client (public)
**Clients** → **Create client**

- Client ID: `myapp-frontend`
- Client authentication: **Off**
- Standard flow: **checked**, Direct access grants: unchecked
- **Valid redirect URIs**: `http://localhost:5173/*`
- **Valid post logout redirect URIs**: `http://localhost:5173/*`
- **Web origins**: `http://localhost:5173`
- Save

> Never set redirect URI or Web origins to `*` — that's an open redirect vulnerability and a CORS hole. List exact origins.

### Step 5 — Create a user
**Users** → **Add user**
- Username: `mengheak`
- Email verified: On (skip verification locally)
- Create → **Credentials** tab → Set password → Temporary: **Off**
- **Role mapping** tab → Assign role → `USER`

### Step 6 — Verify the realm is alive

```bash
curl http://localhost:8080/realms/myapp/.well-known/openid-configuration | jq
```

This **discovery document** lists every endpoint. It's what Spring Boot reads automatically from `issuer-uri`. If this URL returns JSON, your realm is configured correctly.

### Step 7 — Get a token from the command line (for testing)

Temporarily enable **Direct access grants** on `myapp-frontend`, then:

```bash
curl -X POST \
  http://localhost:8080/realms/myapp/protocol/openid-connect/token \
  -d "client_id=myapp-frontend" \
  -d "username=mengheak" \
  -d "password=yourpassword" \
  -d "grant_type=password" | jq -r .access_token
```

Paste the result into https://jwt.io to inspect the claims.

> The `password` grant type is **only for local testing**. It is deprecated in OAuth 2.1 and must not be used in production apps. Turn Direct access grants back off when you're done.

---

## 6. Securing a Spring Boot API

### The critical thing to know first

**The Keycloak Spring Boot adapter is dead.** <cite index="19-1">The Keycloak Spring Boot adapter was deprecated in Keycloak 20 and removed entirely in later versions, so guides referencing `keycloak-spring-boot-starter` or `KeycloakWebSecurityConfigurerAdapter` no longer work with current versions of either Keycloak or Spring Boot.</cite>

If you find a tutorial using any of these, close it:
- `keycloak-spring-boot-starter`
- `keycloak-spring-security-adapter`
- `KeycloakWebSecurityConfigurerAdapter`
- `KeycloakAuthenticationProvider`

<cite index="20-1">The modern approach uses Spring Security's built-in OAuth2 support, which also makes your app portable across any OIDC-compliant provider — Keycloak, Auth0, or anything else — without changing your security configuration code.</cite>

### Dependencies

**Maven:**
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
    </dependency>
</dependencies>
```

**Gradle (Kotlin DSL):**
```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-security")
    implementation("org.springframework.boot:spring-boot-starter-oauth2-resource-server")
}
```

Notice: **zero Keycloak dependencies.** That's the point.

### Configuration

`application.yml`:
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8080/realms/myapp

server:
  port: 8081

logging:
  level:
    org.springframework.security: DEBUG   # remove in production
```

That single `issuer-uri` line is enough for Spring Boot to:
1. Fetch `/.well-known/openid-configuration`
2. Find the JWKS endpoint
3. Download and cache the public keys
4. Validate signature, expiry, and issuer on every request

### Security configuration

```java
package com.example.orders.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;

import java.util.List;

@Configuration
@EnableMethodSecurity   // enables @PreAuthorize
public class SecurityConfig {

    @Bean
    SecurityFilterChain filterChain(HttpSecurity http,
                                    KeycloakJwtConverter jwtConverter) throws Exception {
        http
            // Stateless API: no session, no CSRF token needed
            .csrf(csrf -> csrf.disable())
            .cors(cors -> cors.configurationSource(corsSource()))
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/actuator/health", "/public/**").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/orders/**").hasRole("USER")
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt.jwtAuthenticationConverter(jwtConverter))
            );

        return http.build();
    }

    @Bean
    CorsConfigurationSource corsSource() {
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowedOrigins(List.of("http://localhost:5173"));
        config.setAllowedMethods(List.of("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS"));
        config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
        config.setMaxAge(3600L);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);
        return source;
    }
}
```

### The role converter — the part everyone gets wrong

By default Spring Security reads authorities from the `scope` claim. Keycloak puts roles in `realm_access.roles`. So without a converter, **`hasRole("ADMIN")` will always fail** even though the token clearly contains `ADMIN`. This is the #1 Keycloak + Spring Boot bug.

```java
package com.example.orders.config;

import org.springframework.core.convert.converter.Converter;
import org.springframework.security.authentication.AbstractAuthenticationToken;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.oauth2.jwt.Jwt;
import org.springframework.security.oauth2.server.resource.authentication.JwtAuthenticationToken;
import org.springframework.security.oauth2.server.resource.authentication.JwtGrantedAuthoritiesConverter;
import org.springframework.stereotype.Component;

import java.util.*;
import java.util.stream.Collectors;
import java.util.stream.Stream;

@Component
public class KeycloakJwtConverter implements Converter<Jwt, AbstractAuthenticationToken> {

    private static final String CLIENT_ID = "orders-api";
    private final JwtGrantedAuthoritiesConverter defaultConverter =
            new JwtGrantedAuthoritiesConverter();   // keeps SCOPE_* authorities

    @Override
    public AbstractAuthenticationToken convert(Jwt jwt) {
        Collection<GrantedAuthority> authorities = Stream.of(
                defaultConverter.convert(jwt).stream(),
                realmRoles(jwt).stream(),
                clientRoles(jwt).stream()
        ).flatMap(s -> s).collect(Collectors.toSet());

        // principal name = the stable Keycloak user ID
        return new JwtAuthenticationToken(jwt, authorities, jwt.getClaimAsString("sub"));
    }

    @SuppressWarnings("unchecked")
    private Collection<GrantedAuthority> realmRoles(Jwt jwt) {
        Map<String, Object> realmAccess = jwt.getClaimAsMap("realm_access");
        if (realmAccess == null) return List.of();

        Collection<String> roles = (Collection<String>) realmAccess.get("roles");
        if (roles == null) return List.of();

        return roles.stream()
                .map(role -> new SimpleGrantedAuthority("ROLE_" + role))
                .collect(Collectors.toSet());
    }

    @SuppressWarnings("unchecked")
    private Collection<GrantedAuthority> clientRoles(Jwt jwt) {
        Map<String, Object> resourceAccess = jwt.getClaimAsMap("resource_access");
        if (resourceAccess == null) return List.of();

        Map<String, Object> client = (Map<String, Object>) resourceAccess.get(CLIENT_ID);
        if (client == null) return List.of();

        Collection<String> roles = (Collection<String>) client.get("roles");
        if (roles == null) return List.of();

        // client roles kept as plain authorities: hasAuthority("order:read")
        return roles.stream()
                .map(SimpleGrantedAuthority::new)
                .collect(Collectors.toSet());
    }
}
```

**`ROLE_` prefix rule:** `hasRole("ADMIN")` internally checks for the authority `ROLE_ADMIN`. `hasAuthority("ADMIN")` checks for exactly `ADMIN`. Pick one convention and stick to it.

### The controller

```java
package com.example.orders.web;

import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.jwt.Jwt;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Map;

@RestController
@RequestMapping("/api")
public class OrderController {

    @GetMapping("/me")
    public Map<String, Object> me(@AuthenticationPrincipal Jwt jwt) {
        return Map.of(
            "userId",   jwt.getSubject(),
            "username", jwt.getClaimAsString("preferred_username"),
            "email",    jwt.getClaimAsString("email"),
            "roles",    jwt.getClaimAsMap("realm_access").get("roles")
        );
    }

    @GetMapping("/orders")
    @PreAuthorize("hasRole('USER')")
    public List<String> myOrders(@AuthenticationPrincipal Jwt jwt) {
        String userId = jwt.getSubject();     // use this as your DB key
        return List.of("order-1", "order-2");
    }

    @DeleteMapping("/orders/{id}")
    @PreAuthorize("hasRole('ADMIN') or hasAuthority('order:write')")
    public void delete(@PathVariable String id) {
        // ...
    }
}
```

### Test it

```bash
TOKEN=$(curl -s -X POST \
  http://localhost:8080/realms/myapp/protocol/openid-connect/token \
  -d "client_id=myapp-frontend" -d "username=mengheak" \
  -d "password=yourpassword" -d "grant_type=password" | jq -r .access_token)

curl -H "Authorization: Bearer $TOKEN" http://localhost:8081/api/me
```

| Response | Meaning |
|---|---|
| `200` | Working |
| `401` | Token missing, expired, malformed, or wrong issuer |
| `403` | Token is valid but the role check failed → your converter is wrong |

---

## 7. Kotlin Version of the Converter

Since you're working with Kotlin + Spring Boot, here's the same converter idiomatically:

```kotlin
package com.example.orders.config

import org.springframework.core.convert.converter.Converter
import org.springframework.security.authentication.AbstractAuthenticationToken
import org.springframework.security.core.GrantedAuthority
import org.springframework.security.core.authority.SimpleGrantedAuthority
import org.springframework.security.oauth2.jwt.Jwt
import org.springframework.security.oauth2.server.resource.authentication.JwtAuthenticationToken
import org.springframework.stereotype.Component

@Component
class KeycloakJwtConverter : Converter<Jwt, AbstractAuthenticationToken> {

    override fun convert(jwt: Jwt): AbstractAuthenticationToken {
        val authorities = realmRoles(jwt) + clientRoles(jwt, CLIENT_ID)
        return JwtAuthenticationToken(jwt, authorities, jwt.subject)
    }

    private fun realmRoles(jwt: Jwt): Set<GrantedAuthority> {
        @Suppress("UNCHECKED_CAST")
        val roles = (jwt.getClaimAsMap("realm_access")
            ?.get("roles") as? Collection<String>).orEmpty()

        return roles.map { SimpleGrantedAuthority("ROLE_$it") }.toSet()
    }

    private fun clientRoles(jwt: Jwt, clientId: String): Set<GrantedAuthority> {
        @Suppress("UNCHECKED_CAST")
        val client = jwt.getClaimAsMap("resource_access")
            ?.get(clientId) as? Map<String, Any>

        @Suppress("UNCHECKED_CAST")
        val roles = (client?.get("roles") as? Collection<String>).orEmpty()

        return roles.map { SimpleGrantedAuthority(it) }.toSet()
    }

    companion object {
        private const val CLIENT_ID = "orders-api"
    }
}
```

---

## 8. Connecting a React Frontend

Install the official JS adapter:

```bash
npm install keycloak-js
```

`src/keycloak.ts`:
```ts
import Keycloak from "keycloak-js";

export const keycloak = new Keycloak({
  url: "http://localhost:8080",
  realm: "myapp",
  clientId: "myapp-frontend",
});
```

`src/main.tsx`:
```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { keycloak } from "./keycloak";

keycloak
  .init({
    onLoad: "check-sso",              // don't force login on page load
    pkceMethod: "S256",               // PKCE — required for public clients
    silentCheckSsoRedirectUri:
      window.location.origin + "/silent-check-sso.html",
  })
  .then(() => {
    ReactDOM.createRoot(document.getElementById("root")!).render(
      <React.StrictMode>
        <App />
      </React.StrictMode>
    );
  });
```

Create `public/silent-check-sso.html`:
```html
<html><body><script>
  parent.postMessage(location.href, location.origin);
</script></body></html>
```

`src/api.ts` — attach the token and refresh it before it expires:
```ts
import { keycloak } from "./keycloak";

export async function apiFetch(path: string, options: RequestInit = {}) {
  // refresh if the token expires within 30 seconds
  await keycloak.updateToken(30).catch(() => keycloak.login());

  const res = await fetch(`http://localhost:8081${path}`, {
    ...options,
    headers: {
      ...options.headers,
      Authorization: `Bearer ${keycloak.token}`,
      "Content-Type": "application/json",
    },
  });

  if (res.status === 401) keycloak.login();
  return res;
}
```

`src/App.tsx`:
```tsx
import { useEffect, useState } from "react";
import { keycloak } from "./keycloak";
import { apiFetch } from "./api";

export default function App() {
  const [me, setMe] = useState<any>(null);

  useEffect(() => {
    if (keycloak.authenticated) {
      apiFetch("/api/me").then(r => r.json()).then(setMe);
    }
  }, []);

  if (!keycloak.authenticated) {
    return <button onClick={() => keycloak.login()}>Login</button>;
  }

  const isAdmin = keycloak.hasRealmRole("ADMIN");

  return (
    <div>
      <h1>Hello {keycloak.tokenParsed?.preferred_username}</h1>
      {isAdmin && <p>You are an admin</p>}
      <pre>{JSON.stringify(me, null, 2)}</pre>
      <button onClick={() => keycloak.logout({
        redirectUri: window.location.origin
      })}>
        Logout
      </button>
    </div>
  );
}
```

> **Security note:** `keycloak.hasRealmRole("ADMIN")` in the frontend is for **UI only** — hiding a button. Anyone can edit JavaScript in the browser. The real check must always happen in the Spring Boot API. Frontend role checks are cosmetics, backend role checks are security.

### About storing tokens in the browser

`keycloak-js` keeps tokens in JS memory by default, which is the safest option available to an SPA. Do **not** move them to `localStorage` — any XSS on your page then steals a working token.

For higher-security applications, use the **BFF (Backend for Frontend)** pattern instead: a server-side gateway holds the tokens, the browser only ever gets an HttpOnly session cookie. Spring Cloud Gateway with `TokenRelay` is the usual way to do this in the Spring ecosystem.

---

## 9. Service-to-Service Calls (No User Involved)

When `orders-api` needs to call `inventory-api` with no human in the loop, use the **client credentials grant**.

1. On the `orders-api` client, enable **Service accounts roles**
2. **Service accounts roles** tab → assign the roles that service needs
3. In Spring Boot:

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          keycloak:
            client-id: orders-api
            client-secret: ${KEYCLOAK_CLIENT_SECRET}
            authorization-grant-type: client_credentials
        provider:
          keycloak:
            issuer-uri: http://localhost:8080/realms/myapp
```

Then use a `WebClient` or `RestClient` configured with `OAuth2ClientHttpRequestInterceptor` / `ServerOAuth2AuthorizedClientExchangeFilterFunction`, which fetches and refreshes the token automatically.

Never hardcode the secret. Use an environment variable or a secrets manager.

---

## 10. Common Problems and Fixes

| Symptom | Cause | Fix |
|---|---|---|
| `403` on every endpoint despite valid token | Roles not mapped from `realm_access` | Add the `JwtAuthenticationConverter` from §6 |
| `401 Invalid issuer` | Token issued by `localhost:8080` but API resolves `keycloak:8080` (Docker) | Make the issuer string **identical** everywhere; set `KC_HOSTNAME` |
| `Invalid redirect_uri` | Redirect URI not in the client's allowed list | Add the exact URI, including port and path |
| CORS error in browser | Web origins not set on the client, or CORS not configured in Spring | Set **Web origins** in Keycloak *and* `CorsConfigurationSource` in Spring |
| `hasRole("ROLE_ADMIN")` never matches | Double prefix | Use `hasRole("ADMIN")`, keep Keycloak role named `ADMIN` |
| Token expires while user is active | No refresh logic | Call `keycloak.updateToken(30)` before each request |
| Everything breaks after container restart | Dev mode uses in-memory H2 | Use the Postgres Compose setup |
| `keycloak-spring-boot-starter` won't resolve | Adapter removed | Use `spring-boot-starter-oauth2-resource-server` |
| Users can't log in after import | Realm imported without users | Export with `--users realm_file` |

### The Docker issuer trap — explained

Inside Docker, your Spring Boot container reaches Keycloak at `http://keycloak:8080`. The browser reaches it at `http://localhost:8080`. The token's `iss` claim says one thing; your API expects the other; validation fails.

Fix by pinning the hostname so both agree:
```yaml
environment:
  KC_HOSTNAME: http://localhost:8080
  KC_HOSTNAME_BACKCHANNEL_DYNAMIC: "true"
```
and in Spring Boot, split the two URLs:
```yaml
spring.security.oauth2.resourceserver.jwt:
  issuer-uri: http://localhost:8080/realms/myapp        # must match iss claim
  jwk-set-uri: http://keycloak:8080/realms/myapp/protocol/openid-connect/certs
```

---

## 11. Realm Export and Import (Version Your Config)

Don't click through the admin console every time you set up a new machine. Export the realm and commit the JSON.

**Export:**
```bash
docker exec keycloak /opt/keycloak/bin/kc.sh export \
  --dir /tmp/export --users realm_file --realm myapp

docker cp keycloak:/tmp/export/myapp-realm.json ./keycloak/realm-export.json
```

**Import on startup** — add to your Compose service:
```yaml
    command: start-dev --import-realm
    volumes:
      - ./keycloak/realm-export.json:/opt/keycloak/data/import/realm.json
```

Now `docker compose up` gives every teammate an identical realm. Strip client secrets out of the committed file and inject them via environment variables.

---

## 12. Production Checklist

Dev mode is *not* production. Before you deploy:

- [ ] Use `start` (optimized mode), not `start-dev`
- [ ] Build an optimized image: `kc.sh build` at image-build time
- [ ] Real database (PostgreSQL), with backups
- [ ] **HTTPS everywhere** — Keycloak refuses non-local HTTP in production mode
- [ ] Set `KC_HOSTNAME` to your real public URL
- [ ] Set `KC_PROXY_HEADERS=xforwarded` if behind nginx/Traefik
- [ ] Change the admin password; create a named admin, disable the bootstrap one
- [ ] Turn on **Brute force detection** (Realm settings → Security defenses)
- [ ] Review token lifespans — access token short (5 min), refresh moderate
- [ ] Enable **Refresh token rotation** for public clients
- [ ] Require email verification and enforce a password policy
- [ ] Never use `*` in redirect URIs or web origins
- [ ] Health/metrics endpoints on a separate management port, not public
- [ ] Plan upgrades — Keycloak releases frequently and support windows are short

---

## 13. Suggested Learning Order

1. Run `start-dev`, click through the admin console, create a realm and a user
2. Get a token via curl, decode it on jwt.io, understand every claim
3. Secure one Spring Boot endpoint with `issuer-uri` only — confirm `401` without a token
4. Add the role converter, confirm `@PreAuthorize` works
5. Wire up a React frontend with `keycloak-js` and PKCE
6. Add a second backend service, call it with client credentials
7. Export the realm, import it in Docker Compose
8. Add social login (Google) via Identity Providers
9. Read about the BFF pattern and decide if your app needs it

---

## 14. Official Resources

- Documentation: https://www.keycloak.org/documentation
- Securing Applications guide: https://www.keycloak.org/guides#securing-apps
- Server admin guide: https://www.keycloak.org/guides#server
- Quickstarts repo: https://github.com/keycloak/keycloak-quickstarts
- Spring Security OAuth2 Resource Server docs: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html
- Container image: https://quay.io/repository/keycloak/keycloak

**Current versions (September 2026):** Keycloak server `26.7.3`, `keycloak-js` `26.2.4`, `keycloak-admin-client` `26.0.12`
