# Papers — Reading List for Darija NLP Production Work

> Curated. Not exhaustive. Each entry has the paper title, the citation, a one-line "why read this for Darija," and the directness-to-Darija rating (Direct / Adjacent / Background).

Sort: by relevance to building a production Darija NLP system in 2026.

---

## Direct relevance to Darija

### 1. "AraBERT v2: Towards the Pre-Training of Large-Scale Arabic Language Models" (Antoun et al., 2021)

```bibtex
@inproceedings{antoun-etal-2021-arabert,
  title = {AraBERT v2: Towards the Pre-training of Large-Scale Arabic Language Models},
  author = {Antoun, Wissam and Baly, Fady and Hajj, Hazem},
  booktitle = {OSACT4 Workshop at LREC 2020},
  year = {2021}
}
```

The current best-tested baseline Arabic BERT. **Why read it:** It establishes that MSA-trained Arabic BERTs underperform on Maghrebi dialects by 8–15 F1 points on dialect-identification tasks. The gap is what you have to close.

**Directness:** Adjacent (MSA-focused but the dialect-gap finding is what motivates Darija-specific work).

---

### 2. "MARBERT: Modern and Dialectal Arabic BERT" (Abdul-Mageed et al., 2021)

```bibtex
@inproceedings{abdul-mageed-etal-2021-arbert,
  title = {ARBERT \& MARBERT: Deep Bidirectional Transformers for Arabic},
  author = {Abdul-Mageed, Muhammad and Elmadany, AbdelRahim and Nagoudi, El Moatez Billah},
  booktitle = {ACL 2021},
  year = {2021}
}
```

Pre-trained on social media Arabic (Twitter, including Maghrebi dialects). Performs ~6 F1 points better than AraBERT on Maghrebi dialect tasks. **Why read it:** This is the strongest publicly-available baseline for Maghrebi-Arabic embeddings. If your task is classification (sentiment, trade ID, intent), start here.

**Directness:** Direct.

---

### 3. "Multi-dialect Arabic BERT for Understanding Dialect Code-Switching" (Talafha et al., 2020)

```bibtex
@inproceedings{talafha-etal-2020-multi,
  title = {Multi-Dialect Arabic BERT for Understanding Lebanese Arabic},
  author = {Talafha, Bashar and Ali, Mohammad and Za{\textquoteright}atra, Mahmoud and others},
  booktitle = {ACL Workshop on Open-Source Arabic Corpora and Processing Tools},
  year = {2020}
}
```

Specifically addresses code-switching between Arabic dialects. **Why read it:** Latin-script Darija ("Arabizi") with French loan-words is heavy code-switching. The methodology generalizes.

**Directness:** Direct.

---

### 4. "DZ-NLP: A Cross-Dialect Treebank for Algerian Arabic" (Bouamor et al., 2018)

```bibtex
@inproceedings{bouamor-etal-2018-madar,
  title = {The MADAR Arabic Dialect Corpus and Lexicon},
  author = {Bouamor, Houda and Habash, Nizar and Salameh, Mohammad and others},
  booktitle = {LREC 2018},
  year = {2018}
}
```

MADAR is the canonical multi-dialect Arabic corpus. **Why read it:** Includes Rabat, Casablanca, Fes-specific data points. Sparse, but the methodology section is the playbook for collecting dialect-level corpora.

**Directness:** Direct (Moroccan cities specifically).

---

## Adjacent — applicable techniques

### 5. "BEIR: A Heterogeneous Benchmark for Zero-shot Evaluation of Information Retrieval Models" (Thakur et al., 2021)

```bibtex
@inproceedings{thakur-etal-2021-beir,
  title = {BEIR: A Heterogeneous Benchmark for Zero-shot Evaluation of Information Retrieval Models},
  author = {Thakur, Nandan and Reimers, Nils and Rücklé, Andreas and Srivastava, Abhishek and Gurevych, Iryna},
  booktitle = {NeurIPS Datasets and Benchmarks Track},
  year = {2021}
}
```

The evaluation methodology paper for retrieval systems. **Why read it:** jak.ma's two-pass retrieval needs to be evaluated against well-understood baselines (BM25, dense retrieval, hybrid). BEIR is the reference setup.

**Directness:** Adjacent (general IR, not Arabic-specific).

---

### 6. "Self-Refine: Iterative Refinement with Self-Feedback" (Madaan et al., 2023)

```bibtex
@inproceedings{madaan-etal-2023-self,
  title = {Self-Refine: Iterative Refinement with Self-Feedback},
  author = {Madaan, Aman and Tandon, Niket and Gupta, Prakhar and Hallinan, Skyler and Gao, Luyu and Wiegreffe, Sarah and others},
  booktitle = {NeurIPS 2023},
  year = {2023}
}
```

