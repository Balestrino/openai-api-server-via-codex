# HEAD

- Build with Go 1.26.9. Binaries built with Go 1.23, which no longer receives
  security fixes, contained 36 known standard-library vulnerabilities. CI now
  runs `govulncheck`.
- Without an incoming API key, `/v1/...` requests are accepted only when the
  `Host` header is `localhost`, a `*.localhost` name, or an IP address, which
  blocks DNS-rebinding attacks from web pages. Allow other host names with
  `--allowed-hosts`, `OPENAI_VIA_CODEX_ALLOWED_HOSTS`, or
  `[server].allowed_hosts`.
- Reject cross-origin browser writes (`Sec-Fetch-Site` / `Origin`) with
  `403 cross_origin_request`. SDK clients are unaffected.
- Reject Chat Completions `n` outside 1–128 with `400`, as OpenAI does; a very
  large `n` could exhaust server memory.
- Reject audio transcription and passthrough request bodies over 32 MiB with
  `413`, and close idle keep-alive connections after two minutes.
- Require `https` for `backend_base_url` unless it points at a loopback
  address, so the Codex access token is never sent in clear text.
