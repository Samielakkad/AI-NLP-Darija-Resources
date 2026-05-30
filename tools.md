# Tools — Pipelines, Tokenizers, and Infrastructure for Darija NLP

> The concrete software toolkit. Each tool: what it does, install command, when to use it, when not to.

Sort: by where they sit in a typical Darija NLP pipeline (left-to-right = early to late in the pipeline).

---

## Tokenization

### CAMeL Tools

- **What:** The strongest open-source Arabic NLP toolkit. Morphological analyzer, dialect ID, tokenizer, sentence segmenter.
- **Install:** `pip install camel-tools`
- **Docs:** https://camel-tools.readthedocs.io/
- **Use for:** Sentence segmentation, word tokenization, dialect ID for Maghrebi dialect identification.
- **Strengths:** Handles MSA well, has Maghrebi dialect-specific modules, well-maintained.
- **Weaknesses:** Slower than tiktoken-class tokenizers. Adds ~30ms per query. Fine for offline preprocessing, marginal for sub-100ms latency budgets.
- **When NOT to use:** If your production inference latency is sub-200ms p50, use a SentencePiece BPE tokenizer instead.

---

### SentencePiece (with custom Darija BPE)

- **What:** Subword tokenizer; can be trained on any text including mixed-script content.
- **Install:** `pip install sentencepiece`
- **Docs:** https://github.com/google/sentencepiece
- **Use for:** Production inference tokenization where you need to handle both Arabic script and Arabizi (Latin) in the same vocabulary.
- **Recipe for Darija-specific BPE:**
  ```bash
  spm_train --input=darija_corpus.txt \
            --model_prefix=darija_bpe \
            --vocab_size=32000 \
            --character_coverage=1.0 \
            --model_type=bpe \
            --normalization_rule_name=identity \
            --treat_whitespace_as_suffix=false
  ```
- **Strengths:** Fast (sub-1ms tokenization). Handles unicode well. Easy to ship with the model.
- **Weaknesses:** Doesn't do morphological analysis. If you need stems, use CAMeL after SentencePiece.

---

### Farasa — Arabic segmentation + diacritization

- **What:** Arabic segmentation + diacritization (MSA-focused).
- **Install:** `pip install farasapy` (Python wrapper)
- **Docs:** https://farasa.qcri.org/
- **Use for:** When you have heavily-diacriticized Arabic that needs normalizing.
- **Weaknesses:** MSA-trained, performs poorly on Moroccan dialect. **Don't use as primary tokenizer for Darija.**
- **When to use:** Preprocessing MSA-leaning content (e.g., government documents, formal news) before downstream Darija processing.

---

## Transliteration (Arabic ↔ Latin)

### Custom rule-based mapping (recommended for production)

- **Why over neural:** A 200-entry lookup table for the high-frequency Darija words (kankhdem, b3it, 3andek, etc.) beats neural transliteration on latency, cost, and predictability for common cases.
- **Recipe:** Build a hand-curated `darija_arabizi_map.json` with ~200 entries for the top frequency words from your corpus. Apply with a simple regex pass.
- **When NOT to use:** Long-tail uncommon words. Fall through to neural transliteration for unseen tokens.

Example structure:
```json
{
  "kankhdem": "كنخدم",
  "b3it": "بغيت",
  "3andek": "عندك",
  "fih": "فيه",
  "wash": "واش",
  ...
}
```

---

### Neural Arabizi → Arabic transliterator

- **What:** Trained neural model that converts Latin-script Arabic to Arabic script.
- **Install:** Use Hugging Face — search for "arabizi" pre-trained models. Options change frequently; check leaderboards.
- **Use for:** Long-tail words not in your rule-based table.
- **Caveat:** Latency: 50–200ms per sentence (depending on model size). Only invoke on the long-tail.

---

## Fine-tuning infrastructure

### Hugging Face Transformers + PEFT (LoRA)

