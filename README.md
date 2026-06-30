# Citation Graph Explorer

A single-file HTML/JS app that turns a BibTeX bibliography into an interactive
citation network, using the [Semantic Scholar Graph API](https://api.semanticscholar.org/api-docs/graph).

## Usage

Open `index.html` directly in a browser (or serve it with any static file
server) and drag in a `.bib` file. It will:

1. Parse the BibTeX entries and resolve each one to a Semantic Scholar paper
   (by DOI, falling back to a title search).
2. Fetch references and citations one level deep for each resolved paper.
3. Render a force-directed graph (D3.js): seed papers in blue, discovered
   references in green, discovered citations in orange. Node size scales
   with citation count.
4. Let you click a node for details + a link to its Semantic Scholar page,
   toggle the references/citations layers, select papers, and either export
   everything discovered as a `.bib` file or send selected papers directly
   into a Zotero library via the Zotero Web API.

No build step, no server, no API key required for Semantic Scholar.
Everything — including D3, loaded from a CDN — lives in `index.html`.

## Zotero integration

Enter a Zotero API key (from [zotero.org/settings/keys](https://www.zotero.org/settings/keys),
needs library write access) and your numeric user ID, select papers in the
graph (click a node, check "Include in Zotero send", or use "Select all
visible"), then click "Send to Zotero". Papers are posted as `journalArticle`
items (title, authors, year, DOI, abstract) directly from the browser to
`api.zotero.org` — your key/ID are kept only in this browser's local storage
and never sent anywhere else.

## Notes

- Semantic Scholar requests are routed through a self-hosted `cors-anywhere`
  proxy (`annie-easley.n0g.at`) so that rate-limit/error responses keep their
  CORS headers — without it, the browser can't tell a 429 apart from a
  generic network failure, since Semantic Scholar omits CORS headers on
  non-2xx responses.
- Semantic Scholar's unauthenticated tier shares a low rate limit across all
  anonymous users; the app backs off and retries automatically on 429/5xx,
  but on a congested IP some papers may still fail to resolve (shown in the
  status line). A free API key raises the limit significantly if needed.
- References/citations are capped at 50 each per seed paper to keep the
  graph readable.
