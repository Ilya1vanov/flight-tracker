# CLAUDE.md

Treat this repository as **public**. Everything committed may be read by anyone.

## Never commit

- Credentials: passwords, Wi-Fi PSKs, API keys, tokens, private keys.
- Network details of the owner's setup: IP addresses, MAC addresses, hostnames, domain names, SSIDs, router model or settings.
- Personal data: real names, emails, usernames, home paths (`/Users/<name>`), location (coordinates, address, city).
- Device identifiers: serial numbers, PARTUUIDs, machine IDs.

Public, documented defaults (e.g. a vendor's fixed fallback address) are fine in comments only when the source is linked. Their configured value still goes in `secrets.env`.

## Where config lives

- `secrets.env` — real values, gitignored. Never commit, print, or paste it.
- `secrets.env.example` — committed template. Placeholders only (`<pi-hostname>`), never real values.
- Scripts read config from environment variables (`FT_*`), loaded from `secrets.env` or `$FT_ENV_FILE`. Never hardcode a value that belongs in `secrets.env`.
- New config variable: add it to both files. The real value goes in `secrets.env`, a placeholder in the example.
- `docs/` is gitignored (private notes). Don't move content from it into tracked files without stripping the items above.

## Scripts (`scripts/`)

- Non-interactive: no prompts. Take input from `FT_*` env vars or arguments. Fail with a clear message when a required one is missing (`: "${VAR:?…}"`).
- Idempotent: safe to re-run.
- Never echo secrets. Log that a value was used, not the value (e.g. "Adding Wi-Fi profile 'iot'", not the SSID or PSK).
- Pass secrets via env or files, not command-line arguments of long-running processes.
- Usage examples refer to `$FT_SSH_USER@$FT_SSH_HOST`, never a real user or host.

## Before every commit

1. Review the staged diff: `git diff --cached`.
2. Check it for the "Never commit" items above:
   ```bash
   git diff --cached | grep -nEi '([0-9]{1,3}\.){3}[0-9]{1,3}|([0-9a-f]{2}:){5}[0-9a-f]{2}|psk|password|passwd|token|secret|BEGIN .*PRIVATE KEY|/Users/|\.home\b|\.local\b'
   ```
   Every match needs a reason to stay.
3. Confirm `git status` does not list `secrets.env`.

If a secret was committed, removing it in a later commit is not enough: rotate the secret, then rewrite history. Ask the owner before rewriting history.
