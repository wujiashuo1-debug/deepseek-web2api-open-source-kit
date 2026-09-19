# DeepSeek local account-pool deployment kit

This repository is a sanitized deployment template for a local-only DeepSeek-compatible sidecar and an OpenAI-compatible gateway.

It intentionally contains no live credentials, browser profiles, cookies, auth snapshots, databases, logs, TLS keys, SSH keys, or production domain names. Create all secrets locally during deployment.

## Components

- `deepseek-account-pool/`: the account-pool application source.
- `deepseek-web-api/`: the browser-backed DeepSeek API source.
- `deploy/`: example systemd units and environment templates.

The recommended topology is:

```text
New API -> 127.0.0.1:8788 -> account pool -> authorized DeepSeek accounts
                         127.0.0.1 only
```

Keep the original single-account service as a separately configured rollback path. Do not enable two paths that contain the same account at the same time.

## Deployment outline

1. Install the required OS packages, Node.js, Python, Chrome/Chromium, and build tools.
2. Create a dedicated service user and private data directories.
3. Build/install both applications from their upstream instructions.
4. Copy `deploy/pool.env.example` to a root-readable environment file and generate fresh values for every secret.
5. Import only credentials obtained from the account owner through an authorized login flow. Never commit them.
6. Bind the services to loopback and put authentication, quotas, and audit controls in the gateway.
7. Test health, model listing, session isolation, failure cooldown, and a minimal request before enabling users.

## Security requirements

- Never expose ports 8787, 8788, CDP, VNC, or browser debugging publicly.
- Use one account per isolated session and conservative concurrency.
- Respect the upstream service terms, quotas, and anti-abuse controls.
- Store `accounts.json`, `auth.json`, browser profiles, API keys, and databases outside version control.
- Review all third-party licenses before redistribution.

The included application sources retain their original project licenses.
