# Subtitle Annotations

SubMiner annotates subtitle tokens in real time as they appear in the overlay. Three annotation layers work together to surface useful context while you watch: **N+1 highlighting**, **frequency highlighting**, and **JLPT tagging**.

All three are opt-in and configured under `subtitleStyle` and `ankiConnect.nPlusOne` in your config. They apply independently — you can enable any combination.

## N+1 Word Highlighting

N+1 highlighting identifies sentences where you know every word except one, making them ideal mining targets. When enabled, SubMiner builds a local cache of your known vocabulary from Anki and highlights tokens accordingly.

**How it works:**

1. SubMiner queries your Anki decks for existing `Expression` / `Word` field values.
2. The results are cached locally (`known-words-cache.json`) and refreshed on a configurable interval.
3. When a subtitle line appears, each token is checked against the cache.
4. If exactly one unknown word remains in the sentence, it is highlighted with `nPlusOneColor` (default: `#c6a0f6`).
5. Already-known tokens can optionally display in `knownWordColor` (default: `#a6da95`).

**Key settings:**

| Option | Default | Description |
| --- | --- | --- |
| `ankiConnect.nPlusOne.highlightEnabled` | `false` | Enable N+1 highlighting |
| `ankiConnect.nPlusOne.refreshMinutes` | `60` | Minutes between Anki cache refreshes |
| `ankiConnect.nPlusOne.decks` | `[]` | Decks to query (falls back to `ankiConnect.deck`) |
| `ankiConnect.nPlusOne.matchMode` | `"headword"` | `"headword"` (dictionary form) or `"surface"` (raw text) |
| `ankiConnect.nPlusOne.minSentenceWords` | `3` | Minimum tokens in a sentence for N+1 to trigger |
| `subtitleStyle.nPlusOneColor` | `#c6a0f6` | Color for the single unknown target word |
| `subtitleStyle.knownWordColor` | `#a6da95` | Color for already-known tokens |

::: tip
Set `refreshMinutes` to `1440` (24 hours) for daily sync if your Anki collection is large.
:::

## Frequency Highlighting

Frequency highlighting colors tokens based on how common they are, using dictionary frequency rank data. This helps you spot high-value vocabulary at a glance.

**Modes:**

- **Single** — all highlighted tokens share one color (`singleColor`).
- **Banded** — tokens are assigned to five color bands from most common to least common within the `topX` window.

SubMiner looks up each token's `frequencyRank` from `term_meta_bank_*.json` files. Only tokens with a positive rank at or below `topX` are highlighted.

**Key settings:**

| Option | Default | Description |
| --- | --- | --- |
| `subtitleStyle.frequencyDictionary.enabled` | `false` | Enable frequency highlighting |
| `subtitleStyle.frequencyDictionary.topX` | `1000` | Max frequency rank to highlight |
| `subtitleStyle.frequencyDictionary.mode` | `"single"` | `"single"` or `"banded"` |
| `subtitleStyle.frequencyDictionary.matchMode` | `"headword"` | `"headword"` or `"surface"` |
| `subtitleStyle.frequencyDictionary.singleColor` | — | Color for single mode |
| `subtitleStyle.frequencyDictionary.bandedColors` | — | Array of five hex colors for banded mode |
| `subtitleStyle.frequencyDictionary.sourcePath` | — | Custom path to frequency dictionary root |

When `sourcePath` is omitted, SubMiner searches default install/runtime locations for `frequency-dictionary` directories automatically.

::: info
Frequency highlighting skips tokens that look like non-lexical noise (kana reduplication, short kana endings like `っ`), even when dictionary ranks exist.
:::

## JLPT Tagging

JLPT tagging adds colored underlines to tokens based on their JLPT level (N1–N5), giving you an at-a-glance sense of difficulty distribution in each subtitle line.

**How it works:**

SubMiner loads offline `term_meta_bank_*.json` files from `vendor/yomitan-jlpt-vocab` and matches each token's headword against the bank entries. Tokens with a recognized JLPT level receive a colored underline.

**Default colors:**

| Level | Color | Preview |
| --- | --- | --- |
| N1 | `#ed8796` | Red |
| N2 | `#f5a97f` | Peach |
| N3 | `#f9e2af` | Yellow |
| N4 | `#a6e3a1` | Green |
| N5 | `#8aadf4` | Blue |

All colors are customizable via the `subtitleStyle.jlptColors` object.

**Key settings:**

| Option | Default | Description |
| --- | --- | --- |
| `subtitleStyle.enableJlpt` | `false` | Enable JLPT underline styling |
| `subtitleStyle.jlptColors.N1`–`N5` | see above | Per-level underline colors |

::: tip
JLPT tagging requires the offline vocabulary bundle. See [JLPT Vocabulary Bundle](jlpt-vocab-bundle) for setup instructions and file locations.
:::

## Runtime Toggles

All three annotation layers can be toggled at runtime via the mpv command menu without restarting:

- `ankiConnect.nPlusOne.highlightEnabled` (`On` / `Off`)
- `subtitleStyle.enableJlpt` (`On` / `Off`)
- `subtitleStyle.frequencyDictionary.enabled` (`On` / `Off`)

Toggles only apply to new subtitle lines after the change — the currently displayed line is not re-tokenized in place.

## Rendering Priority

When multiple annotations apply to the same token, the visual priority is:

1. **N+1 target** (highest) — the single unknown word in an N+1 sentence
2. **Frequency highlight** — common-word coloring
3. **JLPT underline** — level-based underline (stacks with the above since it uses underline rather than text color)
4. **Known-word color** — already-learned token tint
