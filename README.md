# Citation Graph Explorer

A single-file HTML/JS app that turns a BibTeX bibliography into an interactive,
explorable citation network, using the [OpenAlex API](https://docs.openalex.org/).

## Usage

Open `index.html` directly in a browser (or serve it with any static file
server).

1. On first load you'll be asked for an OpenAlex API key (avoids the shared
   anonymous rate limit) — paste one or click "Skip for now" to proceed
   anonymously. Once set, this is remembered in local storage and the gate
   is skipped on future visits.
2. Drag a `.bib` file into the sidebar. Each entry is resolved to an OpenAlex
   work (by DOI, falling back to title search) and appears in the sidebar's
   paper list; its references and citations are fetched and added to the
   graph on the right, live as results arrive.
3. The graph (D3.js force layout) colors seed papers blue, discovered
   references green, discovered citations orange. Node size scales with
   citation count. A gold ring marks the 10 best-connected papers that
   *aren't* in your paper list yet — good candidates to expand next.
4. **Click any node to expand it**: it's added to the sidebar's paper list
   and its own references/citations are fetched and merged into the graph,
   so you can keep growing the network beyond the original bibliography.
   Hover a node for its title/authors/year/citation count and an OpenAlex
   link, without expanding it.
5. Use the sidebar footer to export everything discovered as a `.bib` file,
   or open the Zotero panel to pick papers and send them straight to a
   Zotero library.

No build step, no server required. Everything — including D3, loaded from a
CDN — lives in `index.html`. OpenAlex supports CORS natively, so no proxy is
needed.

## Zotero integration

Click the Zotero button in the sidebar footer to open the panel. Enter a
Zotero API key (from [zotero.org/settings/keys](https://www.zotero.org/settings/keys),
needs library write access) and your numeric user ID, check the papers you
want (or "Select all"), then "Send to Zotero". Papers are posted as
`journalArticle` items (title, authors, year, DOI, abstract) directly from
the browser to `api.zotero.org` — your key/ID are kept only in this
browser's local storage and never sent anywhere else.

## OpenAlex API key (optional)

The anonymous tier shares a rate-limited pool (and specifically throttles the
title-search endpoint under load), so a busy moment can mean 429s. Paste a
free key from [openalex.org/rest-api](https://openalex.org/rest-api) at the
initial gate, or later in the sidebar, to get a dedicated, higher limit
instead. It's sent as the `api_key` query parameter and stored only in this
browser's local storage.

## Notes

- The app backs off and retries automatically on 429/5xx, but on a congested
  connection some papers may still fail to resolve (shown in the sidebar's
  paper list with a warning icon). An API key raises the limit significantly
  if needed.
- References/citations are capped at 50 each per paper to keep the graph
  readable. References come back as OpenAlex IDs on the resolved work and
  are batch-resolved into full metadata (`filter=ids.openalex:...`);
  citations are fetched via `filter=cites:{id}`. Expanding a node (via
  click) reuses this same logic, so the graph can grow indefinitely as you
  explore.
- Icons are from [Phosphor Icons](https://phosphoricons.com/) (MIT-licensed
  SVGs), inlined directly — no icon font/JS dependency. The Zotero button
  uses a generic "books" icon rather than Zotero's own logo, since no
  verified logo asset was available to embed.
