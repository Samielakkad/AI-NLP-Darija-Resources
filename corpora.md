# Corpora — Public Datasets for Darija NLP Work

> Curated public corpora useful for Darija NLP. Each entry: name, what it contains, size, license, link, and a one-line "what to use it for in production."

Sort: by directness to Darija production work.

---

## Multi-dialect Arabic corpora (Moroccan subset present)

### MADAR-26 — Multi-Arabic Dialect Applications and Resources

- **Contents:** Parallel sentences across 25 Arabic city-level dialects + MSA. Includes Rabat, Casablanca, Fes subsets.
- **Size:** ~2,000 sentences per dialect × 26 dialects = ~52k sentences total.
- **License:** Research-only; contact authors for commercial use.
- **Link:** https://camel.abudhabi.nyu.edu/madar/
- **Use for:** Bootstrapping a Darija-aware tokenizer. Sentence-level alignment from MSA to Moroccan dialects.
- **Caveat:** The "Moroccan" coverage is 3 cities, all formal/literary register. Doesn't reflect WhatsApp / chat Darija.

---

### PADIC — Parallel Arabic Dialect Corpus

- **Contents:** Modern Standard Arabic ↔ 5 Maghreb dialects (Algerian, Moroccan, Tunisian, Egyptian, Syrian) parallel sentences.
- **Size:** ~6,400 sentence pairs per dialect.
- **License:** Open for research.
- **Link:** https://github.com/aribabou/PADIC (community mirror; check author page for canonical)
- **Use for:** Cross-dialect alignment. Useful as a baseline before any new model evaluation.
- **Caveat:** Bookish register. Real production Darija (jak.ma worker descriptions, customer queries) doesn't look like PADIC sentences.

---

### OSCAR (Open Super-large Crawled Aggregated CoRpus)

- **Contents:** Large-scale crawled web data. Includes ar_MA (Moroccan Arabic subset).
- **Size:** Multi-gigabyte. Moroccan-Arabic subset specifically: ~250 MB raw text.
- **License:** Per-document — varies. Mostly CC-BY or public domain.
- **Link:** https://oscar-project.org/
- **Use for:** Pre-training or continued pre-training on Darija-leaning Arabic text.
- **Caveat:** Aggregated crawl, so quality is uneven. The Moroccan-specific subset is small relative to MSA. Pre-filtering needed.

---

## Darija-specific projects

### DODa (Darija Open Dataset)

- **Contents:** Community-curated Darija → MSA + English dictionary. ~16k entries.
- **Size:** ~16,000 word/phrase entries.
- **License:** CC-BY-4.0.
- **Link:** https://darija-open-dataset.github.io/ (community project)
- **Use for:** Building a lookup-table transliterator. Augmenting tokenizers with high-frequency Darija words.
- **Caveat:** Dictionary entries, not sentence-level data. Limited use for fine-tuning a generation model.

---

### Darija Arabizi (Latin-script Moroccan)

- **Contents:** Scraped Twitter / forum data of Moroccan users writing in Latin script ("Arabizi"). Multiple community-mined sets exist; quality varies.
- **Size:** Varies. The TUNIZI dataset is the closest analog (~3k sentences); Moroccan-specific is sparser.
- **License:** Per-source.
- **Link:** Search arxiv/GitHub for "Arabizi" + year.
- **Use for:** Training a model to handle Arabizi input. Or building a Latin-script ↔ Arabic-script normalizer.
- **Caveat:** Arabizi spelling is **not standardized**. Same word can have 5+ spellings. Models need character-level robustness.

---

### Maghrebi-Arabic-NLP (community resource list)

- **Contents:** A meta-list of Maghrebi-Arabic resources (Algerian, Tunisian, Moroccan).
- **Link:** Various — search GitHub for "maghrebi arabic nlp" — community-maintained.
- **Use for:** Quick reference when you need to know what exists. Don't rely on for production training data — it's a pointer collection.

---

## Synthetic and augmented sets

### Back-translation from MSA

- **Recipe:** Take an MSA corpus → use any MSA-Darija translator (e.g., Helsinki-NLP/opus-mt-ar-fr, fine-tuned variants) → produce synthetic Darija sentences.
- **Size:** Bounded by source MSA corpus.
- **License:** Depends on source.
- **Use for:** Augmenting small Darija sets. Especially useful for getting domain-specific vocabulary (medical, legal, technical) where direct Darija corpora are sparsest.
- **Caveat:** Translation quality varies. Manually validate ~100 samples before training on the full set.

