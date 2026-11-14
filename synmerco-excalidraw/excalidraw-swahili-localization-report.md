# Excalidraw UI Localization to Swahili (Kenya) — Report

**Status: COMPLETE — PULL 2 done 14 Nov 2026 (note recorded 02:15:52 UTC / 09:15:52 WIB), independent second-model review done, no changes to the sources and no fixes to the translations. Deliverable ready for lokie to review. NOT submitted — submit only after lokie has seen the deliverable (first-win rule).**

- Job: localize every Excalidraw interface string into Swahili (Kenya).
- Source of truth for translation: the pinned commit file (see Sources below).
- Deliverables: this report (D1), `excalidraw-master-strings-pull1.csv` (D2, PULL 1), `excalidraw-master-strings-pull2.csv` (D3, PULL 2, complete — 645 data rows, SHA-256 `3e64604f8d616c994289028e31dc69096f735eb7688c7743b5926fcfe6462784`, byte-identical to D2), `translations.jsonl` (D4), `glossary.csv` (D5).

## 1. Counts

- Strings in the pinned file: **642** (41 top-level sections, all values non-empty strings; 0 empty strings).
- Placeholder occurrences in the pinned file: **51**, across 12 distinct placeholders: `{{shortcut}}` (17), `{{shortcut_1}}` (11), `{{shortcut_2}}` (11), `{{count}}` (2), `{{exportSelection}}` (2), `{{exportColorScheme}}` (2), `{{numShapes}}` (1), `{{maxSize}}` (1), `{{eventId}}` (1), `{{authorName}}` (1), `{{min}}` (1), `{{max}}` (1).
- HTML-like tag occurrences: **50**, across 30 distinct tags (`<link>`, `<bold>`, `<button>`, `<br>`, `<issueLink>`, `<discordLink>`, `<shift>`, and the eight Mermaid diagram-type links, plus closers).
- URLs inside string values: **0**.
- Occurrences of the product name `Excalidraw`: **20** (8 of them as `Excalidraw+`).
- Strings containing a real line break: **10** (written as the two characters `\n` in all data files).
- One single-brace token, `{filename}`, in `toast.fileSavedToFilename` — kept verbatim (it is not a `{{...}}` placeholder, but it is a runtime token all the same).
- Glossary terms: **193** (see section 4).
- Translated strings: **642 of 642** (every key path of the pinned file has exactly one line in `translations.jsonl`, in file order).

## 2. Sources and pull record

| Pull | Source | Source time (HTTP Date header) | Bytes | SHA-256 of raw response | Strings |
|---|---|---|---|---|---|
| PULL 1 — pinned | `https://raw.githubusercontent.com/excalidraw/excalidraw/7ded689cad49331167e29fd88b38f38d3736d576/packages/excalidraw/locales/en.json` | Fri, 13 Nov 2026 02:02:15 GMT | 33,630 | `30cde995f90d3714632e4031b3c950fb8b6c3f55f4722a855b806385852dcbbb` | 642 |
| PULL 1 — master | `https://raw.githubusercontent.com/excalidraw/excalidraw/master/packages/excalidraw/locales/en.json` | Fri, 13 Nov 2026 02:09:47 GMT | 33,711 | `59fb4514a8de9ad98629c74468acde9572fccedcd260020725910635bc0575b4` | 645 |

Raw responses of both pulls are saved unmodified. The PULL 1 progress note was filed to the escrow at 2026-11-13 02:10:17 UTC, stating the PULL 1 CSV row count (645 data rows plus header) and SHA-256.

## 3. Translation rules applied

