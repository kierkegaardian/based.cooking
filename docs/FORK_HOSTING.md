# Hosting Your Own based.cooking Fork

This setup keeps the same model: recipe submissions come in as pull requests, and your merge approval triggers deployment.

## 1. Fork and basic config

1. Fork this repo on GitHub.
2. In your fork, update `config.toml`:
   - `baseURL = 'https://cooking.geoffsmiscellany.com/'`
   - `title = 'Your Site Title'`
3. Keep recipe rules and structure unchanged:
   - recipe files in `content/`
   - images in `static/pix/` as small `.webp` files
   - markdown format based on `example.md`

## 2. Workflows included

- `.github/workflows/validate.yml`
  - Builds the Hugo site on pull requests and pushes.
  - Fails fast on broken content/front matter so bad submissions do not merge.
- `.github/workflows/deploy-fork.yml`
  - For fork owners (non-`LukeSmithxyz`) only.
  - Builds in CI and deploys the `public/` output over SSH using `rsync`.
- `.github/workflows/upload.yml`
  - Legacy upstream flow.
  - Now guarded to only run on the original owner repo.

## 3. GitHub secrets for deploy

Set these in your fork: `Settings -> Secrets and variables -> Actions`.

- `DEPLOY_SSH_KEY`: private SSH key for your deploy user
- `DEPLOY_KNOWN_HOSTS`: output of `ssh-keyscan -H your-server-host`
- `DEPLOY_HOST`: optional; defaults to `209.182.197.210`
- `DEPLOY_PORT`: optional; defaults to `2222`
- `DEPLOY_USER`: optional; defaults to `virtus12`
- `DEPLOY_PATH`: optional absolute docroot; defaults to `/home/<DEPLOY_USER>/cooking.geoffsmiscellany.com`

## 4. WebHostingHub values for your setup

The workflow already defaults to your current hosting values:

- `DEPLOY_HOST=209.182.197.210`
- `DEPLOY_PORT=2222`
- `DEPLOY_USER=virtus12`
- `DEPLOY_PATH=/home/virtus12/cooking.geoffsmiscellany.com`

Set known hosts with:

```bash
ssh-keyscan -p 2222 -H 209.182.197.210
```

Confirm your real docroot with:

```bash
ssh -p 2222 virtus12@209.182.197.210 "ls -ld /home/virtus12/cooking.geoffsmiscellany.com /home/virtus12/public_html 2>/dev/null"
```

If cPanel uses a different docroot, set `DEPLOY_PATH` to that absolute path.

## 5. WebHostingHub prep (cPanel)

1. In cPanel, create the subdomain `cooking.geoffsmiscellany.com`.
2. Confirm the subdomain document root.
   - Current configured value on your account: `/home/virtus12/cooking.geoffsmiscellany.com`
3. Enable SSH for the hosting account and install your deploy public key.
4. Point DNS for `cooking.geoffsmiscellany.com` to your WebHostingHub server.

## 6. TLS on WebHostingHub

Use cPanel SSL/TLS (AutoSSL) for `cooking.geoffsmiscellany.com`.

After DNS propagates, verify:

- `https://cooking.geoffsmiscellany.com/` serves a valid certificate.

## 7. Keep the PR approval gate

In your fork GitHub settings:

1. Protect your default branch (`master` or `main`).
2. Require pull requests before merge.
3. Require at least 1 approval.
4. Require status checks to pass (select `Validate Hugo Site`).
5. Disable force pushes and direct pushes for non-admins.

Optional `CODEOWNERS` in `.github/CODEOWNERS`:

```text
content/* @your-github-username
static/pix/* @your-github-username
```

This forces your review on recipe and image changes.
