# Codex Sandbox SDK

Run [OpenAI Codex](https://developers.openai.com/codex) on Cloudflare Sandboxes! This example shows a basic setup that does the following:

- The worker accepts POST requests that include a repository URL and a task description
- The worker spawns a sandbox, clones the repository and runs `codex exec` in non-interactive mode with the provided task
- Codex will edit all necessary files and return when done
- The Worker will return a response with the output logs from Codex and the diff left on the repo.

## Routes

| Route       | Auth method                                                              |
| ----------- | ------------------------------------------------------------------------ |
| `POST /`    | OpenAI API key (`OPENAI_API_KEY`) — pay per token                        |
| `POST /sub` | ChatGPT subscription via seeded `~/.codex/auth.json` (`CODEX_AUTH_JSON`) |

## Setup

Copy `.dev.vars.example` to `.dev.vars` and fill in the var(s) you need:

```
# For POST /
OPENAI_API_KEY=<your-api-key>

# For POST /sub — generate this by running `codex login` on a trusted machine,
# then copying the contents of ~/.codex/auth.json into the variable.
CODEX_AUTH_JSON=<your-auth-json>
```

For production, set secrets with:

```bash
wrangler secret put OPENAI_API_KEY
wrangler secret put CODEX_AUTH_JSON
```

> **Note on `CODEX_AUTH_JSON`:** Treat the file like a password — it contains
> access tokens. Codex will refresh stale tokens during a run, but in this
> stateless setup the refreshed bundle isn't persisted back to your secret
> store. For long-lived deployments, prefer the API key route or follow the
> [CI/CD auth guide](https://developers.openai.com/codex/auth/ci-cd-auth) to
> persist the refreshed file between runs.

## Usage

```bash
# Using an API key
curl -X POST http://localhost:8787/ \
  -H 'Content-Type: application/json' \
  -d '{"repo": "https://github.com/owner/repo", "task": "fix the typo in README.md"}'

# Using a ChatGPT subscription
curl -X POST http://localhost:8787/sub \
  -H 'Content-Type: application/json' \
  -d '{"repo": "https://github.com/owner/repo", "task": "fix the typo in README.md"}'
```

Response:

```json
{
  "logs": "...",
  "diff": "..."
}
```

Happy hacking!
