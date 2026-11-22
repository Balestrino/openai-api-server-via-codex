# HEAD

- Build with Go 1.26.11. Binaries built with Go 1.23, which no longer receives
  security fixes, contained 36 known standard-library vulnerabilities. CI now
  runs `govulncheck`. Go 1.26.11 also fixes an HTTP/1 server crash in
  `net/http` (GO-2026-6859) and distrusts compromised intermediate
  certificates in `crypto/x509` (GO-2026-6809), both present in Go 1.26.9.
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
- Pin every GitHub Action in CI and release workflows to a full commit SHA,
  so a moved or compromised tag cannot change what builds and publishes the
  release artifacts.
- Log requests whose client disconnected before the response was complete as
  `status=499` in the `request.end` access log, instead of the `500` the
  server tried to write to the closed connection.
