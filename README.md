# Gradescope Grid Grader

Chrome extension (MV3, no build step). On a question's **Grade Submissions** page
(`/courses/<cid>/questions/<qid>/submissions`) it adds a **Grid view** toggle:

- one cell per student showing that student's tagged pages for the question
- `◀ ▶` / arrow keys page **every** cell at once (cells with fewer pages stay put)
- cell size slider + quick column presets, persisted
- graded / ungraded marker per cell, `graded k/N` counter, filter + sort
- multiselect (click image, shift-click range, select all) →
  - **Open in tabs**: native grader for each selected student
  - **Grade selected**: pick rubric items (+ optional point adjustment / comment), see projected
    score, apply to all selected in one click (replace semantics: unchosen items get unchecked)

## Install

1. Get the code: `git clone https://github.com/huangworld/gradescope-grid-grader.git`
   (or **Code → Download ZIP** on GitHub and unzip).
2. `chrome://extensions` → enable **Developer mode** → **Load unpacked** → pick the folder.
3. In Gradescope: course → assignment → **Grade Submissions** → click a question. The URL looks like
   `.../courses/<cid>/questions/<qid>/submissions`. A **Grid view** button appears top-right.
4. After updating the code: click the reload icon on the extension card, **then reload the page**
   (a stale page loses its connection to the extension and buttons like *Open in tabs* stop working).

## Usage

- Click a cell (or its tick) to select; shift-click for a range; **Clear selection** resets.
- `←` / `→` or the toolbar page buttons move every cell one page; `Esc` closes the grid.
- In the grading panel, number keys `1`–`9` toggle rubric items.

## First-run checks (unknowns from reverse engineering)

- **Tagged pages vs all pages.** The grader page embeds all page images; which of them are the
  student's *tagged* pages for this question is detected heuristically. If a cell shows too
  many/few pages, open the toolbar `⋯` menu → **Dump props** (logs + copies the first loaded
  submission's React props) and adjust `pickTaggedPages` in `content/gradescope.js`.
  The toolbar also has a **Tagged / All** pages switch.
- **Row scraping.** Rows come from `<a href=".../submissions/<sid>/grade">` links in the native
  table. If the grid shows 0 students but the table has rows, see `collectRows` in `content/rows.js`.

## Acknowledgements

Endpoint and payload details were learned from
[Yuanpeng-Li/gradescope-mcp](https://github.com/Yuanpeng-Li/gradescope-mcp) (MIT), a Python MCP
server for Gradescope. No code is shared; this project is an independent browser extension.

## Caveats

- Uses Gradescope's internal, undocumented endpoints (`/grade` page props and `save_grade` POST).
  They can change without notice. Verify the first batch save in the native grader.
- Page images are signed S3 URLs that expire; a broken image triggers one automatic refetch.
- Batch grading **replaces** the rubric selection on each selected submission. Blank point
  adjustment / comment keeps each submission's existing values.