1. **Placeholders kept exactly.** Every `{{...}}` token appears in the Swahili string exactly as in English, same token, same count, same order. Verified mechanically for all 642 rows.
2. **Tags kept exactly.** Every HTML-like tag (`<link>`, `<bold>`, `<button>`, `<br></br>`, `<shift>`, Mermaid `*Link` tags, and all closers) is preserved; only the text inside tags is translated.
3. **Keyboard key names kept exactly.** The values of `keys.ctrl`, `keys.option`, `keys.cmd`, `keys.alt`, `keys.escape`, `keys.enter`, `keys.shift` are unchanged, as are `keys.spacebar`, `keys.delete`, and `keys.mmb` (all ten `keys.*` rows are byte-identical to English — key names are not translated). The key name inside `<shift>Shift</shift>` is likewise unchanged.
4. **Product and brand names kept exactly.** `Excalidraw`, `Excalidraw+`, `Mermaid`, `GitHub`, `Discord`, `Twitter`, `Brave`, `Firefox`, `PWA`, `PNG`, `SVG`, `IFrame`, `MIT License`, and the font names `Architect`, `Artist`, `Cartoonist` are unchanged. The watermark strings `Made with Excalidraw` (label and quoted text) are unchanged because they are the literal watermark content added to the canvas.
5. **Technical literals kept exactly.** The Firefox flag `dom.events.asyncClipboard.clipboardItem`, the page `about:config`, the Brave setting name `Aggressively Block Fingerprinting`, the single-brace token `{filename}`, the arrow `→`, and the emoticon `:(` are unchanged.
6. **Ordinary words translated even when they look like key names.** Words used as ordinary verbs are translated (for example `Delete` the action becomes `Futa`, while the `keys.delete` value stays `Delete`; `Shift` inside prose would be translated, but no such case occurs).
7. **Empty stays empty.** No empty English strings exist in this file, so the rule did not trigger.
8. **Line breaks as `\n`.** In every data file, a line break inside a value is written as the two characters `\n`, so no row ever spans lines. The one value with leading and trailing spaces (`errorSplash.clearCanvasCaveat`) keeps them.
9. **Short, natural interface Swahili.** Labels stay short; sentences stay direct; the imperative is used for buttons and hints, matching the English tone.
10. **Glossary consistency.** Every recurring term uses the glossary translation or its grammatical form (for example `kuchagua`, `ubofye`, `kushikilia`, `kuhamishwa` are inflected forms of glossary verbs). See section 4.
11. **No machine-translation paste, no copied translation.** Every string was translated and reviewed by hand for this job; no existing Excalidraw translation was copied.

## 4. Glossary summary

`glossary.csv` holds **193 terms**, one row per term, sorted by English term, with fields `english term, translated term, times used`. "Times used" counts case-insensitive substring occurrences of the translated term across the Swahili values in `translations.jsonl`, so grammatical forms sharing the stem (for example `kuchagua`, `ukuzaji`, `kumenakiliwa`) and plurals sharing the stem are included; the method is reproducible from the two files alone. Every term is used at least once.

Most-used terms: `library → maktaba` (44), `save → hifadhi` (35), `copy → nakili` (34), `canvas → turubai` (24), `link → kiungo` (24), `text → maandishi` (23), `file → faili` (22), `image → picha` (21).

Notes on shared words: several English terms legitimately share one Swahili word, so their counts coincide — `save`/`storage → hifadhi`, `open`/`unlock → fungua`, `close`/`lock → funga`, `import`/`insert → ingiza`, `cut`/`crop → kata`. Conversely, one English word can need two Swahili words by part of speech: `open` the verb is `fungua`, but `open` the adjective in "Keep sidebar open" is `wazi`; `align` the verb is `nyoosha`, but `Text align` (the alignment setting) is `Mpangilio wa maandishi`.

Key naming decisions: `canvas → turubai`, `scene → tukio` (kept distinct from `drawing → mchoro`), `stroke → mpaka`, `element → kipengele` (kept distinct from `item → kipengee` for library items), `shape → umbo` / `shapes → maumbo`, `clipboard → ubao wa kunakili`, `opacity → opasiti`, `prompt → prompti`, `diagram → diagramu`, `flowchart → chati ya mtiririko`, `cardinality → kardinaliti`, `crow's foot → mguu wa kunguru`.

## 5. Sample rows (30) with notes on hard choices

