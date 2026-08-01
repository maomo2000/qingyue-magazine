---
name: qingyue-magazine
description: Turn a supplied transcript or long-form text document into a lightweight bilingual, annotatable, print-ready magazine-style HTML reading tool. Use when the user says transcript to webpage, article to magazine, text to HTML, 文字稿转网页, 文章转杂志, or asks for a mobile-friendly two-column reader with highlights, reading notes, PDF/Markdown export, and chapter navigation.
---

# 轻阅杂志

Create a single self-contained HTML file from long-form source text. The result should feel like a quiet editorial magazine: a fixed navigation rail on the left, a readable article column on the right, restrained paper-like colors, strong hierarchy, and optional one-click language switching.

## Interactive user workflow

Treat this skill as a guided intake-to-delivery workflow. Keep the interaction short and ask for the next missing input rather than presenting a long questionnaire.

1. **Collect the source.** On the first turn, ask the user to upload or provide the transcript or long-form text document. Accept Markdown (`.md`), PDF (`.pdf`), Word (`.docx`), and plain text (`.txt`). This skill starts from text; it does not perform audio transcription.
2. **Inspect and classify.** Extract the readable text with the appropriate document/PDF tooling when available. Identify whether the source is Chinese, English, or mixed. Report the classification briefly. Preserve the original source separately from the edited article.
3. **Normalize language.** For Chinese sources, proceed directly to editorial structuring. For English or mixed sources, use an available translation skill/tool when present, otherwise produce a faithful Chinese translation alongside the English source before building the page. Keep names, quoted sources, numbers, links, and uncertainty intact; do not silently replace the original with a translation. Use the bilingual structure even if the source only supplied one language.
4. **Build and deliver.** Generate the standalone HTML beside the source or in the requested output folder. Then give the user the absolute file link and a compact usage guide:
   - open the file on desktop or mobile Chrome;
   - switch 中文 / EN from the top bar;
   - use the left chapter rail to jump sections; desktop columns scroll independently;
   - select text for underline, highlighter, or a reflection note; clicking the same underline/highlighter action again removes that mark;
   - use **Export PDF** for a bilingual printable reading record and **Export MD** for an Obsidian/Agent-readable bilingual note.
5. **Explain persistence and recovery.** Explain that annotations and notes are stored in the browser’s local storage. Clearing browser data, using a different browser/device, or sometimes opening a copied file under a different path can make them unavailable. The page therefore provides **Save progress** (download a JSON state file) and **Restore progress** (import that JSON state). Tell the user to keep the JSON beside the HTML and use it when resuming on another browser/device; also export an MD checkpoint during or after reading and PDF for a fixed visual archive. Do not claim that “Save Page” alone preserves local-storage notes.

When the user only asks how to use the skill, explain this workflow without fabricating an article. When the user provides source material, perform the workflow and create the HTML artifact.

## Workflow

1. Read the entire source before drafting. Preserve facts, names, numbers, quotations, and uncertainty. Do not invent a second-language version; if translation is requested, translate faithfully and keep the source meaning visible.
2. Make an editorial outline before writing HTML:
   - title, subtitle, source/author/date, and a one-sentence dek;
   - 4–8 sections with stable IDs;
   - for each section: a short navigation label, 2–5 keywords, a thesis/angle, and the detailed body;
   - pull quotes, key numbers, source notes, and a short “after reading” takeaway when the source supports them.
3. Remove transcript noise: duplicate openings, timestamps that interrupt reading, false starts, filler words, and ad copy. Keep meaningful personal voice and label sponsored/product material when present.
4. If bilingual output is requested or the source is bilingual, put both versions in the same semantic block using `data-lang="zh"` and `data-lang="en"`. Make the Chinese version the default when the source is Chinese. Keep section order and anchors identical across languages.
5. Start from `assets/magazine-template.html`. Replace its sample content; do not redesign the interaction from scratch unless the user asks for a different visual direction.
6. Save a standalone `.html` file next to the source or in the requested output folder. It must work with `file://` and have no remote JavaScript, CSS, fonts, images, or API calls. Inline small SVG icons if needed.