---

### jak.ma curated chat corpus (in-progress)

- **Contents:** ~2,200 PII-scrubbed Darija chat samples from jak.ma production traffic.
- **Size:** Growing. Target: 5,000–15,000 samples by end of 2026.
- **License:** Internal. Public release planned for v2 once PII pipeline is audited.
- **Where:** Not yet public. Sample format mirrored at: [jak-ma-eval-suite/data/sample_queries.jsonl](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa/blob/main/data/sample_queries.jsonl).
- **Use for:** Production-realistic Darija chat — the kind of register that DOES include code-switching, Arabizi, abbreviations, etc.

---

## Adjacent (Tunisian / Algerian) — useful as fallback

### TUNIZI — Tunisian Arabizi

- **Contents:** ~3,000 sentences of Tunisian Latin-script Arabic. Sentiment-labeled subset of ~750 sentences.
- **License:** CC-BY-NC-SA.
- **Link:** https://github.com/iCompass-ai/TUNIZI-Dataset
- **Use for:** Bootstrapping Arabizi handling. Tunisian and Moroccan Arabizi share ~70% of phonological mappings.
- **Caveat:** Tunisian-specific vocabulary won't transfer 1:1. Use as architecture proof-of-concept, not as Moroccan training data.

---

### NArabizi (North-African Arabizi)

- **Contents:** Cross-North-African Arabizi tokenizer + small parallel corpus.
- **Link:** Community projects on GitHub.
- **Use for:** Character-level normalization baselines.

---

## What's missing in the public corpora ecosystem

If you're starting a Darija NLP project in 2026, here's the gap that will hurt you:

1. **High-quality Arabizi parallel data.** No standardized Moroccan Arabizi-to-Arabic parallel corpus exists at scale. You'll need to build your own.
2. **Trade-specific or domain-specific vocabularies.** General Darija corpora won't have بلومبي (plumber), كلامبيستري (tiler), حدادة (welder) in useful frequencies. Build your own domain lexicon.
3. **Calibrated evaluators.** Most public Darija evaluation is one-shot human rating with no calibration protocol. The methodology in [ernie-evaluation-notes/calibration_practice.md](https://github.com/Samielakkad/AI-LLM-Evaluation-Baidu-ERNIE/blob/main/calibration_practice.md) is what closes this gap.
4. **Pricing / numeric reasoning in Darija.** Almost zero public data. Sami's deterministic rule engine in jak.ma is one of the few production-validated solutions.
5. **Production-realistic chat register.** Most corpora are formal/literary. Chat Darija is shorter, code-switched, with abbreviations. Production logs (PII-scrubbed) are the only good source.

---

## Recipe — building a Darija training set from scratch

If you have no prior data and need to start:

1. **Pull OSCAR ar_MA** (~250 MB) → pre-filter for dialect markers vs. MSA markers → keep ~100 MB of dialect-leaning text.
2. **Add MADAR-26** for parallel anchor sentences (sentence-level pairs).
3. **Add DODa** as a lookup table for word-level normalization.
4. **Use back-translation** from MSA to augment ~20k synthetic dialect sentences.
5. **Scrape your domain** (e.g., service-marketplace logs, Twitter for your domain) → PII-scrub → manually review 200 samples.
6. **Eval against MADAR-26 held-out** + your own 50-query Darija test set (see [jak-ma-eval-suite](https://github.com/Samielakkad/AI-LLM-Evaluation-JakMa)).

This is roughly the path jak.ma used. Total time: ~2 months at part-time work. Total cost: ~$100 in cloud compute for the back-translation step.

---

## Licensing summary

- **MADAR-26, PADIC, DODa, TUNIZI:** Research-friendly, generally CC-BY or compatible.
- **OSCAR:** Per-document — Most CC-BY or public domain, but check on a per-document basis for commercial deployment.
- **Synthetic data from back-translation:** Inherit the source license.
- **Production logs (jak.ma):** PII-scrubbed, internal-only.

Verify the license for each before deploying to production. The "research-only" qualifier in many academic corpora can break a commercial deployment without warning.

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · sam25@mails.tsinghua.edu.cn
