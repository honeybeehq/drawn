# Drawn

Author your own custom icon sets with AI agents — and keep them coherent.
Named for *drawn comb*: the built-out foundation bees fill with work.

Drawn is a local-first, SQLite-backed Electron app connected to
[Honeybee](../honeybee)/hive for agent execution. You define a **style**
(a typed spec, not a prompt), fan out generation across a set, and Drawn's
lint + critic loop keeps every icon in line. Export as SVG, PNG, icon
fonts, and component code.

See [docs/SPEC.md](docs/SPEC.md) for the full product and technical spec.

## Layout

- `apps/desktop` — Electron app (planned)
- `packages/core` — `@drawn/core`: domain model, StyleSpec schema, SVG lint rules (planned)
- `docs/` — specs and design notes

## Status

Pre-code. The spec is written; scaffolding only.

## License

Planned: FSL-1.1 (Functional Source License — commercial use reserved,
converts to Apache-2.0 after two years). Not yet applied; to be confirmed
before first public release. Icons you generate with Drawn are yours,
entirely unencumbered.
