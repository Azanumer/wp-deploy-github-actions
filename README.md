# WordPress Deploy + CI Workflows

Copy-paste GitHub Actions workflows for WordPress projects. No framework,
no SaaS — just two YAML files you drop into `.github/workflows/`.

## Workflows

| File | Purpose |
|---|---|
| `workflows/deploy-wordpress.yml` | Push-to-deploy over SSH: `git fetch` + `reset --hard` on the server, clean untracked files (uploads/`.env` kept), `wp cache flush` + `rewrite flush` when WP-CLI exists |
| `workflows/theme-ci.yml` | CI for themes/plugins: PHP syntax check on all files, PHPCS (WordPress standard) when configured, release-zip build + artifact on `main` |

## Setup (deploy workflow)

1. Copy `workflows/deploy-wordpress.yml` to `.github/workflows/` in your
   WordPress project.
2. On the server, make the site a git repo with `origin/main` checked out
   (bare repo + work tree, or a normal clone).
3. Add these **repository secrets** (Settings → Secrets and variables →
   Actions):

| Secret | Example |
|---|---|
| `SSH_HOST` | `89.117.12.24` |
| `SSH_USER` | `deploy` |
| `SSH_KEY` | private key (no passphrase), in the user's `authorized_keys` |
| `SSH_PORT` | `22` |
| `DEPLOY_PATH` | `/var/www/example.com/public_html` |
| `WP_CLI` | (optional) `/usr/local/bin/wp` |

## Safety notes

- `git clean -fd` removes untracked files except `wp-content/uploads` and
  `.env` — add more `--exclude=` entries for anything else the server keeps
  outside git (e.g. `wp-config.php` if it is not committed).
- Run the workflow once with `workflow_dispatch` and watch the log before
  trusting automatic pushes.
- Keep the deploy key scoped: a dedicated `deploy` user with write access
  only to the site path, and consider branch protection on `main`.
- The CI workflow uses pinned, well-known actions
  (`actions/checkout@v4`, `shivammathur/setup-php@v2`,
  `actions/upload-artifact@v4`, `appleboy/ssh-action@v1`).

MIT licensed.
