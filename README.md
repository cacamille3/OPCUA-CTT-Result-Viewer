# OPC UA CTT Results — Single-File Viewer

A zero-dependency, single-file HTML viewer for **OPC UA Compliance Test Tool (UACTT)** result files (`*.results.xml` / `*.ctt_result_*.xml`).

Open the `.xml` produced by a CTT run and get an instant, color-coded, collapsible view of every conformance group, unit, and test case — with a real pass/fail summary. No install, no server, no internet. Just double-click the HTML file.

> **Privacy:** everything runs locally in your browser via the File API. Your result files never leave your machine and are never uploaded anywhere.

<!-- Add a screenshot to make the README pop:
![Screenshot](docs/screenshot.png)
-->

---

## Features

- 🗂️ **Full result tree** — every `ResultNode` rendered as an indented, **collapsible** table (conformance groups → units → test cases → details).
- 🎨 **Color-coded by result** — Error, Warning, Recommendation, Not Implemented, Skipped, Not Supported, OK, Backtrace — each with its own badge and a left-accent bar.
- 🔢 **Honest summary counts** — the pill totals count **real test cases only**, not the Warning/Error/Backtrace detail sub-nodes that would otherwise inflate the numbers.
- 🖱️ **Click-to-filter** — click any summary pill (or a test case's result badge) to show only test cases with that result. Click again to clear.
- 🔎 **Live search** by node name, plus quick toggles for **hide Backtrace** and **only errors/warnings**.
- 🧭 **Global result banner** — the overall run result and the detected file format, at a glance.
- 🔀 **Automatic format detection** — transparently handles result files from **different CTT tool versions** that use different internal result codes (see below).
- 📦 **Single file, zero dependencies** — pure HTML/CSS/vanilla JS. Works offline in any modern browser.
- 🖐️ **Drag & drop** — drop an `.xml` anywhere on the window to load it.

---

## Usage

1. Open the page in your web browser
2. **Double-click** it (opens in Chrome, Edge, or Firefox), or serve it however you like.
3. Click **Choose File** and pick your CTT result `.xml` — or **drag the file onto the window**.

That's it. Nothing is installed and nothing is transmitted.

---

## Supported result formats

UACTT result files from different tool versions use **different `testresult` numeric codes** for the same meaning. The viewer auto-detects the format and maps codes to a shared set of categories, so the labels, colors, and filters are always correct.

| Category | "new" format<br>(UACTT 2.x, has `groupkey`) | "legacy" format<br>(older tool, no version attrs) |
|---|:---:|:---:|
| Error | 0 | 0 |
| Warning | 1 | 1 |
| Recommendation | 2 | — |
| Not Implemented | 3 | 2 |
| Skipped | 4 | 3 |
| Not Supported | 5 | 4 |
| OK | 6 | 5 |
| Backtrace | 7 | 6 |

**Detection order:** the root node's `binaryversion` / `scriptversion` / `specificationversion` attributes → presence of `groupkey` → otherwise the numeric code on a `Backtrace` node. The detected format is shown in the banner (`… · format: new | legacy`).

---

## How "test cases" are counted

The summary deliberately counts only **executed test cases**, not container or detail nodes:

- **new format:** nodes carrying both `groupkey` and `unitkey`.
- **legacy format:** nodes whose name ends in `.js`.
- In both, the per-unit **setup/teardown** scripts (`initialize.js`, `cleanup.js`, `beforeTest.js`, `afterTest.js`) are excluded.

This is why the pill totals (e.g. `302 test cases`) are much smaller than the raw node count — the Backtraces and Warning/Error detail children are shown in the tree but not counted as test cases.

---

## Result categories

| Badge | Meaning |
|---|---|
| 🟥 **Error** | Test case failed |
| 🟧 **Warning** | Passed with a warning |
| 🟨 **Recommendation** | Spec recommendation not followed |
| 🟪 **Not Implemented** | Test not implemented in the tool |
| ⬜ **Skipped** | Test skipped |
| 🟦 **Not Supported** | Feature not supported by the server |
| 🟩 **OK** | Passed |
| ▪️ **Backtrace** | Script location detail (hidden by default) |

---

## Tech

Pure web platform — **no frameworks, no build step, no dependencies**:

- HTML5 + CSS3 (custom properties, flexbox, sticky headers)
- Vanilla JavaScript (ES6+)
- `DOMParser` for XML, `FileReader` + File API for local file loading, Drag-and-Drop API

Everything lives in one `.html` file, so it's trivially portable — copy it anywhere and open it.

---

## Notes & limitations

- The legacy-format code map for **Error** and **Recommendation** is inferred from the shift pattern of the observed codes; result files that actually contain those rows are the only way to fully confirm them. Everything observed in real files is correct. If a legacy file ever mislabels an Error row, it's a one-line change in the `SCHEMES.legacy` map near the top of the `<script>`.
- Result-code meanings and colors are all defined in two small objects (`CAT` and `SCHEMES`) at the top of the script — easy to tweak or extend for other CTT variants.
- Tested with UACTT 1.04 result files (both the 2.x tool output and older `ctt_result_*.xml` output).

---

## Tip: comparing runs

Because the summary counts real test cases per group, it's handy for spotting **coverage gaps** between runs — e.g. a run missing an entire service set (Subscription, Monitored Item, Method) will show far fewer test cases even though the shared groups match. Load each file and compare the pill totals.

---

## License

[MIT](https://opensource.org/licenses/MIT)

---

## Contributing

Issues and PRs welcome. Since it's a single file with no build, contributing is as simple as editing `index.html` and opening it in a browser.
