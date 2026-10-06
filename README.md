# docs_editor

Readers and editors for Markdown documentation kept in **private** GitHub repositories, published
on GitHub Pages at **https://www.ewandouglas.space/docs_editor/** (also reachable as
`douglase.github.io/docs_editor`).

One folder per repository:

| Path | What it is |
|---|---|
| `<repo>/index.html` | **Reader**: [Docsify](https://docsify.js.org) with `basePath` set to the GitHub Contents API. Pages, sidebar, search index, images and PDFs are fetched from the private repo with the reader's own GitHub credential. Links between pages, full-text search and an "Edit this page" toolbar work as on a normal Docsify site. |
| `<repo>/edit/` | **Editor**: [Sveltia CMS](https://sveltiacms.app/) with a `config.yml` mapping the repo's folders to collections. |
| `vendor/` | Pinned copies of Docsify 5.0.0 and Sveltia 0.226.0 shared by every folder. |
| `_template/` | Starting point for the next repository. |

Nothing runs on a server and no document content is ever stored here. Both pages talk to the
GitHub API from the browser, so only people with access to a repository can read or edit it;
anyone else gets an authorization error from GitHub. One sign-in covers reading and editing: the
two pages share the browser-storage record Sveltia uses (`sveltia-cms.user`).

Why here rather than on a lab server: browsers only run the editor on HTTPS or `localhost`, and
serving the reader from the same place removes the server, cron job, DNS and certificate a
self-hosted copy needs. The repository stays the system of record.

| Folder | Repository |
|---|---|
| `lab_documents/` | `uasal/lab_documents` (lab policies, onboarding, safety, computing) |

## Adding a repository

1. Copy `_template/` to `<repo_name>/`.
2. Reader, `<repo_name>/index.html`: set `OWNER`, `REPO`, `BRANCH`, the page title, and the
   `FILE_COLLECTIONS` / `FOLDER_COLLECTIONS` maps that turn a file path into its editor entry.
   The target repo needs a `_sidebar.md` at its root (Docsify navigation; search indexes what it
   lists) and a `README.md` as the home page.
3. Editor, `<repo_name>/edit/config.yml`: set `backend.repo`, `branch`, and describe the editable
   folders or files as collections. The template shows plain-Markdown (`format: raw`),
   front-matter, and review-required (`editorial_workflow`) examples. The schema in the
   `@sveltia/cms` npm package (`schema/sveltia-cms.json`) validates it.
4. Add the folder to the list in the root `index.html`.
5. Optional: in the target repo, add the `sveltia-pr-link` GitHub Action (see
   `uasal/lab_documents/.github/workflows/`) so review-board cards link to their pull request.

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
- GitHub API budget: 5,000 requests per hour per signed-in user. A first visit costs about one
  request per page (the search index, cached for an hour) plus one per image; after that, one per
  page view.

## Claude and other tools

The reader requires a GitHub login, so Claude cannot fetch it. That is by design: the repository is
the documentation database. Connect `uasal/lab_documents` to Claude through the GitHub connector
(Claude.ai Projects or a Team/Enterprise connector) or clone it for Claude Code; its `CLAUDE.md`
describes the layout. The reader and editor are the human-facing views of the same files.
