# Self-host the open-source Kortix stack

Kortix is the open-source AI Operating System, and a self-hosted instance is the full platform on a server you control: one Docker Compose stack that the `kortix self-host` CLI installs, configures, and updates. The server runs the platform, and each session runs on a cloud sandbox provider. The instance must be reachable from the internet so those sandboxes can call back.

## Requirements

- A Linux server with a public IP. The CLI runs on Linux and macOS, on x86_64 and arm64.
- DNS: A or AAAA records for `<domain>` and `api.<domain>`, both pointed at the server before the first start. Add a wildcard `*.p.<domain>` for sandbox previews.
- Ports 80 and 443 open inbound.
- A sandbox provider key for Platinum or the legacy Daytona fallback. Without one, sessions do not start.
- A model provider key. You add it in the web app after the first sign-in.

## Install the open-source stack

On a server that already has Docker and the Compose plugin:

```bash
curl -fsSL https://kortix.com/install | bash
kortix self-host init --domain kortix.example.com --admin-email you@example.com
kortix self-host start
```

`kortix self-host init` writes `docker-compose.yml`, `.env`, a `Caddyfile`, and `updater.sh` into `~/.config/kortix/self-host/<instance>/`. `kortix self-host start` runs the stack. A first `init` in a terminal asks the setup questions; `--yes` skips them and keeps the defaults. When the sandbox key is missing, `init` and `start` warn and continue.

## Configure

`kortix self-host configure` asks the setup questions again, each answer pre-filled with the current value:

1. Reachability: domain mode with the domain, API subdomain, and ACME email, or tunnel mode.
2. Admin email: the platform admins, comma-separated.
3. Restrict account creation: on by default.
4. Sandbox provider: `platinum`, or `daytona` as the legacy fallback, plus that provider's key.
5. Preview origins: `p.<domain>`, which needs the wildcard record.

Run `kortix self-host start` to apply the answers.

## First sign-in and setup

1. Open `https://<domain>` and sign up with an address in the admin list.
2. Open `/admin/git` and connect managed git. Pick Create a GitHub App, paste an existing App, or use an access token.
3. Create a team account and a project.
4. Add a model key: Customize → Models → Providers, or `kortix providers set <provider>`. A self-hosted instance has no Kortix-managed models.
5. Set `EMAIL_URL` to send sign-in and invitation mail, then set `DISABLE_SIGNUP=true` once your team has signed up.

Do this as soon as the stack starts. Until you set `EMAIL_URL` or `DISABLE_SIGNUP=true`, anyone who reaches the instance can create a confirmed account for any address, the admin address included.

## Check the instance

```bash
kortix self-host doctor
```

`kortix self-host doctor` checks Docker, the Compose plugin, the rendered compose file, the instance directory, the email URL, the preview origins, and whether sign-up is open. It exits `0` when every check passes and `1` when one fails.

## What runs where

| Service | What it does |
| --- | --- |
| `frontend` | The web app. |
| `kortix-api` | The REST API at `/v1`. |
| `llm-gateway` | The standalone LLM gateway. |
| `kortix-migrate` | A one-shot job that migrates the database. |
| `supabase-*` | Postgres, auth, storage, and the rest of the Supabase distribution. |
| `caddy` | Terminates TLS on ports 80 and 443 in domain mode. |

Sandboxes are off the box. Every session runs on Platinum or the legacy Daytona fallback, created with your provider key, and calls back to the instance's public API URL. The images are `kortix/kortix-frontend`, `kortix/kortix-api`, and `kortix/kortix-gateway` on Docker Hub.

## Evaluate on your own machine

To try Kortix without a domain:

```bash
kortix self-host init --tunnel cloudflare
kortix self-host start
```

The web app opens at `http://localhost:13737`, and `cloudflared` publishes the API at a temporary HTTPS URL. A quick tunnel changes its URL at every restart; a named tunnel keeps it stable.

## Update and back up

`kortix-updater` pulls new images once a day, migrates the database, and rolls the app services. Auto-update is on by default. `kortix self-host update` applies the channel now, `kortix self-host rollback --release <version>` goes back, and `kortix self-host status` shows the last outcome.

To back up, stop the stack, copy the instance directory, and start again. That directory holds everything to restore: `.env` with every secret and signing key, `volumes/db/data` with the Postgres database, and `volumes/storage`.

```bash
kortix self-host stop
sudo tar -czf kortix-backup.tgz -C ~/.config/kortix/self-host default
kortix self-host start
```

To run the managed cloud instead, [Try Kortix](https://kortix.com). The full operator reference is at [kortix.com/docs/host](https://kortix.com/docs/host).
