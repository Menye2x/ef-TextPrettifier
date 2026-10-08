# .ef Text Prettifier

> A lightweight, browser-based writing tool that turns a personal bracket-based `.ef` syntax into a readable visual document, with Markdown conversion and optional password-protected encryption.

## Concept

`.ef Text Prettifier` is built around a simple idea: writing markup should feel closer to typing ordinary text than writing traditional HTML.

The `.ef` format uses readable bracket-like markers such as `(b)_'text'`, `< item | item >`, and `[ parent >(child) ]` to create formatting, lists, hierarchy, links, containers, tables, memos, and case transformations.

The application keeps the main writing experience intentionally simple:

```text
Editor (.ef)                  Preview
┌──────────────────────┐     ┌──────────────────────┐
│ plain text + .ef     │  →  │ formatted document   │
│ syntax                │     │ live preview         │
└──────────────────────┘     └──────────────────────┘
```

A second workspace provides optional `.ef` encryption/decryption without changing the normal plaintext export flow.

## Main Features

### `.ef` editor + live preview

- Two-column main view: **Editor (.ef)** and **Preview**.
- Normal text remains plain in the editor.
- `.ef` syntax is syntax-highlighted to make the custom bracket grammar easier to read.
- Syntax highlighting emphasizes meaningful `.ef` brackets/markers instead of coloring ordinary punctuation in normal prose.
- Responsive layout collapses to a single column on smaller screens.
- Editor panel height can be resized with the corner grip or keyboard arrows.

### Presets & Guides

The built-in **Presets & Guides** are the reference grammar for the format. They provide examples, explanations, a rendered preview, and a Markdown conversion example.

The current preset set covers:

| Preset | Purpose | Example |
|---|---|---|
| Title & Text Styles | Headings and text hierarchy | `(h1)_'Title'` |
| Text Marks | Inline formatting and nesting | `(b)_'bold (i)_'italic'''` |
| Neutralize | Literal text that cannot trigger `.ef` parsing | `#text#` |
| Quote | Block quote / inline quote | `(")_'Quote here'` |
| Dividers | Horizontal rules | `---` / `---2x` |
| Table | Header + rows between `***` markers | `*** ... ***` |
| Lists | Unordered / ordered lists | `< a \| b \| c >` / `< a ; b ; c >` |
| Link | Clickable links | `<(url)_(label)>` |
| Tree | Nested hierarchy | `[(Parent)>(Child, (Sub)>(Leaf))]` |
| Container & Button | Content container or link buttons | `[ text ]` |
| Memo | Editable memo/clip block | `*!* 'text' *` |
| Letter Case | Upper/lower/title/sentence conversion | `(ABC)_'text'` |

## `.ef` Grammar Reference

### Text styles

```text
(h1)_'text'      Title / headline
(h2)_'text'      Subtitle / subhead
(h3)_'text'      Body text style
(h4)_'text'      Caption
(h5)_'text'      Footnote
```

### Inline marks

```text
(b)_'bold'
(i)_'italic'
(u)_'underline'
(-)_'strikethrough'
(")_'quote'
```

Nested formatting is supported, so one format can contain another format as a child/subchild.

### Neutralize

```text
#text#
```

Everything inside `# ... #` is treated as literal text and is protected from `.ef` parsing. Brackets, separators, list syntax, tree syntax, and other `.ef` markers inside a neutralized block remain literal.

The `#` markers stay visible in the source editor but are hidden in the rendered preview.

### Dividers

```text
---
---2x
```

### Tables

```text
***
| Feature | Status |
| Editor | Ready |
| Preview | Live |
***
```

The first row is treated as the table header.

### Lists

```text
< Apple | Orange | Pear >
< First ; Second ; Third >
```

- `|` = unordered list
- `;` = ordered list

The selection helper can detect comma-separated selections such as `object1, object2, object3` and offer the corresponding list format automatically.

### Links

```text
<(https://example.com)_(Example)>
```

Allowed link targets in the renderer include `http`, `https`, `mailto`, and `#` targets.

### Tree / hierarchy

