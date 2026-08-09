# Oracle BIP RTF Template Tag Extractor

**Live demo: [oracle-bip-rtf-template-tag-extractor.netlify.app](https://oracle-bip-rtf-template-tag-extractor.netlify.app/)**

A single-file, client-side tool for inspecting and validating **Oracle BI Publisher (BIP)** RTF templates. Drop in an `.rtf` template and it renders the document's layout (tables, paragraphs, fonts, alignment) with every BIP field shown in place as a clickable chip — plus a full validation pass for common templating mistakes.

Everything runs **100% locally in the browser**. No files are uploaded anywhere; the RTF never leaves your machine.

## Features

- **Visual, RTF-like rendering** — reconstructs tables, paragraphs, bold/italic, font size, and alignment so fields appear exactly where they sit in the source document.
- **Field chips** — each `<?...?>` tag renders as a colored chip:
  - blue = plain data field
  - amber = `for-each` loop
  - purple = `if` / `choose` conditional
  - red = has validation errors
  - orange = has validation warnings
- **Hover for a quick preview**, **click to expand** the resolved code inline, or use **Show all code** to expand everything at once.
- **Validation engine** that catches:
  - Structural issues — unclosed/mismatched `for-each`, `if`, `choose` blocks, orphan closing tags
  - Missing or circular `\docvar` references
  - Syntax errors — unclosed string literals, unbalanced parentheses/brackets, malformed `for-each`/`if` tags
  - Best-practice warnings — `format-number` without a mask, unused `set_variable`/`get_variable` pairs, `for-each-group` missing `groupBy`, deprecated `xdoxslt:msg` calls, overly long expressions, descendant-axis XPath (`//NODE`)
- **Search/filter** fields by name or code, and a one-click **copy** button for any field's resolved tag code.

## Usage

1. Open the [live demo](https://oracle-bip-rtf-template-tag-extractor.netlify.app/), or open [`index.html`](index.html) directly in any modern browser.
2. Click the drop zone or drag an `.rtf` BIP template onto it.
3. Browse the rendered document, review the validation panel, and click any field chip to inspect its underlying tag code.

No build step, no server, no dependencies to install — it's a static HTML file. The only external resource loaded is [JSZip](https://stuffit.cdnjs.com/) from a CDN (currently unused by the core flow but included for future RTF/DOCX packaging support).

## How it works

BIP templates store field definitions as hidden, base64-encoded `\docvar` blocks in the RTF, with visible placeholders in the document body referencing them by ID (`xdo0001`, etc.). This tool:

1. Parses the RTF's header tables to extract each `\docvar` definition and decode it.
2. Tokenizes the RTF body into a lightweight event stream (text, fields, table cells/rows, paragraph breaks, formatting).
3. Resolves each field placeholder to its decoded BIP syntax, recursively expanding any `ref:` chains.
4. Runs the resolved tag set through the validation rules above.
5. Renders everything back into an approximation of the original Word layout.

**Limitations:** this is an approximation of Word's rendering, not pixel-perfect. Images, exact fonts, headers/footers, list numbering, and merged cells are not fully reproduced.

## License

MIT
