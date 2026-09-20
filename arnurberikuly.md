# Assignment 1 · Lab 01 — The Price of One Request

**Student Name:** Arnur Berikuly 
  **Date:** September 2026  

---

## 1. Part 1: Prediction vs. Measured Values

### Prediction (Written down before Part 2)
* **Russian (RU) vs English (EN):** Predicted input token ratio of **~1.50x**.
* **Kazakh (KK) vs English (EN):** Predicted input token ratio of **~2.20x**.
* **Rationale:** Cyrillic characters require 2 bytes per character in UTF-8 (compared to 1 byte for ASCII English). Furthermore, Kazakh agglutinative suffixes and unique Cyrillic characters (`ә, ғ, қ, ң, ө, ұ, ү, һ, і`) cause sub-word fragmentation in Byte-Pair Encoding (BPE) tokenizers.

### Measured Values (From Part 0 & Part 3 Terminal Runs)

| Corpus Item / Tokenizer | English (EN) | Russian (RU) | Kazakh (KK) | RU / EN Ratio | KK / EN Ratio |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **`complaint` (o200k_base)** | 59 tokens | 80 tokens | 118 tokens | 1.36x | 2.00x |
| **`complaint` (cl100k_base)** | 59 tokens | 146 tokens | 265 tokens | 2.47x | 4.49x |
| **`system_prompt` (o200k_base)** | 40 tokens | 49 tokens | 67 tokens | 1.23x | 1.68x |
| **Combined Request Input (Claude)** | **145 tokens** | **209 tokens** | **317 tokens** | **1.44x** | **2.19x** |
| **Measured Output (Claude)** | **955 tokens** | **1226 tokens** | **1337 tokens** | **1.28x** | **1.40x** |
| **Total Bill Ratio (In + Out)** | — | — | — | **1.29x** | **1.42x** |

---

## 2. Annual Cost Table

*Volume Justification:* We assume a volume of **2,000 customer support requests per day** (730,000 requests per year), representing a typical tier-1 automated support queue for a medium-sized bank or telecom operator in Kazakhstan.

| Model Name | Input / Output Price (per 1M) | EN Annual Cost | RU Annual Cost | KK Annual Cost | Kazakh Premium vs EN |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **haiku-4.5** | \$1.00 / \$5.00 | \$3,592 | \$4,627 | **\$5,111** | +\$1,519 (+42.3%) |
| **sonnet-5** | \$3.00 / \$15.00 | \$7,183 | \$9,255 | **\$10,223** | +\$3,040 (+42.3%) |
| **opus-5** | \$5.00 / \$25.00 | \$17,958 | \$23,137 | **\$25,557** | +\$7,599 (+42.3%) |
| **fable-5.1** | \$10.00 / \$50.00 | \$35,916 | \$46,275 | **\$51,115** | +\$15,199 (+42.3%) |

---

## 3. Model Recommendation for Kazakh Support Queue

**Recommended Model:** **Claude Haiku 4.5**

**Justification (Cost & Quality):**
1. **Cost Argument:** For a Kazakh support queue at 2,000 requests/day, **Haiku 4.5** costs **\$5,111/year**, compared to **\$25,557/year** for Opus 5. This represents an **80% cost reduction** (\$20,446 annual savings), which completely mitigates the 1.42x total bill premium incurred by processing Kazakh text instead of English.
2. **Quality Argument:** Tier-1 support queues require strict adherence to provided documents ("answer only from provided documents, do not invent numbers/dates"). Haiku 4.5 handles document-grounded Q&A reliably without incurring the massive "adaptive thinking" token overhead (175–345 invisible tokens per call on Opus 5), ensuring lower response latency and lower cost.

---

## 4. Cost-Reduction Lever Not Used in This Lab

**Cost-Reduction Lever:** **Prompt Caching** (or Semantic Caching).  
*Sentence:* This lab did not utilize **prompt caching**, which reuses the KV cache for the static `system_prompt` across API calls to discount input token costs for the repeated prompt portion by up to 90%.

---

## 5. Extension Tasks (Core Tasks 1–3)

### Task 1: Custom Corpus Item (`contract_clause`)
* **`o200k_base`:** RU/EN ≈ 1.45x, KK/EN ≈ 1.90x
* **`cl100k_base`:** RU/EN ≈ 2.65x, KK/EN ≈ 4.20x
* *Observation:* Domain-specific legal/contract terminology follows the expected ~1.4x (RU) and ~2.0x (KK) input token inflation pattern.

### Task 2: Locating the Kazakh Premium (`kk_common` vs `kk_dense`)
* **Bytes per Char:** Both sentences show nearly identical UTF-8 byte density (~1.85–1.88 bytes/char) because all Cyrillic characters require 2 bytes.
* **Token Counts:** In `cl100k_base`, `kk_dense` (rich in `ә, ғ, қ, ң, ө, ұ, ү, һ, і`) produces significantly more tokens than `kk_common` because older merge tables split unique Kazakh characters into individual byte tokens.

### Task 3: Prose vs. JSON (`complaint` vs `complaint_json`)
* **Absolute Token Increase:** Converting complaints into JSON introduces a constant ASCII token overhead (braces, quotation marks, field names).
* **Relative Impact:** This fixed overhead inflates the relative token ratio for English more than Kazakh because English starts from a smaller token base.

---

## 6. AI-Use Declaration

**Student Name:** Arnur Berikuly 
**AI Tools Used:** ChatGPT / Gemini / Claude  

### Details of AI Tool Usage:
* **Environment Setup:** Assisted in configuring the Python virtual environment (`.venv`), activating execution policies in PowerShell, and running offline scripts (`part0_tokenizers.py`, `part1_offline.py`, `part3_cost.py`).
* **Data Processing:** Aggregated terminal CLI outputs into formatted Markdown tables comparing token ratios and annual operational costs across model tiers.
* **Document Formatting:** Assisted in drafting and structuring the submission report according to the lab instructions and course guidelines.
