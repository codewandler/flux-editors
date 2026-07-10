# AGENTS.md

Instructions for coding agents working in — or *passing by* — this repository.
Humans: see [`README.md`](README.md). This file is the machine-actionable version.

**What this is:** editor tooling for **Flux-Lang** (`.flux`), the human-writable
text form of a [flux](https://github.com/codewandler/flux) execution graph. Two
artifacts live here; a third (tree-sitter) is a sibling repo:

| Editor family | Use | Where |
|---|---|---|
| **VS Code** | TextMate grammar | [`textmate/`](textmate/) (this repo) |
| **IntelliJ / RustRover / CLion** | TextMate bundle *or* full plugin | [`textmate/`](textmate/) or [`intellij/`](intellij/) (this repo) |
| **Helix / Neovim / Zed** | tree-sitter grammar | [`codewandler/flux-tree-sitter`](https://github.com/codewandler/flux-tree-sitter) → its `AGENTS.md` |

> The two grammars here and the tree-sitter one are kept **in sync** on token
> classes. The editor-facing vocabulary (keywords/types/ops/annotations + hover
> docs) is curated in
> [`intellij/src/main/kotlin/dev/flux/intellij/editor/FluxVocabulary.kt`](intellij/src/main/kotlin/dev/flux/intellij/editor/FluxVocabulary.kt),
> sourced from flux's
> [`crates/flux-lang/docs/syntax.md`](https://github.com/codewandler/flux/blob/main/crates/flux-lang/docs/syntax.md).
> If you add a keyword/op, update it in all grammars.

---

## Task: highlight `.flux` in the user's editor

### VS Code

```sh
# Install the grammar as a local extension (idempotent).
mkdir -p "$HOME/.vscode/extensions"
ln -sfn "$(pwd)/textmate/flux" "$HOME/.vscode/extensions/flux-lang"
# then reload VS Code (Developer: Reload Window)
```

`textmate/flux` is a complete VS Code extension (`package.json` +
`syntaxes/flux.tmLanguage.json` + `language-configuration.json`); it contributes
the `flux` language for `.flux` and colours it. No build step.

### IntelliJ / RustRover / CLion — quick (TextMate bundle)

GUI, not scriptable: **Settings ▸ Editor ▸ TextMate Bundles ▸ +**, select the
`textmate/flux` folder, then open a `.flux` file. Recolor under **Settings ▸
Editor ▸ Color Scheme ▸ TextMate**.

### IntelliJ — full plugin (build & run)

Requires **JDK 21**.

```sh
cd intellij
./gradlew runIde       # launches a sandbox IDE with the plugin loaded
./gradlew buildPlugin   # → build/distributions/Flux-Lang-<version>.zip (Install Plugin from Disk)
```

Live validation / plan preview shell out to the `fluxlang` binary; it is resolved
from `$FLUXLANG_BIN`, then `<project>/target/{debug,release}/fluxlang`, then
`$PATH`. Build it from the flux repo:
`cargo build -p flux-lang --features cli --bin fluxlang`.

### Helix / Neovim / Zed

TextMate is not read by these editors. Humans start with the canonical
[Flux editor setup guide](https://codewandler.github.io/flux/docs/language/editors); agents and
automation follow [`codewandler/flux-tree-sitter`](https://github.com/codewandler/flux-tree-sitter)
and its `AGENTS.md`, whose tested installer owns idempotent setup and updates.

---

## Task: change a grammar / the plugin

- **TextMate grammar:** edit `textmate/flux/syntaxes/flux.tmLanguage.json`. Regex
  scope matching only — no build. Verify by opening a `.flux` file.
- **IntelliJ plugin:** the real lexer is `intellij/src/main/kotlin/dev/flux/intellij/lang/FluxLexer.kt`;
  token→colour mapping is under `highlight/`. Build/run with `./gradlew runIde`.
- **Keep grammars aligned.** A keyword/op/annotation added here should also land in
  [`codewandler/flux-tree-sitter`](https://github.com/codewandler/flux-tree-sitter)
  (`grammar.js` + `queries/highlights.scm`) and in `FluxVocabulary.kt`.

## Gotchas

- These tools **shell out to `fluxlang`** for real semantics (validation, plan
  preview) — they never re-implement the parser, so they can't drift from the
  language. Don't add a second parser here.
- Some `.flux` files are the **JSON wire form** (model-emitted plans), not the
  text form; they open but highlight as generic tokens. The grammars target the
  human text form.
- The grammars highlight the **superset** (including aspirational keywords like
  `type`/`pipe`/`race`/`try`/`await`), matching authoring intent rather than only
  the strict runtime-parsed subset.

## See also

- [Flux editor setup](https://codewandler.github.io/flux/docs/language/editors) — canonical human
  onboarding and troubleshooting.
- [codewandler/flux](https://github.com/codewandler/flux) — language, runtime, `fluxlang` CLI.
- [codewandler/flux-tree-sitter](https://github.com/codewandler/flux-tree-sitter) — tree-sitter grammar.
