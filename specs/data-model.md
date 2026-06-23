# Data Model — Inventory (canonical)

> Single source of truth for the schema. Both `inventory-laravel` (owns/migrates it)
> and `inventory-android` (consumes it via the API) depend on this file.

## Naming & conventions

- The product is **Inventory** — a general-purpose, multi-household stock manager.
  Freezer / fridge / pantry are *examples* of what it manages, not its identity.
- `inventory-laravel` ships as a **Composer package mounted into a host Laravel app**
  (sd-admin). To avoid colliding with the host app's own tables, **every
  package-owned table is prefixed `inventory_`**.
- Engine: **MySQL** (the host app's default connection). All FKs `ON DELETE CASCADE`
  down the location → shelf → product tree. No soft deletes. `quantity` floors at 0.

## Tables

```
inventory_users
  id, name, email (unique), password (nullable — Google-only users have none),
  google_id (nullable, unique), avatar_url (nullable),
  email_verified_at (nullable), remember_token, created_at, updated_at

inventory_households
  id, name, join_code (unique), created_at, updated_at
  -- join_code drives the invite link + QR (D-026)

inventory_household_user                      -- membership pivot
  household_id, user_id, joined_at
  -- composite PK; NO role column (all members equal, D-017)

inventory_storage_locations
  id, household_id (FK CASCADE), name,
  type ENUM(freezer|fridge|pantry|other), created_at, updated_at

inventory_shelves
  id, location_id (FK CASCADE), name, position, created_at, updated_at

inventory_products
  id, shelf_id (FK CASCADE), name, quantity (>= 0),
  created_at, updated_at
  -- quantity 0 = out of stock; row retained for easy re-add

personal_access_tokens (Sanctum)             -- shared/global; tokenable_type
                                                 distinguishes the inventory User model
```

## Indexes (performance)

- `inventory_storage_locations.household_id`
- `inventory_shelves.location_id`
- `inventory_products.shelf_id`
- `inventory_products.name` — add a **FULLTEXT** index if/when `LIKE` search
  feels slow. Not needed at expected scale (dozens–thousands of products/household).

## Tenancy rule

Everything belongs to a **household**, never directly to a user. Membership is
enforced at the API boundary (`household.member` middleware) *before* any resource
access. Child queries are scoped by the validated `household_id`.

## Relationship diagram

```
inventory_users >──< inventory_households          (via inventory_household_user)
inventory_households ──< inventory_storage_locations ──< inventory_shelves ──< inventory_products
```
