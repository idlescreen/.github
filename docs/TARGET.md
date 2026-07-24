# IdleScreen target architecture

## User installs

| Package | Repo |
|---------|------|
| **idle-cosmic** | idle-cosmic |
| **idle-tui** | idle-tui |
| **idle-studio** | idle-studio |
| **idle-windows** | idle-windows |
| **idle-steam** | idle-steam |

## Engine (dependency, not a product)

| | |
|--|--|
| Repo | **[idle](https://github.com/idlescreen/idle)** (was idle-core) |
| Daemon package | `idle-daemon` |
| CLI package | `idle-cli` → command **`idle`** |
| Savers | `idle-savers` / `idle-saver-*` |

## Keep short

`packages` · `brand` · `render`

## Former names

| Old | New |
|-----|-----|
| idle-core | **idle** (repo) |
| package idlescreen / idle | **idle-daemon** |
| app-cosmic | **idle-cosmic** |
| saver-* | **idle-saver-*** |
