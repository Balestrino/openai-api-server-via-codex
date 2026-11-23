# HEAD

## Security

- Build with Go 1.27.4. Binaries built with Go 1.23, which no longer receives
  security fixes, contained 36 known standard-library vulnerabilities. CI now
  runs `govulncheck`. Go 1.27.4 also fixes an HTTP/1 server crash in
  `net/http` (GO-2026-6859), which Go 1.27.3 still has.
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

## Fixes

- Send Responses input messages with `role: system` as `developer`. Codex
  answers `400 Bad Request` to a system message whose content is a list of
  parts, which is how gateways such as Bifrost convert a Chat Completions
  system message.
- Log requests whose client disconnected before the response was complete as
  `status=499` in the `request.end` access log, instead of the `500` the
  server tried to write to the closed connection.

## Distribution

- Build container images from digest-pinned Go, Node.js, and Alpine base
  images, and pin release workflow actions, including transitive action
  dependencies, through a GitHub Actions lockfile.
