# HostGator production checkout (`mypoolos.app`)

GitHub Actions deploys this repo by **SSH** into HostGator and running **`git pull`** in a single **canonical directory**. That directory **must** be a real Git working tree — not a one-time copy of HTML files.

## Canonical paths

| Item | Value |
|------|--------|
| cPanel **Document root** for `mypoolos.app` | `~/public_html/mypoolos_app` |
| Path used by Actions (this account) | `/home1/nhqxwzte/public_html/mypoolos_app` |
| Workflow | [`.github/workflows/deploy.yml`](./.github/workflows/deploy.yml) |

If your cPanel username is not `nhqxwzte`, change the Actions `cd` path and the examples below to match **your** home directory layout — but keep **one** folder name (`mypoolos_app`) everywhere so doc root, disk, and workflow agree.

## Requirements

1. **`public_html/mypoolos_app` contains a `.git` directory** — clone of `MyPoolOS/poolos-website`, branch **`main`**.
2. The server can **`git fetch` / `git pull`** from GitHub (SSH deploy key or HTTPS credentials on the HostGator side).
3. **Domains → `mypoolos.app` → document root** points at **`public_html/mypoolos_app`**, not a sibling folder.

## One-time setup (SSH / Terminal as HostGator user)

**Backup first** if the folder has anything you need.

```bash
cd ~/public_html
# If mypoolos_app is only a manual copy with no .git, remove or rename it after backup:
# mv mypoolos_app "mypoolos_app.backup-$(date +%Y%m%d)"
git clone git@github.com:MyPoolOS/poolos-website.git mypoolos_app
cd mypoolos_app
git checkout main
git pull origin main
```

If GitHub SSH is not configured from the server, use HTTPS:

```bash
cd ~/public_html
git clone https://github.com/MyPoolOS/poolos-website.git mypoolos_app
cd mypoolos_app && git checkout main
```

Then in **cPanel → Domains**, set **`mypoolos.app`** document root to **`public_html/mypoolos_app`**.

## Migrating from `website_2da8ad2a` (legacy)

If the **old** path is still the real clone (has `.git` and already tracks `main`):

```bash
cd ~/public_html
# Preserve any non-git work in mypoolos_app before overwriting:
# mv mypoolos_app "mypoolos_app.copy-$(date +%Y%m%d)"
mv website_2da8ad2a mypoolos_app
```

Point cPanel document root at **`public_html/mypoolos_app`**, then:

```bash
cd ~/public_html/mypoolos_app && git pull --ff-only origin main
```

## Verify after a deploy

Push **`main`** from GitHub, wait for Actions, then on the server:

```bash
cd ~/public_html/mypoolos_app && git rev-parse --is-inside-work-tree && git log -1 --oneline
```

You should see `clean` or normal status and the latest commit SHA from GitHub.

## Local editing

Develop in the monorepo submodule **`PoolOS/poolos-website/`** on branch **`main`** (see **`Marketing/BRAND.md`** in the iOS monorepo). **Never** treat a HostGator FTP upload as the source of truth — **GitHub `main`** is.