```text
[(Parent)>(Child 1, Child 2, (Child 3)>(Sub-child 1, Sub-child 2))]
```

The tree syntax is designed for parent → child → subchild structures and can be nested deeply.

### Container and buttons

```text
[ A note inside a container ]
[ <(https://example.com)_(Open Example)> ]
```

A container containing only links renders those links as action-style buttons.

### Memo

```text
*!* 'A short memo or note.' *
```

Memo blocks are editable in the preview and include a copy action.

### Letter case

```text
(ABC)_'make me loud'
(abc)_'MAKE ME QUIET'
(Abc)_'make me a title'
(Abcd)_'make me. a sentence'
```

## Selection Helpers

Selecting meaningful text in the editor can expose the **Set as** popover.

Available formatting can include:

- text styles and inline marks;
- neutralize;
- quote;
- letter-case modes;
- detected unordered/ordered list conversion;
- Memo Clipper for long text or long keywords.

Whitespace-only or empty selections do not open the `Set as` helper.

### Memo Clipper

Long selections or long keywords can trigger a Memo Clipper helper that prepares a ready-to-use:

```text
*!* 'selected text' *
```

The generated memo can be applied to the editor or copied directly.

## Undo / Redo

The editor maintains a compact editing history:

- up to **10 Undo** steps;
- up to **10 Redo** steps;
- `Ctrl/Cmd + Z` for Undo;
- `Ctrl/Cmd + Shift + Z` or `Ctrl/Cmd + Y` for Redo;
- formatting actions from **Set as** are recorded as editing steps;
- new edits after Undo clear the Redo branch.

## Markdown Conversion

`.ef Text Prettifier` supports bidirectional conversion:

```text
.ef → Markdown
Markdown → .ef
```

The main editor's **Export .md** creates a Markdown representation. **View .md** opens a popup containing the live converted Markdown and provides copy/export actions.

Standard Markdown is used where there is a direct equivalent. `.ef`-specific constructs use HTML/metadata markers where necessary so they can round-trip back to `.ef` without silently losing structure.

Examples of preserved `.ef`-specific constructs include:

- h3/h4/h5 text styles;
- nested tree data;
- containers and button containers;
- memo blocks;
- the double divider;
- neutralize blocks;
- letter-case markers.

## Encryption & Decryption

The **Encrypt & Decrypt** workspace is optional. Normal Editor export remains plaintext/readable.

### Plain export vs encrypted export

**Editor workspace**

```text
Export .ef  → readable plaintext .ef
Export .md  → regular Markdown
```

**Encrypt & Decrypt workspace**

```text
Import/Generate from editor text
        ↓
set password
        ↓
Encrypt & prepare files
        ↓
Encrypted .ef + credential note
        ↓
optional ZIP containing both
```

The encrypted export is separate from the normal readable `.ef` export.

### Cryptographic design

The current implementation uses browser-native cryptography:

- **AES-256-GCM** encrypts the document payload.
- A fresh random **256-bit Content Encryption Key (CEK)** is generated for each encryption operation.
- A fresh **12-byte IV** is used for AES-GCM encryption and key wrapping operations.
- The password is processed with **PBKDF2-SHA-256** using a random **16-byte salt** and **300,000 iterations**.
- The derived password key wraps the CEK instead of directly encrypting the whole document.
- A separate random **256-bit Persistent Recovery Key** also wraps the same CEK.
- Both password and Recovery Key are therefore independent ways to unlock the same CEK.
- The encrypted `.ef` file does **not** store the plaintext password.
- Cryptographic operations run locally through the browser's **Web Crypto API**; no server is required for the encryption operation.

### Persistent Recovery Key

Each encrypted file receives one persistent Recovery Key.

The same Recovery Key can be reused repeatedly to:

- decrypt the imported encrypted `.ef`;
- reset the password;
- create a new password-wrapped CEK without re-encrypting the document payload.

Resetting the password keeps the Recovery Key unchanged.

### Credential note

After encryption, the app can create a separate credential note containing:

- the encrypted filename;
- the password chosen by the user;
- the persistent Recovery Key.

