# vikunja-subpath

Builds a Vikunja **Windows amd64** binary whose frontend is built with a custom
`VIKUNJA_FRONTEND_BASE` (subpath deployment, e.g. `https://example.com/vikunja-noms/`).

Upstream ships root-path-only binaries: the vite `base` is baked at build time and
`frontend/dist` is `go:embed`'ed into the binary, so a subpath install needs a
rebuild. SQLite goes through `mattn/go-sqlite3`, hence the mingw-w64 cross build.

Run the `Build Vikunja (Windows, custom frontend base)` workflow (inputs: `ref`,
`base`). It uploads a `vikunja-exe` artifact **and** publishes the binary as the
release asset of tag `<ref>-subpath`, e.g.

```
https://github.com/Young-Lord/vikunja-subpath/releases/download/v2.7.0-subpath/vikunja.exe
```

which is what the server downloads (scp over the chisel tunnel dies mid-transfer).

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
