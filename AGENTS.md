# poolos-website

Static marketing website for **https://mypoolos.app** (the PoolOS iOS app). See `README.md` and `HOSTGATOR.md` for the edit/ship/deploy workflow.

## Cursor Cloud specific instructions

### What this is
- A **pure static site**: hand-authored, self-contained `*.html` pages at the repo root (each with inline CSS/JS), one logo asset, plus a single dynamic file `support.php` (the contact form).
- **No package manager, no build step, no database, no lockfiles.** There is nothing to compile or bundle; files are served as-is.

### Running it (dev)
- The only runtime dependency is **PHP** (used to serve pages and execute `support.php`). It is installed in the environment.
- Serve the whole site from the repo root with PHP's built-in server:
  - `php -S 0.0.0.0:8000 -t /workspace`
  - Then open `http://localhost:8000/index.html` (landing page) or `http://localhost:8000/support.php` (contact form).
- A plain static server (e.g. `python3 -m http.server`) also serves the `.html` pages, but it will **not execute** `support.php` — it would return the raw PHP source. Use `php -S` for full coverage.

### Testing the support form (`support.php`)
- The form validates on `POST`; on success it calls PHP `mail()` to send to `info@mypoolos.app`.
- The VM has **no mail transport**, so `mail()` returns `false` and the page shows the red error alert even for valid input — this is expected, not a bug. Validation/render logic still runs correctly.
- To exercise the green **success** path locally, point PHP at a throwaway sendmail catcher so `mail()` returns true, e.g.:
  - create a script that reads stdin and `exit 0` (e.g. `/tmp/fake-sendmail.sh`), then run `php -d sendmail_path="/tmp/fake-sendmail.sh" -S 0.0.0.0:8000 -t /workspace`.

### Lint / test / build
- There is **no lint, no automated test suite, and no build** in this repo. "Build" == serving the files. Validate changes by loading the affected page(s) in the browser via the PHP dev server.

### Deploy (context only — not for local runs)
- Production deploy is GitHub Actions → SSH into HostGator → `git pull --ff-only origin main` (`.github/workflows/deploy.yml`). No build runs on deploy.
