# Model card: chest X-ray report generator (baseline)

## Summary

A small image-to-text model that generates radiology-style report text from a chest X-ray.
It is a learning and portfolio project, **not a medical device**.

## Intended use

- Studying image captioning and report generation on a public dataset.
- A reproducible baseline for comparing other approaches.

**Not intended for:** diagnosis, triage, clinical decision support, or any use involving real
patients.

## Model

- **Encoder:** DenseNet-121, frozen, 224x224 RGB input, global average pooling to a 1024-d vector.
  Initialised with CheXNet weights when a weights file is found; otherwise random.
- **Decoder:** image vector projected to 256 units, repeated across time and concatenated with
  256-d word embeddings, one LSTM layer (256 units), softmax over the vocabulary.
- **Training:** teacher forcing, padding-masked cross-entropy, Adam (lr 1e-3), 5 epochs by default.
- **Inference:** greedy decoding with a 4-gram repeat guard, up to 100 tokens.

## Data

Indiana University Chest X-ray collection (see [DATA.md](DATA.md)). The default run uses the first
500 image/report pairs; train/validation split is grouped by study `uid`.

## Evaluation

BLEU-1 and ROUGE-L on up to 50 validation reports.

| Metric | Score |
|---|---|
| BLEU-1 | _fill in after your run_ |
| ROUGE-L | _fill in after your run_ |

These metrics measure word overlap with the reference report. They do not measure whether the
findings are clinically correct.

## Known limitations and failure modes

- Trained on a small subset with a small model, so output tends to be generic ("normal" style
  reports) and can be repetitive.
- Does not reliably describe abnormal findings; rare conditions are poorly represented.
- Single-institution data, so it may not generalise to other scanners, populations or report styles.
- No uncertainty estimate, no clinical metric, and no human (radiologist) review.
- The encoder may be randomly initialised if CheXNet weights are not loaded; check the console
  output of the run.

## Ethics

Reports and images come from a de-identified public research collection. Any generated text could
look authoritative while being wrong, so it must never be shown to patients or used in care.
