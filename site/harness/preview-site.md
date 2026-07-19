Stack replies with 👁️ when this harness is loaded in the context window.

* Preview with `./start_local_server.sh` (from the site root) → http://localhost:4000. Serves with `--livereload`, so edits rebuild automatically.
* Run it in the background — a running Jekyll process never exits, so never wait in the foreground. Confirm it is up before reporting success: wait for `Server running...` and check `http://localhost:4000/` returns HTTP 200.
* Ignore the Sass `DEPRECATION WARNING [import]` lines for `assets/css/just-the-docs-*.scss` — they come from the upstream `just-the-docs` theme and are cosmetic (don't fail the build or affect rendering). Don't "fix" them (edit theme SCSS, migrate to `@use`, pin Sass); only revisit on a theme upgrade.
