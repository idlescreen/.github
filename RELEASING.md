# Release conventions

Every repo in the org carries its own independent version and release
number — there is no org-wide version.

## Product repos — SemVer

`runtime`, `cli`, `tui`, `studio`, `savers`, `cosmic`, `packages`,
`idlescreen` version their released crate(s) in `Cargo.toml` using
semantic versioning. A release is cut by pushing a `v<semver>` tag;
`.github/workflows/release.yml` builds the packages, signs them with
cosign, creates the GitHub release, and dispatches the packages-repo
import.

- Tag follows the released crate's `Cargo.toml` version
  (`savers` tags follow `meta/Cargo.toml`; saver members share
  `workspace.package.version`).
- `packages` is tag-only: it hosts the apt/rpm pool, so its tags mark
  repository state and carry no GitHub release.

## Content repos — CalVer

`.github` (this repo) and `idlescreen.github.io` have no build
artifacts and no API to break, so they use calendar versioning:

- `VERSION` file in the repo root: `YYYY.M.N`
  - `YYYY.M` — year and month of the release
  - `N` — Nth release within that month, starting at 1
- Tag: `v$(cat VERSION)`, e.g. `v2026.9.1`
- Each tag gets a GitHub release whose notes summarize what changed.

To cut a content release:

```sh
echo "YYYY.M.N" > VERSION      # bump N, or reset to 1 on a new month
git add VERSION && git commit -m "release: vYYYY.M.N"
git push && git tag "v$(cat VERSION)" && git push origin "v$(cat VERSION)"
gh release create "v$(cat VERSION)" --title "v$(cat VERSION)" --notes "..."
```

## Coordinated hardening commits

The repos in the org share several contracts that don't fit cleanly into
a single per-repo release tag:

- `aegis.yml` / `boneyard.yml` / `necrometer.yml` / `proven.yml` /
  `snip.yml` use the same pinned `studio2201/studio2201` and
  `necrometer-dev/necrometer-action` SHAs across all 10 subrepos.
  Bump one, bump the rest in the same window.
- `deny.toml` is a duplicated workspace-mirror under
  `cli/tui/studio/cosmic`; if `runtime/deny.toml` tightens, the four
  copies should land in the same window.
- `runtime/` path-deps flow into `cli/`, `tui/`, `cosmic/`, `savers/`,
  and `studio/engine`. Bootstrap shims, CI guards, and
  `Cargo.toml`'s `version` pin in the consumer all move together.
- The `packages/install.sh` trust model (fingerprint-verified
  channel, no hand-placed `.list` or `.repo`) drives changes in
  `tui/src/cosmic.rs`, `cosmic/src/main.rs`, and `packages/index.html`
  in the same window.

For these commits, prefer a small number of focused PRs per repo with
matching commit-message prefixes, all landed within a single CI window.
No new tag is required for coordinated hardening — the per-repo
release tag moves only when a `Cargo.toml` version or `VERSION` file
actually changes.