## Required page behavior

- A narrow dark masthead with issue label, title, and language controls.
- Save/restore controls must download/import a JSON state containing the current language, underline/highlight marks, and reflection notes. Restoring must replace the current rendered marks, repopulate the notes appendix, and restore the selected language.
- Desktop: a fixed-height two-column reading area. The left rail contains section labels, angles, and keywords and has its own vertical scrollbar; the article has a separate vertical scrollbar. Do not couple the two scroll positions.
- Mobile: collapse the rail into a horizontal or native `<details>` navigation so the article remains readable.
- Clicking a navigation item scrolls to its section and updates the active item. Use `scroll-margin-top`; do not rely on a framework.
- Language buttons switch all marked content, including title, metadata, navigation, captions, pull quotes, and footer. Use `aria-pressed` and visible focus styles.
- Reading annotations: when a reader selects text inside the article, show a small toolbar with underline, highlight, and note actions. Notes should preserve the selected quotation, attach to its section, render in a notes appendix, and persist in `localStorage` under a page-specific key. Provide a fixed “add note” action for notes without a selection.
- Annotation toggling: if a selected range is already underlined or highlighted, clicking the same annotation button removes that mark and removes its excerpt from the marked-excerpts list. Use a bright red underline so it remains distinct from the paper background.
- Export: provide a clearly labeled “Export PDF” action that calls `window.print()`. Print CSS must hide controls and navigation while keeping annotations and the rendered notes appendix, so the browser’s “Save as PDF” destination captures the complete reading record.
- Markdown export: provide an “Export MD” action that downloads Obsidian-friendly Markdown with YAML frontmatter, complete Chinese and English article sections, language-aware marked excerpts, and language-aware reflection notes. Use `==highlight==` for highlighter excerpts and `<u>underline</u>` for underlined excerpts so agents can distinguish the two kinds of reading trace.
- Print/PDF: hide controls and the navigation rail, use black text on white paper, preserve headings and avoid breaking a section immediately after its heading.
- Use semantic elements (`header`, `nav`, `main`, `article`, `section`, `aside`, `blockquote`, `footer`) and unique heading levels. Escape user text before inserting it into HTML.

## Editorial and visual rules

- Prefer a two-column editorial grid: rail around 220–280px, article max-width around 720–820px. On wide screens, leave generous outer margins.
- Use a warm off-white background, near-black body text, one muted red/orange accent, hairline rules, and restrained labels. Avoid gradients, cards everywhere, excessive rounded corners, and dashboard-like UI.
- Body text should be comfortable at 16–19px with 1.65–1.85 line-height. Use a serif stack for long-form reading and a sans-serif stack for labels and controls; always provide system fallbacks.
- Keep paragraphs short. Put the main argument near the top of each section, then evidence, examples, counterpoints, and implications. Mark direct quotes clearly.
- Never let the sidebar become a second summary article. Navigation labels are scannable; the right column contains the nuance.

## Quality check before delivery

Check the generated file as text and, when a browser is available, open it and inspect the first screen plus a long section. Verify:

- no placeholder text, broken anchors, duplicate IDs, invalid nesting, or missing language variants;
- every sidebar item has a matching section;
- Chinese punctuation and English typography are natural;
- language switching changes all visible translatable content;
- long words/URLs do not overflow, and the mobile layout has no horizontal scroll;
- opening the file locally works without a server;
- the final response links to the absolute HTML path and briefly states what was preserved or editorially condensed.

## Output convention

Use a descriptive filename such as `文章名-轻阅版.html`. If the user asks for only the skill, create or update the skill folder and do not fabricate a magazine. If they provide source text and ask to use the skill, create the HTML artifact as well.
