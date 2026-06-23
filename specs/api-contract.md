# API Contract — Inventory (canonical)

> Single source of truth for the HTTP contract between `inventory-android` (client)
> and `inventory-laravel` (server). Versioned from day one; **backward compatible** —
> a shipped Android build updates on the user's schedule, not ours.

## Base

- **Host:** `https://inventory.{domain}` (host-based routing; the package answers on
  `config('inventory.domain')`).
- **Prefix:** `/api/v1`
- **Format:** REST + JSON.
- **Auth:** Laravel **Sanctum** bearer tokens. Email/password **and** Google sign-in
  both resolve to a Sanctum token the Android client stores and sends as
  `Authorization: Bearer <token>`.
- **Middleware chain:** `auth:sanctum` → `household.member({household})` → resource policy.

## Auth

```
POST   /api/v1/auth/register   { name, email, password }      -> { user, token }
POST   /api/v1/auth/login      { email, password }            -> { user, token }
POST   /api/v1/auth/google     { id_token }                   -> { user, token }
POST   /api/v1/auth/logout                                    -> revoke current token
```

**Google flow:** Android performs native Google Sign-In, obtains a Google **ID
token**, and posts it to `/auth/google`. The server verifies the ID token
(Socialite stateless / Google token validation), finds-or-creates the
`inventory_users` row (matching on `google_id` then `email`), and returns a Sanctum
token. Google-only users have a null `password`.

## Households & membership

```
GET    /api/v1/households                                     -> households the caller belongs to
POST   /api/v1/households                  { name }           -> create (creator auto-joins)
GET    /api/v1/households/{household}/invite                  -> { code, link }
                                                                 (client renders QR from link)
POST   /api/v1/households/join             { code }           -> join by code
DELETE /api/v1/households/{household}/leave                   -> leave self
GET    /api/v1/households/{household}/search?q=               -> products + location path
                                                                 (location › shelf) + quantity
```

## Resources (all under `/api/v1/households/{household}`)

```
       /locations[/{location}]                       CRUD   (type: freezer|fridge|pantry|other)
       /locations/{location}/shelves[/{shelf}]       CRUD
       /shelves/{shelf}/products[/{product}]         CRUD

POST   /products/{product}/add     { amount }        -> increment quantity
POST   /products/{product}/remove  { amount }        -> decrement (floor 0)
POST   /products/{product}/move    { shelf_id }      -> relocate within the household
```

## Conventions

- Form Requests validate input at the boundary; API Resources shape responses.
- Route-model binding scoped to the household; a resource that doesn't belong to the
  path household returns 404 (not 403 — don't leak existence).
- Concurrency is **last-write-wins** — no version / If-Match / optimistic-lock headers.
- Errors: standard Laravel JSON error envelope; 401 unauth, 403 non-member,
  404 not-found/out-of-tenant, 422 validation.
