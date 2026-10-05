# vikunja-subpath

Builds a Vikunja **Windows amd64** binary whose frontend is built with a custom
`VIKUNJA_FRONTEND_BASE` (subpath deployment, e.g. `https://example.com/vikunja-noms/`).

Upstream ships root-path-only binaries: the vite `base` is baked at build time and
`frontend/dist` is `go:embed`'ed into the binary, so a subpath install needs a
rebuild. SQLite goes through `mattn/go-sqlite3`, hence the mingw-w64 cross build.

Run the `Build Vikunja (Windows, custom frontend base)` workflow (inputs: `ref`,
`base`) and grab the `vikunja-exe` artifact.

Deployment (nssm):

```
E:\WSL\server\vikunja\
  vikunja.exe
  config.yml        # service.rootpath, interface 127.0.0.1:50779, sqlite, files
  files\
  vikunja.db
  log.txt           # AppStdout/AppStderr
```

The reverse proxy must **strip the prefix** (`/vikunja-noms/**` -> `http://127.0.0.1:50779/**`)
and `service.publicurl` must be the full public URL *including* the subpath, because the
backend injects it into `window.API_URL` at serve time.
