# IdleScreen repository catalog

Public repositories under [github.com/idlescreen](https://github.com/idlescreen).  
Maintained layout; see [TARGET.md](TARGET.md) for architecture and naming rules.

## Product entry points

| Repo | Package / binary | Notes |
|------|------------------|--------|
| [app-cosmic](https://github.com/idlescreen/app-cosmic) | **`app-cosmic`** | COSMIC one-shot: applet + requires daemon + all savers |
| [idle-core](https://github.com/idlescreen/idle-core) | **`idlescreen`**, `idlescreen-cli` | Daemon + CLI + plugin API |
| [packages](https://github.com/idlescreen/packages) | — | APT/DNF host |

## Apps (`app-*`)

| Repo | Status |
|------|--------|
| [app-cosmic](https://github.com/idlescreen/app-cosmic) | Active product |
| [app-tui](https://github.com/idlescreen/app-tui) | Active (optional controller) |
| [app-studio](https://github.com/idlescreen/app-studio) | Active (offline director) |
| [app-kde](https://github.com/idlescreen/app-kde) | Stub |
| [app-windows](https://github.com/idlescreen/app-windows) | Stub |
| [app-steam](https://github.com/idlescreen/app-steam) | Stub |

## Engines & business

| Repo | Role |
|------|------|
| [idle-core](https://github.com/idlescreen/idle-core) | Live host |
| [render](https://github.com/idlescreen/render) | Offline export |
| [idle-pro](https://github.com/idlescreen/idle-pro) | Pro strategy |
| [brand](https://github.com/idlescreen/brand) | Visual identity |
| [packages](https://github.com/idlescreen/packages) | Package index |
| [.github](https://github.com/idlescreen/.github) | Org profile + docs |

## Effects (`saver-*`)

| Repo |
|------|
| [saver-beams](https://github.com/idlescreen/saver-beams) |
| [saver-bursts](https://github.com/idlescreen/saver-bursts) |
| [saver-chaos](https://github.com/idlescreen/saver-chaos) |
| [saver-cosmos](https://github.com/idlescreen/saver-cosmos) |
| [saver-glyphs](https://github.com/idlescreen/saver-glyphs) |
| [saver-gnats](https://github.com/idlescreen/saver-gnats) |
| [saver-hearth](https://github.com/idlescreen/saver-hearth) |
| [saver-radar](https://github.com/idlescreen/saver-radar) |
| [saver-ripple](https://github.com/idlescreen/saver-ripple) |
| [saver-storm](https://github.com/idlescreen/saver-storm) |

## Former names (GitHub redirects)

| Old | New |
|-----|-----|
| idle-tui | app-tui |
| idle-studio | app-studio |
| idle-render | render |
| idle-brand | brand |
| idlescreen-applet / idle-cosmic | app-cosmic |

## Pinning (suggested)

On the org profile, pin:

1. **idle-core**  
2. **app-cosmic**  
3. **packages**  
4. **render**  
5. **app-tui**  
6. **brand**  

*(Pins are set in GitHub → Organization profile if not already.)*
