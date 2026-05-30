# AI + NLP · darija-nlp-resources

> Curated list of public corpora, papers, tools, and benchmarks for Moroccan Arabic (Darija) NLP — the operating language behind [jak.ma](https://jak.ma).

[![Live](https://img.shields.io/badge/used_in-jak.ma-brightgreen)](https://jak.ma) [![Language](https://img.shields.io/badge/Darija-Moroccan_Arabic-orange)]() [![License](https://img.shields.io/badge/license-CC--BY--4.0-blue)]()

---

## Why this list

Moroccan Arabic (Darija) is the operating language of ~37 million people but is severely under-resourced in mainstream NLP infrastructure. Most "Arabic NLP" work targets Modern Standard Arabic (MSA), which Moroccan users do not actually use for service-discovery, day-to-day messaging, or marketplace transactions.

This repo is the curated reading + tooling list I built while shipping [jak.ma](https://jak.ma) — a live Moroccan marketplace running grounded retrieval in Darija. If you're starting a Darija NLP project, start here.

---

## Public corpora

### Curated multi-dialect Arabic corpora that include Darija

- **PADIC** (Parallel Arabic Dialect Corpus) — multi-dialect Arabic including Maghrebi varieties. Useful for cross-dialect alignment.
- **MADAR** (Multi-Arabic Dialect Applications and Resources) — 25 city-level Arabic dialects across 25 Arab cities. Includes Rabat, Casablanca, Fes for Morocco.
- **OSACT** (Open-Source Arabic Corpora and Processing Tools) — repeating shared task with Arabic dialect tracks.
- **TUNIZI** — Tunisian Latin-script Arabic. Code-switching patterns transfer to Darija Arabizi.

### Darija-specific projects

- **DODa** (Darija Open Dataset) — community-curated Darija dictionary mapping to MSA + English.
- **MADAR-26** — Rabat + Casablanca + Fes subsets, sentence-level transliteration.
- **Maghrebi-Arabic-NLP** — community resource list for Maghreb dialects (Algeria, Tunisia, Morocco).

### Script-mixed (Arabizi) corpora

Latin-script Darija ("Arabizi") is ~30% of jak.ma's user-generated text. Resources are sparser:

- Twitter-mined Arabizi datasets (search arxiv for "Arabizi" + year)
- Tunisian Arabizi corpora (TUNIZI) — closest analog
- Hand-curated jak.ma worker descriptions (sample dataset in [jak-ma-eval-suite](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa))

---

## Papers worth reading

(Curated. Not exhaustive.)

- **"AraBERT"** (Antoun et al., 2020) — baseline Arabic BERT. Performance gap between MSA and dialect is the gap you have to close.
- **"DARI"** (Habash et al.) — Maghreb-specific morphological analysis.
- **"MARBERT"** and **"ARBERT"** (Abdul-Mageed et al., 2021) — pre-trained on social media Arabic, better dialect coverage than AraBERT.
- **"AraT5"** and **"AraGPT2"** — generative Arabic models. Test on Darija before assuming coverage.
- **"Multi-dialect Arabic BERT for Understanding Dialect Code-Switching"** — directly relevant for Arabic↔Arabizi switching.
- **xAI Grok technical reports** — multilingual coverage notes; informs why jak.ma uses Grok-3-mini for live inference.

---

## Tools & pipelines

### Tokenization

- **CAMeL Tools** — strongest open-source Arabic NLP toolkit. Morphological analyzer, dialect ID, tokenizer.
- **SentencePiece** — BPE for cross-script. Necessary for handling Arabic ↔ Arabizi.
- **Farasa** — Arabic segmentation + diacritization (MSA-heavy but useful baseline).

### Transliteration

- **Arabizi → Arabic transliterators** — Buckwalter scheme + neural extensions. None are perfect for Darija; expect to fine-tune.
- **rule-based mapping tables** — for the common Darija words (kanбhdem, b3it, etc.), a 200-entry lookup beats neural transliteration for cost.

### Fine-tuning infrastructure

- **Hugging Face PEFT (LoRA)** — what we use in the [jak-ma-eval-suite](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa) fine-tune scaffolding.
- **Modal / RunPod / Vast.ai** — affordable single-A100 hosts for one-off LoRA runs.

---

## Benchmarks

- **ARLUE** (Arabic Language Understanding Evaluation) — GLUE analog. Has Maghrebi tasks.
- **ALUE** (Arabic Language Understanding Evaluation) — Sentence-level tasks. Dialect splits exist.
- **DART** — Arabic dialect identification benchmark.

### jak.ma-specific (open-sourced)

The 50-prompt held-out test set for jak.ma's grounded retrieval is in [jak-ma-eval-suite/DARIJA_QUERY_SET.md](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa/blob/main/DARIJA_QUERY_SET.md). Production-validated, scores ≥0.92 aggregate as of May 2026.

---

## What's missing in the public ecosystem

If you're starting fresh, here's the gap that will hurt you:

1. **High-quality Arabizi parallel data.** Mostly community-mined, mostly Tunisian. Build your own from your domain.
2. **Trade-specific or domain-specific vocabularies.** Generic Darija corpora won't have بلومبي (plumber), كلامبيستري (tiler), حدادة (welder) in useful frequencies.
3. **Calibrated evaluators.** Most public Darija evaluation is one-shot human rating with no calibration protocol. Build your rubric early (see [ernie-evaluation-notes](https://github.com/Samielakkad/AI-LLM-Evaluation-Baidu-ERNIE) for methodology).
4. **Pricing / numeric reasoning in Darija.** Almost none. Sami's deterministic rule engine in jak.ma is one of the few production-validated solutions.

---

## How to contribute

PRs welcome from anyone working on Maghrebi NLP. Add corpora, papers, tools that have been useful in YOUR work. Brief usage notes ≫ marketing-style descriptions.

For sensitive issues (corpus licensing concerns, PII), email `sam25@mails.tsinghua.edu.cn`.

---

## Related

- [jak.ma](https://jak.ma) — live Darija marketplace, 1,996 verified workers
- [jak-ma-eval-suite](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa) — production grounded-retrieval eval methodology
- [ernie-evaluation-notes](https://github.com/Samielakkad/AI-LLM-Evaluation-Baidu-ERNIE) — LLM eval methodology from Baidu ERNIE Mentor Program

## License

CC-BY-4.0 for the curated list. Linked resources retain their own licenses.

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · sam25@mails.tsinghua.edu.cn
