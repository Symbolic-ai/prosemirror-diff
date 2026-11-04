# prosemirror-diff

Structural diffing for [ProseMirror](https://prosemirror.net/) documents: given two
documents, it walks both node trees and (via `diff-match-patch`) their text to produce a single
document whose changed text carries diff marks — used to render an inline before/after view of
what changed between two versions.

## About this fork

This repository is **Symbolic's fork**, maintained under the [`Symbolic-ai`](https://github.com/Symbolic-ai)
organization so the Symbolic app does not depend on an individual's personal GitHub account for a
build-critical package.

Fork chain:

- [`hamflx/prosemirror-diff`](https://github.com/hamflx/prosemirror-diff) — the original.
- [`alexgaribay/prosemirror-diff`](https://github.com/alexgaribay/prosemirror-diff) — added
  word-level diffing (see below). This fork was made from tag `v0.0.11`.
- **`Symbolic-ai/prosemirror-diff`** (this repo) — maintained by Symbolic and consumed by the
  monorepo at an immutable commit SHA. It skips zero-length diff segments before constructing
  ProseMirror text nodes: `diff-match-patch` can emit these during line-mode cleanup, while
  ProseMirror rejects empty text nodes.

MIT licensed, per the upstream `package.json`. Attribution to the original author (`hamflx`) and
the fork author (Alex Garibay) is preserved.

## What differs from the original (`hamflx/prosemirror-diff`)

The meaningful difference lives in the `alexgaribay` fork this repo is based on: it adds
**word-level diffing**. The original only diffs at the character level.

- `patchDocumentNode`, `patchTextNodes`, and `diffEditor` accept an options object with
  `{ level: 'char' | 'word' }` (the original takes no options and is always character-level).
- Two new exports back the word path: `tokenizeText` (word/whitespace/punctuation tokenizer) and
  `diffWordsWithDetail` (word-aware diff).
- `createDiffMark` accepts the diff `level`, so each emitted diff mark records whether it was a
  character- or word-level change.

Concretely, with `level: 'word'` a change like `emergiu → surgiu` is highlighted as a whole
replaced word rather than as individual changed characters — which reads far better for prose.

## API

Exports from `src/index.js` include `diffEditor`, `patchDocumentNode`, `patchTextNodes`,
`DiffType` (`Unchanged` / `Deleted` / `Inserted`), `tokenizeText`, and `diffWordsWithDetail`.
Runtime dependencies: `diff-match-patch` and `prosemirror-model`.

## Upstream roadmap (from the original, not yet implemented)

The original README listed these as unchecked goals; they remain unimplemented here:

- [ ] Preserve marks from the new version's state.
- [ ] Add line-level similarity detection.
