# Agent Directives

These rules apply to every task unless explicitly overridden.

## What This Repo Is

A single Makefile that automates AUR (Arch User Repository) package maintenance: checking for upstream updates, bumping versions, building packages, and publishing to AUR.

**Not a software project.** This is a build/publish automation tool. There is no application code, tests, or CI.

## Critical Context

- **Requires `PKGBUILD`** in the same directory — the Makefile will fail without it. The Makefile extracts `url=` and `pkgver=` from PKGBUILD.
- **Arch Linux only** — depends on `makepkg`, `updpkgsums`, and other Arch-specific tools.
- **No tests exist** — verification is manual (`make check`, `make build`).

## Commands

| Command | What it does | When to use |
|---------|--------------|-------------|
| `make check` | Compare local pkgver vs upstream | Before any work — verify current state |
| `make bump` | Update PKGBUILD to latest upstream | Version update workflow |
| `make build` | Build package with makepkg | After bump, before publish |
| `make publish` | Commit + push to AUR | After successful build |
| `make release` | bump → build → publish (stops on failure) | Full release workflow |
| `make bump-dry` | Preview bump without changes | Safe verification |
| `make release-dry` | Preview full release | Safe verification |
| `make clean` | Remove build artifacts | After failed builds, before fresh start |

## Workflow

Standard release: `make release` (or step-by-step: `make bump` → `make build` → `make publish`).

Dry-run first: `make release-dry` to preview what would happen.

## Variables

- `AUR_REMOTE` (default: `label`) — git remote name for AUR
- `AUR_BRANCH` (default: `master`) — git branch to push to AUR

Override: `make release AUR_REMOTE=origin AUR_BRANCH=main`

## Gotchas

- `.SRCINFO` must be regenerated after any PKGBUILD change — `make bump` handles this automatically.
- `make publish` verifies `.SRCINFO` is in sync before committing — out-of-sync will fail.
- `.DELETE_ON_ERROR` is set — if a command fails, Make deletes the target.
- Shell is bash, not sh.
