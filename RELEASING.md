# Release conventions

Every repo in the org carries its own version and release tag (`v4.4.2`).

## Product repos — SemVer

`idlescreen`, `packages`, and `studio` version their released crate(s) in
`Cargo.toml` using semantic versioning. A release is cut by pushing a
`v<semver>` tag; `.github/workflows/release.yml` builds the packages, signs
them with cosign, creates the GitHub release, and dispatches the packages-repo
import.

- Tag follows the released crate's `Cargo.toml` version (`crates/idlescreen/Cargo.toml` in `idlescreen`).
- `packages` hosts the signed APT/RPM pools and installer scripts; its tags mark repository state.

## Content repos

`.github` (this repo) and `idlescreen.github.io` track documentation, release workflows, and the public site (`VERSION` file in the repo root).

## Coordinated hardening commits

The 5 active repositories in the `@idlescreen` organization (`idlescreen`, `packages`, `idlescreen.github.io`, `studio`, `.github`) share several contracts:

- `AGENTS.md` and `qa/page_score_check.sh` enforce the single-page engineering standard across all repositories.
- `packages/install.sh` trust model (fingerprint-verified channel, SHA-256 module integrity pins) and `idlescreen.github.io` landing page copy stay synchronized on release tags.
