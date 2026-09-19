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
