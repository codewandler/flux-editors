# Editor support for Flux-Lang

> Extracted from the [flux](https://github.com/codewandler/flux) monorepo (2026-06) to keep the core
> focused. These tools shell out to the `fluxlang` CLI that flux ships.

Tooling for authoring `.flux` files (the human-writable Flux-Lang text form). Pick by editor:

For user-facing installation and update instructions, start with the canonical
[Flux editor setup guide](https://codewandler.github.io/flux/docs/language/editors). This repository
owns the TextMate and IntelliJ artifacts; it stays a router for tree-sitter-based editors.

| Artifact | What | Use it when |
|---|---|---|
| [`textmate/`](textmate/) | A TextMate / VS Code grammar. Zero build. | You want color in **VS Code**, or in **IntelliJ** as a TextMate bundle. |
| [`intellij/`](intellij/) | A full IntelliJ plugin for any JetBrains IDE. | You want the complete authoring experience in **IntelliJ / RustRover / CLion**. |
| [`codewandler/flux-tree-sitter`](https://github.com/codewandler/flux-tree-sitter) ↗ | A tree-sitter grammar + queries (separate repo). | You want color in **Helix, Neovim, or Zed** — none of which read TextMate — or GitHub code view. |

The **IntelliJ plugin** is the full editor experience (see [`intellij/README.md`](intellij/README.md) for
the complete list and build/run steps):

- **Syntax highlighting** — keywords, `$symbols`, strings + `{interpolation}`, numbers, `@annotations`,
  types, `#` comments; plus a recolorable **color settings page**.
- **Live validation** — reuses the Rust `fluxlang` (`compile` for text, `render` for the JSON wire form)
  to flag parse errors as red squiggles.
- **Completion** — keywords, types, built-in ops, `@annotations`, and the `$symbols` bound in the file.
- **Live templates** — `flow`, `when`, `repeat`, `each`, `match`, `try`, `confirm`, `parallel`, `ctx`, …
- **Quick documentation** (Ctrl-Q) on keywords/nodes.
- **Folding** of indentation blocks and a **structure view** (Alt-7) of `flow`/`type` declarations.
- **Preview Plan (Tree) / Show AST (JSON)** actions that run `fluxlang` on the current file.
- **Line commenting** (`#`, Ctrl-/) and **brace matching** (`()`/`[]`/`{}`).

The **TextMate grammar** is highlighting only (regex scopes, no validation) — the fastest path to color
in VS Code / IntelliJ, and the reusable highlighting source.

The **[tree-sitter grammar](https://github.com/codewandler/flux-tree-sitter)** lives in its own repo. It is
the highlighting path for editors built on tree-sitter (Helix, Neovim, Zed), which do not read TextMate
grammars. It deliberately mirrors the same token classes as the TextMate grammar here, so the two agree.

Architecture: a thin Kotlin layer hosts the IDE features over a flat PSI; the real language semantics
(validation, plan preview) are reused from the Rust `fluxlang` toolchain, so they never drift.

> Note: some `.flux` files are the **JSON wire form** (model-emitted plans, e.g. `examples/*.flux`), not the
> text form. Those still open fine but highlight as generic tokens — the grammars target the human text form
> documented in flux's
> [`crates/flux-lang/docs/syntax.md`](https://github.com/codewandler/flux/blob/main/crates/flux-lang/docs/syntax.md).

## See also

- [Flux editor setup](https://codewandler.github.io/flux/docs/language/editors) — canonical
  onboarding, updates, verification, and troubleshooting for every editor family.
- [`AGENTS.md`](AGENTS.md) — agent-actionable setup for each editor (point your agent here).
- [codewandler/flux](https://github.com/codewandler/flux) — the language, runtime, and `fluxlang` CLI.
- [codewandler/flux-tree-sitter](https://github.com/codewandler/flux-tree-sitter) — the tree-sitter grammar
  (Helix / Neovim / Zed).
