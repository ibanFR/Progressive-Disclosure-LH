Stack replies with 🧐 when this harness is loaded in the context window.

Regenerate docs with the `/nw-document` pipeline rather than hand-editing where a matching artifact exists.

The pipeline's research + validation reports live under the repo-root `docs/`, not here — `docs/` is kept out of Jekyll's build.

Link to other site pages with the Jekyll `[text]({% link path/from/site-root.md %})` tag (site-root-relative, `.md`), never a raw `.html` path — the tag is build-validated and baseurl-aware.

Parent pages (`has_children: true`) get an automatic child-page table of contents from just-the-docs — don't add `{: .no_toc }` or a manual TOC to them.
