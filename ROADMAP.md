# Roadmap

> Forward-looking **commitments** only — documentation work being tracked now. Companion
> file [`BACKLOG.md`](BACKLOG.md) holds **wishes** (Ideas) and **history**. This repo is the
> shared source of truth for the Inventory product; per-repo engineering roadmaps live in
> [`inventory-laravel`](https://github.com/spdotdev/inventory-laravel) and
> [`inventory-android`](https://github.com/spdotdev/inventory-android).

Markers: 🟡 TBD · 🔲 TODO · ✅ done (shipped work moves to `BACKLOG.md`).

---

## Active TODOs

### SPEC HYGIENE
- [x] **Keep `specs/` authoritative** *(ongoing)* — reconciled to the implemented backend
  2026-06-23: `api-contract.md` auth section now reflects the real Google verifier
  (tokeninfo, fail-closed, `email_verified`/`aud`) rather than "Socialite", and the contract
  is marked implemented. Keep updating specs-first as the API evolves.
- [ ] **Resolve Q-3 (realtime)** — confirm pull-to-refresh stays the answer, or schedule
  WebSockets/Reverb. Currently recommended: pull-to-refresh. *(Needs your call.)*
