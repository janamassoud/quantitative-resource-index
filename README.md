# Quantitative Language Resource Index

A quantitative index of NLP resource availability across more than 8,000 languages, combining coverage of multilingual corpora, language models, benchmarks, parallel (translation) data, grammars, and dictionaries into a single, weighted composite score.

This repository contains the underlying data and an interactive explorer for browsing and re-weighting the results.

---

## Contents

| Path | Description |
|---|---|
| `composite_resource_scores.csv` | List of languages with composite scores and per-dimension raw counts. |
| `Quantitative_Language_Resource_Index.html` | Interactive score explorer with the sources and calculation documentation. |
| `glottolog_lang_metadata.csv` | Base list of languages (ISO 639-3 and Glottocode) that the index is computed over, sourced from [Glottolog](https://glottolog.org). |
| `corpus_langs/` | Per-corpus CSVs listing which languages each multilingual corpus (FineWeb2, MADLAD400, HPLT, NLLB, CC-100, mC4, WikiMatrix, OpenSubtitles, ROOTS) covers. |
| `model_langs/` | Per-model CSVs listing which languages each multilingual model (Apertus, mmBERT, Omnilingual MT, Glot500-m, NLLB, PaLM, XGLM, mT5, XLM-R, BLOOM, PolyLM) covers. |
| `benchmark_langs/` | Per-benchmark CSVs listing which languages each multilingual benchmark (Flores200, ALM-bench, XTREME, MMLU-ProX, BenchMAX, MAPS) covers. |
| `parallel_data_counts/` | Per-language counts of OPUS parallel corpus coverage, translation partners, and sentence pairs. |
| `grammar_dictionary_counts/` | Per-language counts of grammars and dictionaries listed in Glottolog's bibliographic references. |

---

## The Composite Score

For every language, a composite score $S \in [0, 100]$ is computed as a weighted combination of six resource dimensions:

$$S = 100 \cdot \frac{\sum_{r \in R} w_r \cdot n_r}{\sum_{r \in R} w_r}$$

where $R = \{\text{Grammars, Dictionaries, Corpora, Models, Benchmarks, Parallel Data}\}$, $w_r$ is the weight assigned to dimension $r$, and $n_r \in [0,1]$ is that dimension's normalized score.

For the five count-based dimensions, normalization is log-compressed against a fixed maximum $M_r$, so that the marginal difference between small counts matters more than the same difference between large ones, and no single dimension can be inflated beyond its fair share by extreme outliers:

$$n_r = \min\!\left(1,\ \frac{\ln(1 + c_r)}{\ln(1 + M_r)}\right)$$

Parallel Data arrives already normalized on a 0–100 scale (derived from OPUS corpus coverage — how many corpora include a language, how many translation partners it has, and total sentence pairs), so it is rescaled linearly instead:

$$n_r = \min\!\left(1,\ \frac{c_r}{100}\right)$$

**Default weights** (configurable in the explorer):

| Dimension | Weight | Maximum ($M_r$) |
|---|---|---|
| Models | 6 | 12 |
| Corpora | 5 | 9 |
| Benchmarks | 5 | 9 |
| Parallel Data | 4 | (already 0–100) |
| Grammars | 2 | 30 |
| Dictionaries | 2 | 20 |

---

## Interactive Explorer

An HTML tool is included for browsing the index without writing any code (open it directly in a browser):

- **Adjust weights** for each dimension live and watch every language's score recalculate.
- **Search and sort** the full ranked table.
- **Long Tail chart** see where a language sits relative to the full distribution, ranked by composite score or by any individual dimension.
- **Resource Profile** a per-language wedge chart breaking down its score across all six dimensions.
- **Data Sources** and **Calculation Approach** tabs documenting exactly what feeds into the index and how.

---

## Data Sources

The index draws on the following resource types. Full citations for every individual corpus, model, and benchmark are listed in the explorer's **Data Sources** tab.

- **Multilingual corpora**: FineWeb2, MADLAD400, HPLT, NLLB, CC-100, mC4, WikiMatrix, OpenSubtitles, ROOTS
- **Multilingual models**: Apertus, mmBERT, Omnilingual MT, Glot500-m, NLLB, PaLM, XGLM, mT5, XLM-R, BLOOM, PolyLM
- **Multilingual benchmarks**: Flores200, ALM-bench, XTREME, MMLU-ProX, BenchMAX, MAPS
- **Parallel corpora**: OPUS (translatewiki, MultiCCAligned, GNOME, QED, TED2020, Tatoeba, bible-uedin, wikimedia, KDE4)
- **Traditional documentation**: Grammar and dictionary counts from [Glottolog](https://glottolog.org)'s References bibliography

---

## Limitations

- The index measures **presence**, not **quality**. A language counted as covered by a corpus, model, or benchmark says nothing about the amount, cleanliness, or representativeness of that inclusion.
- Coverage reflects what is **publicly documented and disclosed** at the time of collection; models and corpora that do not report language coverage were necessarily excluded.
- The weighting scheme reflects one general-purpose configuration; other use cases (e.g. machine-translation-specific applications) may require different weights, which are adjustable interactively on the interactive explorer.