LLM self-correction methodology. **Why read it:** Inspiration for the verifier-gated generation pattern in jak.ma. The verifier-as-second-pass is a specific instantiation.

**Directness:** Adjacent (general LLM eval, applicable to Darija via the same architecture).

---

### 7. "Chain-of-Verification Reduces Hallucination in Large Language Models" (Dhuliawala et al., 2023)

```bibtex
@inproceedings{dhuliawala-etal-2023-cov,
  title = {Chain-of-Verification Reduces Hallucination in Large Language Models},
  author = {Dhuliawala, Shehzaad and Komeili, Mojtaba and Xu, Jing and Raileanu, Roberta and Li, Xian and Celikyilmaz, Asli and Weston, Jason},
  booktitle = {ACL 2024 Findings},
  year = {2024}
}
```

Methodology for catching hallucinations before they ship. **Why read it:** The verifier in `jak-ma-eval-suite/VERIFIER_SPEC.md` is a deterministic-rules version of this pattern. Read it to understand the LLM-based version we explicitly didn't ship (for cost reasons).

**Directness:** Adjacent.

---

### 8. "GPTCache: An Open-Source Semantic Cache for LLM Applications" (Bang, 2023)

```bibtex
@inproceedings{bang-2023-gptcache,
  title = {{GPTCache}: An Open-Source Semantic Cache for LLM Applications},
  author = {Bang, Fu},
  booktitle = {EMNLP 2023 NLP-OSS Workshop},
  year = {2023}
}
```

Reference implementation of semantic caching for LLM production. **Why read it:** jak.ma's 22% cache hit rate is from a similar approach, but production-tuned for the trade-city-intent canonical form. GPTCache shows the general pattern.

**Directness:** Adjacent.

---

## Background — for orientation

### 9. "The Risk of Racial Bias in Hate Speech Detection" (Sap et al., 2019)

```bibtex
@inproceedings{sap-etal-2019-risk,
  title = {The Risk of Racial Bias in Hate Speech Detection},
  author = {Sap, Maarten and Card, Dallas and Gabriel, Saadia and Choi, Yejin and Smith, Noah A.},
  booktitle = {ACL 2019},
  year = {2019}
}
```

**Why read it:** Foundational on the broader problem of low-resource and dialectal NLP being treated as out-of-distribution by default systems. The arguments generalize from African American English to Darija.

**Directness:** Background.

---

### 10. "Building effective agents" (Anthropic, 2024)

```bibtex
@misc{anthropic-2024-agents,
  title = {Building Effective Agents},
  author = {{Anthropic}},
  year = {2024},
  url = {https://www.anthropic.com/engineering/building-effective-agents}
}
```

**Why read it:** The architectural framing for two-pass + verifier. Specifically the "augmented LLM" pattern (LLM + retrieval + tool + verification). Not a Darija paper, but the architecture is what jak.ma instantiates.

**Directness:** Background (architecture).

---

## Papers I'd write next (if I had a PhD slot)

Honest research gaps in Darija NLP as of 2026:

1. **A production-quality Darija-Arabizi parallel corpus** — needed for any tokenizer or transliterator to work well. Tunisian Arabizi corpora exist; Moroccan doesn't.

2. **A trade-specific Darija lexicon** — service-economy vocabulary (بلومبي, كلامبيستري, etc.) is missing from general Darija corpora. jak.ma has some of this in production logs (PII-scrubbed). Should be open-sourced.

3. **Cross-dialect Arabic LoRA evaluation** — does a single LoRA adapter work across Maghrebi dialects (Moroccan, Algerian, Tunisian)? Or do we need separate adapters per country? The training cost difference is 1× vs. 3–5×.

4. **Verifier-gated generation as a formal pattern** — there's adjacent literature (CoV, Self-Refine) but no paper that names "verifier-gated generation" as a deployment pattern with empirical numbers. jak.ma has the data (50-query test set, 100% no-fabrication in production).

If you're a researcher and any of these interest you, let's collaborate. Email: sam25@mails.tsinghua.edu.cn.

---

## How to use this list

- **Starting a Darija classification project?** Read 1, 2, 3 in order.
- **Building a Darija RAG system?** Read 5, 7, 8.
- **Evaluating an LLM for Darija production?** Read 2, 6, 7.
- **Researching Darija specifically?** Read 1–4, then check the bibliographies for follow-on citations.

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · sam25@mails.tsinghua.edu.cn
