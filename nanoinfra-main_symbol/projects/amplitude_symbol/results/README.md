# Curated results

This directory contains small, reviewable summaries selected from the full run
artifacts. Per-sample predictions, training histories, logs, Hydra metadata, and
checkpoints remain outside Git under the corresponding `outputs/` and `models/`
trees.

Included summaries:

- [`word_bidi_500k_val.json`](word_bidi_500k_val.json): full validation
  evaluation of `step_499999`.
- [`word_bidi_150k_val.json`](word_bidi_150k_val.json): full validation
  evaluation of `step_149999`.
- [`error_analysis_500k_vs_150k.json`](error_analysis_500k_vs_150k.json):
  aggregate error overlap for the two runs.

The evaluation JSON records the relative external-artifact location of its raw
per-sample predictions; those prediction files are intentionally not tracked.
