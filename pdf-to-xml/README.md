# pdf-to-xml

Autoresearch-style experiment loop to optimize a Claude Vision prompt for extracting structured XML from Idaho legislative bill PDFs.

## Task

Convert PDF pages of Idaho House Bill H0002 into structured XML that captures:
- Line-by-line text with correct numbering (including headers and blank lines)
- **Underlined text** (new legislative language) marked with `<underline>` tags
- **Strikethrough text** (deleted language) marked with `<strikethrough>` tags

## Results

**Final score: 0.988** (from baseline 0.590) across 42 experiments.

| Milestone | Score | Change |
|-----------|-------|--------|
| Baseline prompt | 0.590 | - |
| Fix line numbering (continuous from header) | 0.902 | +0.312 |
| Prompt refinements (visual patterns, examples) | 0.920 | +0.018 |
| Switch to `claude-sonnet-4-6` | 0.966 | +0.046 |
| Underline-past-period instruction | 0.988 | +0.022 |

### Key findings

1. **Line numbering was the biggest win.** The PDF has printed margin numbers that skip headers/blanks, but the XML needs every visual line numbered sequentially. Showing the model an explicit example (Line 10 = "AN ACT", not Line 1) was critical.

2. **Model choice matters.** Sonnet 4.6 significantly outperformed Sonnet 4.5 on underline/strikethrough detection. Opus 4.6 got a perfect page 1 but introduced double-space artifacts on page 2.

3. **Punctuation biases underline boundaries.** The model tends to end underline tags at periods/commas. Explicitly instructing it that underlines can span sentence boundaries helped.

4. **Less is more for prompt changes.** Many "helpful" additions (step-by-step process, word replacement hints, detailed boundary rules) actually hurt scores. The model performs best with clear, concise instructions.

## Files

- `prompt.md` — the optimized prompt sent to Claude Vision
- `agent.py` — sends PDF pages to Claude API (model: `claude-sonnet-4-6`)
- `evaluate.py` — scoring script comparing output against gold XMLs
- `program.md` — experiment loop instructions
- `results.tsv` — full log of all 42 experiments
- `gold/` — gold-standard XML files
- `pdfs/` — source PDF (Idaho House Bill H0002)
- `output_page*.xml` — latest model outputs

## Running

```bash
# Set your API key
export ANTHROPIC_API_KEY=your_key_here

# Run evaluation
python evaluate.py
```

Requires: `pip install anthropic PyMuPDF`