This note is intentionally separate from the encrypted `.ef` file.

> **Security note:** the credential note contains secrets in readable form. A ZIP that contains both files is a convenience package, not a secure secret vault. Anyone who obtains that ZIP also obtains the password and Recovery Key.

### ZIP export

**Export ZIP (2 files)** creates one ZIP containing exactly:

```text
<document>.ef
<document>.password-recovery.txt
```

The ZIP is generated locally without requiring a server-side packaging service.

## Encrypted File Import Flow

The **Import .ef file** area handles both readable and encrypted files.

### Import encrypted `.ef`

1. The encrypted file is detected as `EF-SECRET/1`.
2. The user is sent to **Encrypt & Decrypt → Import .ef file**.
3. A password popup appears first.
4. Up to **3 password attempts** are allowed.
5. After 3 failures, the Recovery Key reset workflow is offered.
6. Successful decryption opens the document in **Read Only Mode**.
7. The imported plaintext is not inserted into the main editor automatically.

### Read Only Mode

Read Only Mode is a separate snapshot of the imported/decrypted file.

It does not mirror later edits made in the main Text Editor.

The release action:

```text
Release Encryption | add to text editor
```

copies the decrypted snapshot into the main editor.

After release, the same button becomes:

```text
Go to text editor
```

which returns directly to the main Editor tab.

Re-importing another encrypted file resets the release state back to the initial **Release Encryption** action.

### Import readable `.ef` from Encrypt & Decrypt

If a normal plaintext `.ef` file is imported into the Encrypt & Decrypt workspace, the app shows:

```text
Oops! Already Decrypted
```

The user can then choose **View in Text Editor** to send the readable document to the main editor.

## Password UX

- Minimum password length: **5 characters**.
- Password and confirmation fields display live character counters.
- The Encrypt & prepare files button remains unavailable until the source text is present and both passwords satisfy the minimum and match.
- Invalid password state is shown in red.
- Valid password state is shown in green.
- Password fields include Font Awesome eye / eye-slash toggles.

## Workspace Controls

### Empty Workspace

The Encrypt & Decrypt workspace includes **Empty Workspace**.

It clears the encryption/import workspace after confirmation, including imported files, encrypted package state, password fields, Recovery Key state, and crypto workspace text.

The main Text Editor document is intentionally left unchanged.

## Sample

The **Sample** action loads a compact application concept that demonstrates every built-in preset/format so the grammar can be explored from one document.

## Accessibility & UI Notes

- Syntax colors are selected for strong contrast against the editor background.
- Primary action colors use readable foreground/background combinations.
- Focus states are visible for interactive controls.
- Responsive layouts are provided for smaller screens.
- Popovers and modals use high stacking order so important information is not hidden behind the workspace.

## Runtime & Dependencies

The application is designed as a single HTML file:

```text
index.html
```

No build process is required.

The current HTML references **Font Awesome 6.7.2** from the jsDelivr CDN for icons. All editor, Markdown conversion, encryption, password handling, and ZIP generation logic is implemented in the HTML file itself.

## Running Locally

Because this is a client-side single-file app, it can be opened directly in a modern browser. A local HTTP server is also suitable for development and testing.

Example repository structure:

```text
.
├── index.html
├── README.md
└── .gitignore
```

## Privacy Model

The application's core document editing and cryptographic operations run in the browser. The app does not require a backend server for document rendering, Markdown conversion, encryption, or decryption.

Do not confuse this with absolute protection against reverse engineering: because the application is a client-side HTML application, another developer can inspect its source and reproduce the file format. The security boundary is the cryptographic secret (password and/or Recovery Key), not obscurity of the `.ef` format.

## Recovery Rule

The password itself is not recoverable from the encrypted `.ef` file.

If both are lost:

```text
Password lost + Recovery Key lost
                ↓
      encrypted plaintext cannot be recovered
```

Keep the persistent Recovery Key and credential note in a secure location.

## License

No license is defined by the application source unless a separate repository license file is added. Add a license file before publishing the project for reuse under specific legal terms.
