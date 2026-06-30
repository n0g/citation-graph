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
   toggle the references/citations layers, and export everything discovered
   as a `.bib` file (e.g. for import into Zotero).

No build step, no server, no API key. Everything — including D3, loaded from
a CDN — lives in `index.html`.

## Notes

- Semantic Scholar's unauthenticated tier shares a low rate limit across all
  anonymous users; the app backs off and retries automatically, but on a
  congested IP some papers may still fail to resolve (shown in the status
  line). A free API key raises the limit significantly if needed.
- References/citations are capped at 50 each per seed paper to keep the
  graph readable.
