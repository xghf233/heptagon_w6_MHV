# Experiment reports

The reports in this directory are compact, reviewable records selected from the
full experiment artifacts:

- [`BASELINE_REPORT.md`](BASELINE_REPORT.md): causal-attention baseline.
- [`WORD_BIDI_REPORT.md`](WORD_BIDI_REPORT.md): 500K-step word-bidirectional run.
- [`WORD_BIDI_150K_REPORT.md`](WORD_BIDI_150K_REPORT.md): 150K-step budget comparison.
- [`ERROR_ANALYSIS.md`](ERROR_ANALYSIS.md): full-validation error overlap between
  the 500K and 150K checkpoints.

Selected figures are stored in [`figures/`](figures/):

- `baseline_loss_curve.png`
- `smoke_word_bidi_curve.png`
- `smoke_word_bidi_from_jsonl.png`
- `word_bidi_report.png`
- `word_bidi_150k_report.png`

Small machine-readable summaries live in [`../results/`](../results/README.md).
Complete training histories, periodic evaluations, raw predictions, logs, and
checkpoints are external artifacts and are intentionally not tracked.
