# Citation Graph Explorer

A single-file HTML/JS app that turns a BibTeX bibliography into an interactive
citation network, using the [OpenAlex API](https://docs.openalex.org/).

## Usage

Open `index.html` directly in a browser (or serve it with any static file
server) and drag in a `.bib` file. It will:

1. Parse the BibTeX entries and resolve each one to an OpenAlex work
   (by DOI, falling back to a title search).
2. Fetch references and citations one level deep for each resolved paper.
3. Render a force-directed graph (D3.js), live as results arrive: seed papers
   in blue, discovered references in green, discovered citations in orange.
   Node size scales with citation count, and the 10 most "central" papers
   (a blend of citation count and how connected they are within the
   discovered graph) get a gold ring so influential sources stand out.
4. Let you click a node for details + a link to its OpenAlex page,
   toggle the references/citations layers, select papers, and either export
   everything discovered as a `.bib` file or send selected papers directly
   into a Zotero library via the Zotero Web API.

No build step, no server, no API key required. Everything — including D3,
loaded from a CDN — lives in `index.html`. OpenAlex supports CORS natively,
so unlike the earlier Semantic Scholar-based version, no proxy is needed.

## Zotero integration

Enter a Zotero API key (from [zotero.org/settings/keys](https://www.zotero.org/settings/keys),
needs library write access) and your numeric user ID, select papers in the
graph (click a node, check "Include in Zotero send", or use "Select all
visible"), then click "Send to Zotero". Papers are posted as `journalArticle`
items (title, authors, year, DOI, abstract) directly from the browser to
`api.zotero.org` — your key/ID are kept only in this browser's local storage
and never sent anywhere else.

## OpenAlex API key (optional)

The anonymous tier shares a rate-limited pool (and specifically throttles the
title-search endpoint under load), so a busy moment can mean 429s. Paste a
free key from [openalex.org/rest-api](https://openalex.org/rest-api) into the
field above the upload box to get a dedicated, higher limit instead. It's
sent as the `api_key` query parameter and stored only in this browser's
local storage.

## Notes

- The app backs off and retries automatically on 429/5xx, but on a congested
  connection some papers may still fail to resolve (shown in the status
  line). An API key raises the limit significantly if needed.
- References/citations are capped at 50 each per seed paper to keep the
  graph readable. References come back as OpenAlex IDs on the resolved work
  and are batch-resolved into full metadata (`filter=ids.openalex:...`);
  citations are fetched via `filter=cites:{id}`.
