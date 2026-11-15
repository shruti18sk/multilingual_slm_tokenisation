# Multilingual Small LLMs: Tokenization Efficiency across Hindi, Korean and English

Early-stage experiments for a research study on optimising multilingual small
language models (SLMs) for resource-constrained devices. This repo currently
covers **Stage 1: tokenization efficiency**. Stage 2 (quantization) is planned.

## Motivation
Small LLMs are attractive for on-device use, but the number of tokens needed to
encode the same sentence varies a lot by language. More tokens means more
compute, memory and latency per sentence. This repo measures that gap for
Hindi and Korean relative to English.

## Method
- **Data:** FLORES-200 `devtest`, first 300 parallel sentences in English
  (`eng_Latn`), Hindi (`hin_Deva`) and Korean (`kor_Hang`).
- **Models (tokenizers only):** Qwen2.5-1.5B, SmolLM2-1.7B, TinyLlama-1.1B-Chat.
- **Metrics:**
  - **Parity ratio** = tokens(language) / tokens(English) per sentence, averaged
    (95% bootstrap CI, 2000 resamples). This is the primary metric.
  - **Bytes per token** and **fertility** (tokens per whitespace word).
    Fertility is not comparable across languages (Korean spacing differs), so
    it is reported as secondary.

## Results (n = 300)

| Model | Hindi parity (95% CI) | Korean parity (95% CI) |
|---|---|---|
| Qwen2.5-1.5B | 4.40x (4.30-4.51) | 1.71x (1.68-1.75) |
| TinyLlama-1.1B-Chat | 4.55x (4.45-4.66) | 3.23x (3.16-3.30) |
| SmolLM2-1.7B | 5.09x (4.98-5.22) | 4.83x (4.73-4.94) |

## Related Work
Petrov et al. (2023), https://arxiv.org/abs/2305.15425

<img src="parity_plot%20(1).png" alt="Tokenization parity" width="700">

**Observations**
- Hindi needs roughly 4.4-5.1x more tokens than English across all three models.
- Korean varies strongly by tokenizer: from 1.71x (Qwen2.5) to 4.83x (SmolLM2).
- SmolLM2 on Korean has ~1.19 bytes/token, which suggests frequent byte-level
  fallback (hypothesis, not yet verified).
- An earlier 5-sentence pilot overestimated the ratios by about 8-13%; the model
  ranking stayed the same.

Full numbers: `token_ratio.csv`.

## Limitations
- Only 3 languages and 3 small models; tokenizer vocabulary size and training
  data were not controlled for.
- FLORES text is Wikipedia/news style; results may differ for conversational text.
- The first 300 devtest sentences were used (not randomly sampled).
- Tokenization cost only; no model quality evaluation yet.

## Next steps
1. Add more tokenizers (e.g. Gemma, Llama-3.2) and check byte-fallback rates.
2. Quantization (FP16 vs 8-bit vs 4-bit): perplexity (bits-per-byte), memory and
   speed per language.
3. Downstream accuracy on Hindi/Korean benchmarks.

## Run it
Open `notebooks/multilingual_slm_tokenisation.ipynb` in Google Colab.
Dependencies: `requirements.txt`.
