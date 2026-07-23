# Transfer plan: Crateria → IdleScreen

**Target org:** [idlescreen](https://github.com/idlescreen)  
**Source org:** [crateria](https://github.com/crateria)  
**Status:** Preparation only — execute when explicitly approved.

## Decision summary

| Repo | Transfer? | Rationale |
|------|-----------|-----------|
| **trance** | **YES — required** | Core IdleScreen application (daemon, CLI, TUI, applet, trance-api) |
| **trance-plugins** | **YES — required** | Official effects; CI clones sibling `trance`; packages ship `trance-plugin-*` |
| **packages** | **YES — required** | APT/DNF index + GitHub Pages install story is product distribution |
| **brand** | **Optional → recommend YES (copy or transfer)** | Icons/headers used in product READMEs; either transfer, or fork/copy assets into IdleScreen and leave Crateria brand behind |
| **crateria.github.io** | **NO (or replace)** | Crateria studio site. IdleScreen should get **idlescreen.github.io** (new) with IdleScreen docs, not the old multi-product Crateria hub |
| **crateria/.github** | **NO** | Stays as Crateria org profile; update to point at IdleScreen after transfer |
| **studio2201/** | **NO** | Unrelated web/containers org |

### Not required / do not invent

- Unraid templates (desktop product, not Unraid CA)
- morphball / rusting (deleted; do not restore)
- Separate packages repo per plugin (later decision)

---

## Recommended end state

```text
idlescreen/
  .github                 # org profile (this repo)
  idlescreen              # transferred from crateria/trance (rename repo optional, later)
  idlescreen-plugins      # transferred from crateria/trance-plugins (rename optional)
  packages                # transferred from crateria/packages
  brand                   # transferred or fresh IdleScreen brand kit
  idlescreen.github.io    # NEW docs/install site (not a transfer of crateria.github.io)

crateria/
  .github                 # "IdleScreen moved to github.com/idlescreen"
  crateria.github.io      # stub or archive
  (empty of product code)
```

**Rename timing:** transfer first with current names (`trance`, `trance-plugins`), then rename repos to `idlescreen` / `idlescreen-plugins` when ready. Package names (`trance`, `trance-plugin-beams`, …) can lag GitHub names.

---

## Transfer order (execute in this order)

1. **Org prep (IdleScreen)** — profile, description, Actions permissions, default branch policy  
2. **packages** — so Pages/GPG secrets path is clear before product release hooks change  
3. **trance**  
4. **trance-plugins** (depends on trance URL in CI)  
5. **brand** (if moving)  
6. **Create idlescreen.github.io** — new content, install URLs  
7. **Post-transfer string/CI/secrets pass**  
8. **Crateria cleanup** — profile becomes a pointer  

GitHub keeps redirects from `crateria/trance` → new location for a long time; still update canonical URLs.

---

## Secrets & integrations to recreate on IdleScreen

| Secret / config | Where today | Where after |
|-----------------|-------------|-------------|
| `CRATERIA_PACKAGES_DISPATCH_TOKEN` | Product repos | Rename to `IDLESCREEN_PACKAGES_DISPATCH_TOKEN` or keep name temporarily; must dispatch to **idlescreen/packages** |
| `GPG_PRIVATE_KEY` (+ passphrase/name) | `crateria/packages` | **idlescreen/packages** (required for signed repos) |
| GitHub Pages | `crateria.github.io/packages` | Prefer **idlescreen.github.io/packages** (new hostname in apt sources) — plan a migration note for users |
| Actions allowlist | org-level on crateria | Mirror on idlescreen (GitHub-owned + verified + Swatinem if used) |
| CodeQL / security | per-repo | re-enable on transferred repos |
| Cosign / release | product workflows | paths update to new org |

**Hard migration note:** APT list files currently say:

```text
https://crateria.github.io/packages/...
```

Moving packages to IdleScreen means either:

- **A.** Keep serving packages from Crateria Pages forever (packages stays on Crateria — not ideal), or  
- **B.** Move packages to IdleScreen and publish new apt/dnf URLs + document upgrade path, or  
- **C.** Dual-publish for one transition period  

Recommend **B** with a short dual-host period if anyone already installed.

---

## Code / CI strings to update after transfer

### trance-plugins CI (today)

```text
git clone https://github.com/crateria/trance.git ../trance
```

→ `https://github.com/idlescreen/trance.git` (or `/idlescreen.git` after rename)

### trance release.yml

```text
repos/crateria/packages/dispatches
```

→ `repos/idlescreen/packages/dispatches`

### READMEs / badges / security links

All `github.com/crateria/trance*` → `github.com/idlescreen/...`

### D-Bus (do not change in v1 unless major)

```text
/io/github/crateria/trance
```

Treat as ABI; optional later `io.github.idlescreen...` with major version.

### LICENSE appendix

Still may say `Copyright 2026 studio2201` in places — update to IdleScreen / your legal name when branding.

### Cargo / deb names

Optional later rename from `trance*` → `idlescreen*`. Not required for org transfer.

---

## Pre-flight checklist (before `gh` transfer)

- [ ] IdleScreen org: description, avatar, blog URL  
- [ ] IdleScreen Actions: selected actions allow GitHub-owned (+ rust-cache if needed)  
- [ ] IdleScreen: you remain admin; billing email OK  
- [ ] Note all secrets currently on crateria/packages and product repos  
- [ ] Decide package host URL strategy (A/B/C above)  
- [ ] Decide immediate repo renames vs transfer-as-trance  
- [ ] No open critical CI reds you care about (or accept re-run after)  
- [ ] Local clones: note remotes will need `git remote set-url`

---

## Transfer commands (do not run until approved)

```bash
# Example: transfer as-is names
gh api --method POST repos/crateria/packages/transfer -f new_owner=idlescreen
gh api --method POST repos/crateria/trance/transfer -f new_owner=idlescreen
gh api --method POST repos/crateria/trance-plugins/transfer -f new_owner=idlescreen
# optional:
gh api --method POST repos/crateria/brand/transfer -f new_owner=idlescreen
```

After transfer, optional renames:

```bash
gh repo rename idlescreen --repo idlescreen/trance
gh repo rename idlescreen-plugins --repo idlescreen/trance-plugins
```

---

## Post-transfer verification

- [ ] `github.com/idlescreen/trance` loads; old URL redirects  
- [ ] Plugins CI clones new sibling URL and is green  
- [ ] Packages Pages deploys on **idlescreen**  
- [ ] Tag a dry-run or re-run release dispatch to packages  
- [ ] Install doc URLs match live Pages  
- [ ] Crateria profile points to IdleScreen  

---

## Crateria after move

Leave:

- `crateria/.github` — short “products moved to idlescreen”  
- Optionally empty or archived `crateria.github.io`  

Do not leave **packages** on Crateria if IdleScreen is the product brand — that splits the install story.
