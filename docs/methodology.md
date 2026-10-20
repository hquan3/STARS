# Methodology summary

STARS is a two-stage synthetic-to-real transfer pipeline for 24-hour building electricity forecasting.

## Stage 1: representation-aware screening

The VAE is trained on calendar-aligned, 14-day (336-hour) ComStock load windows. Each window is standardized independently so that reconstruction error emphasizes load shape rather than absolute building scale.

The selected manuscript configuration uses:

- two 1-D convolutional encoder layers with 32 and 64 channels;
- kernel sizes 5 and 3;
- a 32-dimensional latent space;
- a mirrored transposed-convolution decoder; and
- early stopping with a patience of 20 epochs.

At inference, the VAE reconstructs an identically aligned BDG2 window. A real building is retained when its reconstruction MSE does not exceed the maximum synthetic reconstruction MSE for the season. The threshold is a deployment heuristic for identifying cases represented by the synthetic training domain; it is not a formal guarantee of forecast quality.

## Stage 2: zero-shot forecasting

One PatchTST forecaster is pretrained on the complete synthetic source cohort for each season, then applied without target-specific fine-tuning.

| Parameter | Value |
|---|---:|
| Look-back window | 336 hours |
| Forecast horizon | 24 hours |
| Input features | 8 |
| Output channels | 1 |
| Embedding dimension | 32 |
| Feed-forward width | 64 |
| Attention heads | 4 |
| Encoder layers | 3 |
| Dropout | 0.1 |
| Activation | GELU |

The feature set combines electricity load, outdoor air temperature, relative humidity, wind speed, and calendar indicators. Summer and winter models are trained separately.

## Comparisons

The manuscript compares four strategies:

1. one PatchTST trained on the full synthetic source cohort and evaluated on every real target;
2. the same full-source PatchTST evaluated only on VAE-screened targets;
3. target-specific PatchTST models trained on the K nearest synthetic sources; and
4. K-nearest-source models evaluated only on VAE-screened targets.

The similarity ablation uses Euclidean distance on the aligned, normalized 14-day load window for `K = 1, 5, 10, 20, 40, 80, 160`.

## Metrics

The reported evaluation metrics are CVRMSE and NMBE. Before an archival release, the implementation must freeze whether these are macro-averaged per building or computed after concatenating all observations, and regenerate the summary table from that single rule.
