# docs_editor

The browser editor for the [UASAL lab documents](https://github.com/uasal/lab_documents),
published on GitHub Pages at **https://douglase.github.io/docs_editor/**.

It is [Sveltia CMS](https://sveltiacms.app/): a static page (`index.html`), a configuration
(`config.yml`) that maps the lab_documents folders to editable collections, and a pinned copy of
the Sveltia script under `vendor/`. Nothing runs on a server.

## Why it lives here

Browsers only run the editor on HTTPS or `localhost`. The rendered docs site is on an internal
Steward host without a certificate, so the editor is published here over HTTPS and the docs
site's "Edit this page" links point at it. The editor talks to the GitHub API from the signed-in
person's browser; this site never holds or proxies document content, and anyone without access to
`uasal/lab_documents` gets an authorization error from GitHub.

## Using it

1. Open https://douglase.github.io/docs_editor/ and sign in: with GitHub once the OAuth relay is
   configured (`base_url` in `config.yml`), or with a fine-grained personal access token scoped to
   `lab_documents` with Contents and Pull requests read/write and Issues read.
2. Lab-wide, Computing, ARB and Reports pages save straight to `main`. Policies and Safety open a
   draft pull request that a maintainer merges.
3. "Edit this page" on the docs site opens the matching page here.

## Maintaining it

- The collections in `config.yml` mirror the folders of `uasal/lab_documents`; add a line when a
  top-level page or folder is added there. The same file is kept at `admin/config.yml` in
  lab_documents (used for local testing); keep the two in step.
- `vendor/` is `@sveltia/cms` 0.226.0 (`dist/sveltia-cms.js` and `dist/chunks/`). To upgrade,
  `npm pack @sveltia/cms@<version>`, copy the same files into a new versioned folder, update the
  `<script>` path in `index.html`, and remove the old folder.
- Pages is served from the `main` branch root (Settings > Pages).