| # | Key path | English | Swahili | Note |
|---|---|---|---|---|
| 1 | `labels.addWatermark` | Add "Made with Excalidraw" | Ongeza "Made with Excalidraw" | Quoted watermark text kept verbatim; only the verb translated. |
| 2 | `labels.madeWithExcalidraw` | Made with Excalidraw | Made with Excalidraw | The watermark string itself; kept unchanged. |
| 3 | `labels.strokeStyle` | Stroke style | Mtindo wa mpaka | `stroke → mpaka` (outline/border), applied in all stroke compounds. |
| 4 | `labels.arrowhead_crowfoot_one_or_many` | Crow's foot (one or many) | Mguu wa kunguru (moja au nyingi) | ERD notation term; literal translation keeps the notation recognizable. |
| 5 | `labels.installPWA` | Install Excalidraw locally (PWA) | Sakinisha Excalidraw kwenye kifaa (PWA) | Product name and PWA kept; "locally" rendered as "kwenye kifaa" (on the device). |
| 6 | `labels.textToDiagram` | Text to diagram | Maandishi hadi diagramu | `diagram → diagramu` throughout, distinct from `mchoro` (drawing). |
| 7 | `elementLink.notFound` | Linked object wasn't found on canvas. | Kitu kilichounganishwa hakikupatikana kwenye turubai. | Passive construction kept; `object → kitu`. |
| 8 | `buttons.zoomOut` | Zoom out | Punguza ukuzaji | "Punguza" alone would be ambiguous; the zoom noun is kept for symmetry with "Weka upya ukuzaji" (Reset zoom). |
| 9 | `alerts.collabStopOverridePrompt` | Stopping the session will overwrite your previous, locally stored drawing. Are you sure? (blank line) (If you want to keep your local drawing, simply close the browser tab instead.) | Kusitisha kipindi kutaandika juu ya mchoro wako wa awali, uliohifadhiwa kwenye kifaa. Una uhakika? `\n\n` (Ikiwa unataka kuweka mchoro wako wa kwenye kifaa, funga tu kichupo cha kivinjari badala yake.) | Two-paragraph value; the blank line is written as `\n\n` in the data files. "overwrite" rendered as "kuandika juu ya". |
| 10 | `alerts.uploadedSecurly` | The upload has been secured with end-to-end encryption, which means that Excalidraw server and third parties can't read the content. | Upakiaji umelindwa kwa usimbaji fiche wa mwisho-hadi-mwisho, ambayo inamaanisha kuwa seva ya Excalidraw na wahusika wengine hawawezi kusoma maudhui. | Long sentence kept as one sentence; product name kept. |
| 11 | `errors.brave_measure_text_error.line1` | Looks like you are using Brave browser with the `<bold>Aggressively Block Fingerprinting</bold>` setting enabled. | Inaonekana unatumia kivinjari cha Brave na mpangilio wa `<bold>Aggressively Block Fingerprinting</bold>` umewashwa. | Setting name kept in English inside the tag (it is the literal label in Brave); tag preserved. |
| 12 | `errors.brave_measure_text_error.line4` | If disabling this setting doesn't fix the display of text elements, please open an `<issueLink>issue</issueLink>` on our GitHub, or write us on `<discordLink>Discord</discordLink>` | Ikiwa kuzima mpangilio huu hakurekebishi uonyeshaji wa vipengele vya maandishi, tafadhali fungua `<issueLink>issue</issueLink>` kwenye GitHub yetu, au tuandikie kwenye `<discordLink>Discord</discordLink>` | GitHub term `issue` and `Discord` kept; both link tags preserved. |
| 13 | `hints.canvasPanning` | To move canvas, hold `{{shortcut_1}}` or `{{shortcut_2}}` while dragging, or use the hand tool | Ili kusogeza turubai, shikilia `{{shortcut_1}}` au `{{shortcut_2}}` huku ukiburuta, au tumia zana ya mkono | Two placeholders kept in order. |
| 14 | `hints.resize` | You can constrain proportions by holding `{{shortcut_1}}` while resizing, (newline) hold `{{shortcut_2}}` to resize from the center | Unaweza kuzuia uwiano kwa kushikilia `{{shortcut_1}}` unapobadilisha ukubwa, `\n` shikilia `{{shortcut_2}}` ili kubadilisha ukubwa kutoka katikati | Line break written as `\n`; both placeholders kept. |
| 15 | `hints.firefox_clipboard_write` | This feature can likely be enabled by setting the "dom.events.asyncClipboard.clipboardItem" flag to "true". To change the browser flags in Firefox, visit the "about:config" page. | Kipengele hiki kinaweza kuwashwa kwa kuweka alama ya "dom.events.asyncClipboard.clipboardItem" kuwa "true". Ili kubadilisha alama za kivinjari katika Firefox, tembelea ukurasa wa "about:config". | Flag name, `"true"`, and `about:config` kept verbatim. |
| 16 | `errorSplash.clearCanvasCaveat` | `␣`This will result in loss of work`␣` | `␣`Hii itasababisha upotezaji wa kazi`␣` | Leading and trailing spaces of the English value preserved (`␣` marks the spaces). |
| 17 | `errorSplash.openIssueMessage` | We were very cautious not to include your scene information on the error. If your scene is not private, please consider following up on our `<button>bug tracker</button>`. Please include information below by copying and pasting into the GitHub issue. | Tulikuwa waangalifu sana kutokujumuisha taarifa za tukio lako kwenye hitilafu. Ikiwa tukio lako si la faragha, tafadhali fikiria kufuatilia kwenye `<button>kifuatilia hitilafu</button>` yetu. Tafadhali jumuisha taarifa zilizo hapa chini kwa kunakili na kubandika kwenye issue ya GitHub. | `<button>` tag preserved; "bug tracker" translated inside the tag. |
| 18 | `roomDialog.desc_privacy` | Don't worry, the session is end-to-end encrypted, and fully private. Not even our server can see what you draw. | Usijali, kipindi kimesimbwa fiche kutoka mwisho hadi mwisho, na ni cha faragha kabisa. Hata seva yetu haiwezi kuona unachochora. | Reassuring tone kept; encryption phrase matches the glossary. |
| 19 | `helpDialog.shortcuts` | Keyboard shortcuts | Mikato ya kibodi | Plural form; the singular `mkato` never occurs standalone in the file. |
| 20 | `publishDialog.noteLicense` | By submitting, you agree the library will be published under the `<link>MIT License</link>`, which in short means anyone can use them without restrictions. | Kwa kuwasilisha, unakubali kuwa maktaba itachapishwa chini ya `<link>MIT License</link>`, ambayo kwa ufupi inamaanisha mtu yeyote anaweza kuzitumia bila vizuizi. | License name kept inside the link tag. |
| 21 | `publishSuccessDialog.content` | Thank you `{{authorName}}`. Your library has been submitted for review. You can track the status `<link>here</link>` | Asante `{{authorName}}`. Maktaba yako imewasilishwa kwa ukaguzi. Unaweza kufuatilia hali `<link>hapa</link>` | Placeholder plus link tag; link text translated. |
| 22 | `imageExportDialog.tooltip.embedScene` | Scene data will be saved into the exported PNG/SVG file so that the scene can be restored from it. (newline) Will increase exported file size. | Data ya tukio itahifadhiwa kwenye faili ya PNG/SVG iliyohamishwa ili tukio liweze kurejeshwa kutoka humo. `\n` Itaongeza ukubwa wa faili iliyohamishwa. | Line break as `\n`; PNG/SVG kept. |
| 23 | `toast.copyToClipboardAsPng` | Copied `{{exportSelection}}` to clipboard as PNG (newline) (`{{exportColorScheme}}`) | Imenakiliwa `{{exportSelection}}` kwenye ubao wa kunakili kama PNG `\n` (`{{exportColorScheme}}`) | Both placeholders kept; the `{{exportSelection}}` values resolve to the translated `toast.canvas`/`toast.selection` strings (`turubai`/`uteuzi`). |
| 24 | `toast.fileSavedToFilename` | Saved to {filename} | Imehifadhiwa kwenye {filename} | Single-brace runtime token kept verbatim. |
| 25 | `mermaid.description` | `<flowchartLink>Flowcharts</flowchartLink>`, `<sequenceLink>Sequence</sequenceLink>`, `<classLink>Class</classLink>`, `<stateLink>State</stateLink>` and `<erdLink>Entity Relationship</erdLink>` diagrams, `<ganttLink>Gantt charts</ganttLink>`, `<quadrantLink>Quadrant charts</quadrantLink>` and `<timelineLink>Timelines</timelineLink>` are converted to Excalidraw elements. Other types are inserted as an image. | `<flowchartLink>Chati za mtiririko</flowchartLink>`, `<sequenceLink>Mfuatano</sequenceLink>`, `<classLink>Darasa</classLink>`, `<stateLink>Hali</stateLink>` na `<erdLink>Uhusiano wa Huluki</erdLink>` diagramu, `<ganttLink>Chati za Gantt</ganttLink>`, `<quadrantLink>Chati za robo</quadrantLink>` na `<timelineLink>Ratiba</timelineLink>` hubadilishwa kuwa vipengele vya Excalidraw. Aina zingine huingizwa kama picha. | Eight link tags preserved with translated inner text; diagram-type names match the AI-chat hint string (`chat.placeholder.hint`). |
| 26 | `fileDrop.replaceHint` | Hold `<shift>Shift</shift>` to keep existing content | Shikilia `<shift>Shift</shift>` ili kuweka maudhui yaliyopo | Key name inside the tag kept exactly, per the rules. |
| 27 | `chat.rateLimit.generalRateLimit` | Hold your horses, you're too fast for us! Please wait a moment before trying again. | Pole pole, wewe ni mwepesi sana kwetu! Tafadhali subiri kidogo kabla ya kujaribu tena. | Idiom rendered with the natural Swahili equivalent "Pole pole" (slowly); "hold" here is not the keyboard hold. |
| 28 | `chat.errors.promptTooShort` | Prompt is too short (min `{{min}}` characters) | Prompti ni fupi sana (kiwango cha chini ni herufi `{{min}}`) | "min" expanded to "kiwango cha chini" (minimum level); placeholder kept. |
| 29 | `chat.errors.invalidDiagram` | Generated an invalid diagram :(. You may edit manually, retry with auto-fix, or try a different prompt. | Imetengeneza diagramu si sahihi :(. Unaweza kuhariri wewe mwenyewe, kujaribu tena kwa rekebisho otomatiki, au kujaribu prompti tofauti. | Emoticon kept; "manually" rendered as "wewe mwenyewe" (yourself). |
| 30 | `keys.shift` | Shift | Shift | Key names are not translated; all ten `keys.*` rows are unchanged. |

## 6. Key paths that differ between the pinned commit and master (PULL 1)

Comparing the pinned file (642 strings) with the master file pulled at PULL 1 (645 strings):

- **Added in master (3):** `labels.minimap` ("Minimap"), `labels.zoom` ("Zoom"), `buttons.zoomToFit` ("Zoom to fit").
- **Removed from master (0):** none.
- **Changed in master (1):** `labels.showHints`: pinned "Show hints" → master "Toolbar hints".
- Net: 645 − 642 = +3, consistent with 3 added, 0 removed.

The translations in D4 cover the pinned file only, as specified; the three new master strings are not part of this deliverable. If the buyer wants them translated too, they are: Minimap, Zoom, Zoom to fit.

## 7. Changes since PULL 1 (completed at PULL 2)

PULL 2 re-ran the same two requests 24 hours after the PULL 1 note (PULL 2 note recorded 2026-11-14 02:15:52 UTC / 09:15:52 WIB, 24 h 5 m after the PULL 1 note). Comparing the PULL 1 and PULL 2 master CSVs by the key column:

- **Added in PULL 2 (0):** none.
- **Removed in PULL 2 (0):** none.
- **Changed in PULL 2 (0):** none.
- **Row counts:** PULL 1 = 645 data rows (+ header), PULL 2 = 645 data rows (+ header). Check: 645 = 645 + 0 − 0. ✓
- Both raw responses are byte-identical to PULL 1 (pinned 33,630 bytes, SHA-256 `30cde995f90d3714632e4031b3c950fb8b6c3f55f4722a855b806385852dcbbb`; master 33,711 bytes, SHA-256 `59fb4514a8de9ad98629c74468acde9572fccedcd260020725910635bc0575b4`; ETags identical too), and the PULL 2 CSV is byte-identical to the PULL 1 CSV (SHA-256 `3e64604f8d616c994289028e31dc69096f735eb7688c7743b5926fcfe6462784` for both). The pinned file is likewise unchanged, so the 642 translations in D4 still cover the pinned file exactly.

**No change.**

## 8. Verification performed

- **Completeness:** `translations.jsonl` has 642 lines, one per pinned key path, in pinned file order; field order on every line is `key path`, `english`, `swahili`.
- **Placeholders:** for every row, the multiset of `{{...}}` tokens in Swahili equals the English multiset (51 occurrences total, 0 mismatches).
- **Tags:** for every row, the multiset of `<...>` tags in Swahili equals the English multiset (50 occurrences total, 0 mismatches).
- **Product name:** the count of `Excalidraw` matches per row (20 total, 0 mismatches).
- **Key names:** all ten `keys.*` rows are byte-identical to English; `<shift>Shift</shift>` preserved.
- **Tokens and literals:** `{filename}` preserved; no URLs introduced; the Firefox flag, `about:config`, the Brave setting name, `→`, and `:(` preserved.
- **Line breaks:** for every row, the number of real newlines in English equals the number of literal `\n` sequences in Swahili (10 rows affected, 0 mismatches).
- **Glossary:** 193 terms, no duplicates, every term used at least once; a consistency sweep over 100+ term checks flagged 34 rows, of which 33 were false positives (placeholder tokens, tag names, subjunctive verb forms, and idioms) and 1 was a genuine fix (`buttons.zoomOut`, corrected to "Punguza ukuzaji" and re-verified).
- **CSV hygiene:** both CSVs carry a header row; no value spans rows; line breaks inside values are literal `\n`.

## 9. PROOF — requests, times, and raw excerpts

### Request 1 — pinned file (PULL 1)

- URL: `https://raw.githubusercontent.com/excalidraw/excalidraw/7ded689cad49331167e29fd88b38f38d3736d576/packages/excalidraw/locales/en.json`
- Response header: `Date: Fri, 13 Nov 2026 02:02:15 GMT`
- HTTP 200, 33,630 bytes, SHA-256 `30cde995f90d3714632e4031b3c950fb8b6c3f55f4722a855b806385852dcbbb`
- Raw excerpt (start of response body):
  `{"labels": {"signUp": "Sign up", "signIn": "Sign in", "upgrade": "Upgrade", "tryExcalidrawPlus": "Try Excalidraw+", "plusFreeTrial": "14-day free trial", "paste": "Paste", "pasteAsPlaintext": "Paste as plaintext", "pasteCharts": "Paste charts", "chartType_bar": "Bar chart", ...`

### Request 2 — master file (PULL 1)

- URL: `https://raw.githubusercontent.com/excalidraw/excalidraw/master/packages/excalidraw/locales/en.json`
- Response header: `Date: Fri, 13 Nov 2026 02:09:47 GMT`
- HTTP 200, 33,711 bytes, SHA-256 `59fb4514a8de9ad98629c74468acde9572fccedcd260020725910635bc0575b4`
- Raw excerpt (start of response body):
  `{"labels": {"signUp": "Sign up", "signIn": "Sign in", "upgrade": "Upgrade", "tryExcalidrawPlus": "Try Excalidraw+", "plusFreeTrial": "14-day free trial", "paste": "Paste", "pasteAsPlaintext": "Paste as plaintext", "pasteCharts": "Paste charts", "chartType_bar": "Bar chart", ...`

### Request 3 — PULL 1 progress note

- Endpoint: `POST /v1/escrows/esc_194a1c8f898a4d6ab3da27f3/progress`
- Server-recorded time: `2026-11-13 02:10:17.112683+00`
- Note text: "PULL 1: Pulled both Excalidraw en.json sources from raw.githubusercontent.com. Master (...), source time Fri, 13 Nov 2026 02:09:47 GMT (HTTP Date header). Pinned commit 7ded689... (...), source time Fri, 13 Nov 2026 02:02:15 GMT. Flattened master into excalidraw-master-strings-pull1.csv: 645 data rows plus header, SHA-256 3e64604f8d616c994289028e31dc69096f735eb7688c7743b5926fcfe6462784. Raw responses of both pulls saved. PULL 2 follows 24 hours after this note."

### Request 4 — master + pinned files (PULL 2)

- URLs: same as Requests 1–2 (`.../7ded689cad49331167e29fd88b38f38d3736d576/...` for pinned, `.../master/...` for master).
- Pinned response header: `Date: Sat, 14 Nov 2026 02:10:53 GMT`
- Master response header: `Date: Sat, 14 Nov 2026 02:10:56 GMT`
- HTTP 200 for both; pinned 33,630 bytes, SHA-256 `30cde995f90d3714632e4031b3c950fb8b6c3f55f4722a855b806385852dcbbb`; master 33,711 bytes, SHA-256 `59fb4514a8de9ad98629c74468acde9572fccedcd260020725910635bc0575b4` — both byte-identical to PULL 1.
- Raw excerpt (start of both response bodies, identical):
  `{"labels": {"signUp": "Sign up", "signIn": "Sign in", "upgrade": "Upgrade", "tryExcalidrawPlus": "Try Excalidraw+", "plusFreeTrial": "14-day free trial", "paste": "Paste", "pasteAsPlaintext": "Paste as plaintext", "pasteCharts": "Paste charts", "chartType_bar": "Bar chart", ...`

### Request 5 — PULL 2 progress note

- Endpoint: `POST /v1/escrows/esc_194a1c8f898a4d6ab3da27f3/progress`
- Server-recorded time: `2026-11-14 02:15:52.307662+00` (24 h 5 m after the PULL 1 note)
- Note text: "PULL 2: Pulled both Excalidraw en.json sources from raw.githubusercontent.com. Master (...), source time Sat, 14 Nov 2026 02:10:56 GMT (HTTP Date header). Pinned commit 7ded689... (...), source time Sat, 14 Nov 2026 02:10:53 GMT. Flattened master into excalidraw-master-strings-pull2.csv: 645 data rows plus header, SHA-256 3e64604f8d616c994289028e31dc69096f735eb7688c7743b5926fcfe6462784. No change since PULL 1: 0 added, 0 removed, 0 changed; both raw responses byte-identical to PULL 1. Raw responses of both pulls saved. Independent second-model review of translations, glossary and report complete, no fixes required. Deliverable ready for buyer review."
- Delivery note: the POST response itself was lost (server closed the connection without responding), but a follow-up GET of the escrow confirmed the platform record holds a second `progress_note` event at `2026-11-14 02:15:52.307662+00` and the abandon clock reset (silent, due 2026-11-15T02:15:52Z). No re-POST was made, to avoid a duplicate note. Full record in `pull2-note-response.json` and `pull2-escrow-get.json`.

## 10. Independent second-model review (PULL 2)

A second-model review pass over D4 (`translations.jsonl`, all 642 rows read), D5 (`glossary.csv`) and this report, re-running every mechanical check from section 8 from scratch plus a full read of every translation and a glossary-consistency sweep over 17 high-frequency terms:

- **Completeness:** 642 rows, key paths in pinned file order, English field matches the pinned file on all rows except the 10 line-break rows, where the English field stores the break as the two characters `\n` per the job rule (same as the CSVs) — not a defect.
- **Placeholders:** 51 occurrences, 0 mismatches. **Tags:** 50 occurrences, 0 mismatches. **Product name:** 20 occurrences per row, 0 mismatches. **Line breaks:** literal `\n` counts in English and Swahili match on all 642 rows (10 rows affected). **Key names:** all ten `keys.*` rows byte-identical; `{filename}` preserved.
- **Glossary:** 193 terms, sorted, every "times used" count reproduces exactly from `translations.jsonl`, every term used at least once. The consistency sweep flagged 63 rows across the 17 terms; every one judged a false positive on inspection (plural forms `vipengele`/`michoro`, noun form `uteuzi` for "selection", verb forms `kuchora`/`unganishwa`, the documented adjective `wazi` for "open" in `labels.sidebarLock`, and `link`/`export`/`file` occurring only inside a tag name, a placeholder, or the `{filename}` token).
- **Translation quality:** all 642 rows read; natural, short interface Swahili, consistent with the glossary throughout. No mistranslation found.
- **Observations (not fixes — the original choices are defensible, so nothing was changed):** `labels.hachure` and `labels.zigzag` stay untranslated while sibling fill styles (`Solid` → `Thabiti`, `Cross-hatch` → `Mistari ya msalaba`) are translated — both are pattern names/loanwords with no settled Swahili equivalent. `colors.transparent` → `Wazi` (acceptable; `Angavu` would be more precise). `imageExportDialog.label.padding` → `Padingi` (loanword; no standard Swahili equivalent).
- **Result:** no fixes required; no file changed by the review. The deliverable stands as translated.
