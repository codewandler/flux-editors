# Flux-Lang TextMate grammar (Tier 0)

Instant syntax highlighting for `.flux` files with **no build step**. Same grammar works in IntelliJ (as a
TextMate bundle) and VS Code (as an extension).

## IntelliJ / RustRover / CLion

1. **Settings ▸ Editor ▸ TextMate Bundles ▸ +**
2. Select the folder `textmate/flux`
3. Open any `.flux` file — keywords, `$symbols`, strings, `{interpolation}`, `@annotations`, numbers, and
   `#` comments are colored.

The IDE recognizes `.flux` from the bundle's `package.json` (`extensions: [".flux"]`). Recolor scopes under
**Settings ▸ Editor ▸ Color Scheme ▸ TextMate**.

## VS Code

Copy or symlink `textmate/flux` into `~/.vscode/extensions/` (or open it and run *Extensions: Install
from Location*), then reload. It contributes the `flux` language + grammar.

## What it highlights

Mirrors flux's
[`crates/flux-lang/docs/syntax.md`](https://github.com/codewandler/flux/blob/main/crates/flux-lang/docs/syntax.md):
control-flow keywords, `$symbol` refs, single/triple-quoted strings with `{symbol}` interpolation,
`@effect`/`@thing` annotations, `true|false|null`, numbers, built-in + named types, and `#` line comments.
Effect names like `read`/`delete` are intentionally *not* keywords (they are ordinary op names).

## Grammar is not a parser

This is regex-based scope matching — good enough for color, but it does not validate. For real diagnostics,
use the IntelliJ plugin (`../intellij`) and the higher tiers that shell out to `fluxlang compile`.

## Not using a TextMate editor?

**Helix, Neovim, and Zed** don't read TextMate grammars — use the tree-sitter grammar at
[`codewandler/flux-tree-sitter`](https://github.com/codewandler/flux-tree-sitter), which mirrors the same
token classes as this one.
