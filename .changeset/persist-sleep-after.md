---
'@cloudflare/sandbox': patch
---

Fix `sleepAfter` configuration silently reverting to the default after the sandbox restarts. The configured sleep timeout is now retained reliably across the sandbox lifecycle.