- **What:** Standard pipeline for parameter-efficient fine-tuning.
- **Install:** `pip install transformers peft accelerate datasets`
- **Docs:** https://huggingface.co/docs/peft/
- **Use for:** Adapting a base model (Llama 3.1 8B, Qwen 2.5 7B, Mistral Nemo) to Darija without full fine-tuning.
- **Recipe:** See [jak-ma-eval-suite/scripts/finetune/lora_train.py](https://github.com/Samielakkad/jak-ma-eval-suite) for a working template (target: Llama 3.1 8B, rank 16, alpha 32).
- **Compute cost:** ~6 hours on a single A100, ~$20–50 on Modal/RunPod/Vast.ai.

---

### Modal / RunPod / Vast.ai

- **What:** Affordable single-A100 hosts for one-off LoRA runs.
- **Cost:** Modal ~$3/hour A100. RunPod ~$2/hour. Vast.ai ~$1–2/hour but unreliable.
- **Use for:** Iterating on LoRA experiments without a dedicated GPU.
- **Recommendation for Darija LoRA:** Start with Modal for the reliability. Move to RunPod once your recipe is stable and you're optimizing cost.

---

## Inference hosting

### vLLM (on RunPod)

- **What:** High-throughput inference server for open-source LLMs.
- **Install:** `pip install vllm`
- **Docs:** https://docs.vllm.ai/
- **Use for:** Hosting your Darija-fine-tuned LoRA at production-scale throughput.
- **Cost vs. Grok-3-mini:** vLLM-hosted Llama 3.1 8B at ~$0.10/1M tokens vs. Grok at ~$0.30/1M. ~3× cheaper at scale.

---

### Replicate

- **What:** Pay-per-inference hosting for fine-tuned models.
- **Docs:** https://replicate.com/
- **Use for:** Quick-deploy of a fine-tuned model for production traffic.
- **Cost:** ~$0.0002–$0.005 per inference depending on model size.
- **When NOT to use:** If you have constant traffic — vLLM on RunPod amortizes better.

---

## Evaluation infrastructure

### jak-ma-eval-suite scripts/run_eval.py

- **What:** Run the 5-dim eval rubric against any OpenAI-compatible chat endpoint.
- **Install:** Clone https://github.com/Samielakkad/jak-ma-eval-suite + `pip install httpx tqdm`
- **Use for:** Release-gating your Darija model. Get a JSON summary of factuality, naturalness, trade-fit, price-fairness, geographic.
- **Output:** `results.json` with per-dimension scores and aggregate.

---

### Hugging Face Datasets (for benchmark hosting)

- **What:** Host your eval set as a Hugging Face dataset for reproducibility.
- **Install:** `pip install datasets`
- **Use for:** Publishing your Darija test set so others can run against the same prompts.

---

## Production infrastructure (the jak.ma stack)

### MongoDB Atlas (with composite indexes)

- **What:** Document database for workers, eval_logs, and price_cache.
- **Use for:** Structured retrieval (the Pass 1 → retrieve step in jak.ma).
- **Index recipe (jak.ma):** `(category, city, approved, available)`, `(category, featured, verified, rating)`. See [jak-ma-eval-suite/VERIFIER_SPEC.md](https://github.com/Samielakkad/jak-ma-eval-suite).
- **Cost:** M0 tier free up to 512 MB. M2 tier $9/mo. M5 tier $25/mo.

---

### Vercel Serverless

- **What:** Function-per-route serverless hosting with edge compute and 30s function timeout.
- **Use for:** API endpoints for your chat handler.
- **Caveat:** The 30s timeout shapes your architecture. Streaming responses can exceed 30s only via SSE; non-streaming must complete within 30s. Plan accordingly.

---

### xAI Grok API

- **What:** Multilingual LLM with strong dialect coverage including Maghrebi.
- **Cost:** Grok-3-mini at $0.30/1M input, $0.50/1M output.
- **Use for:** Pass 1 classification (JSON mode) + Pass 2 constrained generation.
- **Caveat:** JSON mode reliability is 99%+ but not 100%. Add a fallback parser for malformed JSON.

---

## Browser-side inference

### TensorFlow.js + MobileNetV3

- **What:** Sub-250ms p50 inference on mid-tier mobile devices for image classification.
- **Install:** `<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>`
- **Use for:** Multimodal classification on the client side. Saves API roundtrip cost (Grok-2-Vision is ~$0.005 per image).
- **Caveat:** Requires a trained model. Training MobileNetV3 on 12-class trade classification needs ~50–100 images per class.

---

## Tools I tried that didn't work for Darija production

- **fasttext:** Underwhelming on Maghrebi text. Not worth the complexity for our use case.
- **Polyglot:** Maintained but not updated for recent Arabic dialect coverage.
- **NLTK:** Western-language-first. Arabic support is bolted-on. Use CAMeL Tools instead.
- **MITIE:** Information extraction; not suited for Darija out-of-box.
- **Stanford CoreNLP:** Has Arabic models but they're MSA-only.

These are listed not to denigrate the projects but to save you the time of trying them for Darija production work.

---

## The stack I'd recommend for a Darija-NLP project starting today

If you have 2 weeks to ship a Darija production system:

1. **Inference:** xAI Grok-3-mini (or Anthropic Claude Sonnet for non-Chinese-region deployments)
2. **Tokenization:** Custom SentencePiece BPE trained on your domain corpus + a small rule-based Arabizi normalizer
3. **Retrieval:** MongoDB Atlas with composite indexes
4. **Eval:** [jak-ma-eval-suite/scripts/run_eval.py](https://github.com/Samielakkad/jak-ma-eval-suite) against a domain-specific test set you build
5. **Hosting:** Vercel serverless (you'll outgrow it at ~200k queries/month, but it's free at the start)
6. **Verifier:** Deterministic rules in code, no LLM call inside the verifier (see [jak-ma-eval-suite/VERIFIER_SPEC.md](https://github.com/Samielakkad/jak-ma-eval-suite/blob/main/VERIFIER_SPEC.md))

If you have 2 months and a $500 compute budget:

7. Add a LoRA fine-tune on Llama 3.1 8B for Pass 2 generation (cuts cost ~3×)
8. Add the curated Darija test set + production logs (PII-scrubbed) to your training set
9. Add BM25 + semantic cache (cuts cost another ~2× at scale)

---

**Sami EL AKKAD** · Tsinghua SIGS AI MSc · sam25@mails.tsinghua.edu.cn
