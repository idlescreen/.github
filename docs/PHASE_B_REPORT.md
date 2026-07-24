# Phase B org hardening report

Contract: Apache-2.0 Rust, max 250 lines per `.rs`, no production unwrap/expect,
proptest for protocol logic, declarative commits on barriers.

## Inventory audit (snapshot)

| Area | Result |
|------|--------|
| Files over 250 lines | None found across audited repos |
| Production unwrap/expect | None outside test modules (audited product crates) |
| proptest present | idle-core, packages, saver-beams, render, app-studio |
| New product tools | render, app-studio hardened this cycle |

## Hardening applied this cycle

### render (v0.2.1)

- Path traversal denial on output/plugin/audio (`paths::deny_parent_dirs`)
- proptest for duration parse and segment plan invariants
- Blue Ocean README, AGENT.md, HARDENING.md
- Alpine Dockerfile stub, Unraid XML template
- CI already present

### app-studio (v0.2.1)

- proptest on CLI arg mapping
- Blue Ocean README, AGENT.md
- Dockerfile, Unraid XML
- TUI remains production-safe (no unwrap in hot path)

### packages

- Existing proptest and path safety retained
- Metapackage deb already in pool from prior work

### idle-core / savers

- Prior seed API and sandbox skip retained
- Savers honor env seed

## Residual risks / follow-ups

1. Full GHCR multi-stage images need idle-core monorepo-style context (path deps).
2. packages Pages signing still requires maintainer GPG secrets for production apt.
3. 3:1 test-to-code is aspirational for large savers; newest crates improved coverage.
4. cargo-audit / cargo-udeps not run org-wide in this environment if tools absent.

## Release tags

See render and app-studio `v0.2.1` tags from this Phase B release edge.
