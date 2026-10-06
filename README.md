# docs_editor

Browser editors ([Sveltia CMS](https://sveltiacms.app/)) for Markdown documentation kept in
GitHub repositories, published on GitHub Pages at **https://douglase.github.io/docs_editor/**.

One folder per target repository, each holding an `index.html` and a `config.yml`; all share the
single pinned copy of Sveltia under `vendor/`. Nothing runs on a server: each editor talks to the
GitHub API from the signed-in person's browser, so this site never holds or proxies document
content, and anyone without access to a target repository gets an authorization error from GitHub.

| Folder | Edits | Rendered site |
|---|---|---|
| `lab_documents/` | `uasal/lab_documents` (lab policies, onboarding, safety, computing) | internal docs server (campus/VPN) |
| `_template/` | starting point for the next repository | |

## Why a separate repo

Browsers only run the editor on HTTPS or `localhost`. The lab's rendered docs live on an internal
Steward host without a certificate, so the editors are published here over HTTPS and each docs
site's "Edit this page" links point at its folder here.

## Adding a repository

1. Copy `_template/` to `<repo_name>/`.
2. In `<repo_name>/config.yml` set `backend.repo`, `branch`, and describe the editable folders or
   files as collections. The template shows plain-Markdown (`format: raw`), front-matter, and
   review-required (`editorial_workflow`) examples. The schema in the `@sveltia/cms` npm package
   (`schema/sveltia-cms.json`) validates it.
3. Add the folder to the list in `index.html`.
4. Optional: in the target repo, add the `sveltia-pr-link` GitHub Action (see
   `uasal/lab_documents/.github/workflows/`) so review-board cards link to their pull request.
5. Point the target site's edit links at `https://douglase.github.io/docs_editor/<repo_name>/`.

## Signing in

- **Token:** a fine-grained personal access token scoped to the target repository with Contents
  and Pull requests read/write and Issues read. Works now, no setup.
- **GitHub button:** needs one OAuth App and one
  [`sveltia-cms-auth`](https://github.com/sveltia/sveltia-cms-auth) relay (a Cloudflare Worker)
  with `ALLOWED_DOMAINS=douglase.github.io`; set its URL as `base_url` in each `config.yml`.
  One relay serves every editor here.

## Maintaining

- `vendor/` is `@sveltia/cms` 0.226.0 (`dist/sveltia-cms.js` and `dist/chunks/`, which must stay
  together). To upgrade: `npm pack @sveltia/cms@<version>`, copy the same files into a new
  versioned folder, update the `<script>` path in every `*/index.html` and in `_template/`,
  remove the old folder.
- Pages is served from the `main` branch root (Settings > Pages > Deploy from a branch).
