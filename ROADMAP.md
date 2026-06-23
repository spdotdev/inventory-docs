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
- [ ] **Keep `specs/` authoritative** — when the schema or API changes during
  implementation, update `specs/data-model.md` / `specs/api-contract.md` first, then the
  app repos follow. Don't let code drift ahead of the contract silently.
- [ ] **Resolve Q-3 (realtime)** — confirm pull-to-refresh stays the answer, or schedule
  WebSockets/Reverb. Currently recommended: pull-to-refresh.

### NICE TO HAVE
- [ ] **Add an ERD diagram** to `specs/data-model.md` (visual of the household → location →
  shelf → product tree) once the schema is implemented and stable.
