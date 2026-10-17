# Workflow comparison: Kaggle reference notebook vs. this repository

The reference is the public Kaggle notebook this project was inspired by
([Ebrahim Elgazar, *Image Captioning Chest X-rays*](https://www.kaggle.com/code/ebrahimelgazar/image-captioning-chest-x-rays)).
This comparison describes what each notebook **does in code**, based on reading both.
It does not compare accuracy: the reference reports no evaluation metric, so there is no
number to put next to ours (see "Making it a fair comparison" below).

## Side by side

| Stage | Kaggle reference | This repository |
|---|---|---|
| Data used | All 7,470 images, ~7,414 captions | First 500 image/report pairs by default (`MAX_REPORTS`; `None` = all) |
| Target text | `impression` only | `findings` + `impression`, de-duplicated |
| Train/validation split | None; trains on everything | Grouped by study `uid` (no study in both sets), checked with an assert |
| Image encoder | DenseNet-121 with **ImageNet** weights | DenseNet-121; loads CheXNet `.h5` if found, otherwise random weights with a printed warning |
| Text cleaning | Lower-case; drops every word shorter than 2 characters (removes "a") | Regex tokeniser keeps punctuation and numbers |
| Vocabulary | Fitted on all captions. A frequency threshold (10) gives 540 words, but the generator uses the full word index (1,356 entries), so the threshold has no effect on training | Built from the training split only, max 10,000, with `<pad> <unk> <start> <end>` |
| Start/end tokens | Not added to training captions; `startseq`/`endseq` only appear in the generation code, so the model never sees them in training | `<start>`/`<end>` added to every training report |
| Word embeddings | DistilBERT vectors (768-d) used to initialise a trainable embedding | Randomly initialised 256-d embedding |
| Training samples | One sample per prefix of every caption (next-word prediction, generator class) | Whole-sequence teacher forcing, one sample per report |
| Loss | Sparse cross-entropy (padding is masked in the embedding via `mask_zero`) | Padding-masked cross-entropy, validation loss tracked |
| Epochs | 20 (final training loss about 0.58) | 5 |
| Decoding | Top-k sampling with temperature (random each run), 25 words max | Greedy decoding with a 4-gram repeat guard, up to 100 tokens |
| Evaluation | One generated caption shown for one image | BLEU-1 and ROUGE-L over validation reports, plus saved results CSV |
| Reproducibility | Hard-coded Kaggle paths, `max_length = 124` typed in by hand | Configurable paths, seeds set, `requirements.txt` |

## What each does better

**Kaggle reference**
- Uses the full dataset and trains much longer.
- Starts from pretrained DistilBERT word vectors, which is a sensible idea for a small vocabulary.
- The prefix-by-prefix generator scales to the full dataset without building huge arrays.

**This repository**
- Has a leak-free validation set and reports numbers, so claims can be checked.
- Teaches the model where reports start and end.
- Declares and warns about its encoder weights instead of silently using something else.
- Is runnable outside Kaggle.

## Things both share (be upfront about these on GitHub)

- Neither run actually uses CheXNet weights as written. The reference defines a `chexnet_sequential`
  helper but never calls it and extracts features with ImageNet weights. The original version of
  this notebook used random weights. The cleaned notebook only uses CheXNet if the weights file
  loads, so check the console output of your run before describing it as CheXNet in the README.
- Both produce generic or repetitive text. The reference's own sample output repeats "no effusion or"
  and does not match its real caption.
- Both are small baselines, not clinical tools.

## Making it a fair comparison

To compare the two approaches properly, they need the same data, split and metrics. The simplest
route is to add the reference's two main ideas to this notebook as switches and report all variants
with the same BLEU-1 and ROUGE-L on the same validation set:

1. `USE_BERT_EMBEDDINGS` (DistilBERT-initialised embedding layer)
2. `MAX_REPORTS = None` and more epochs (full data)

Until that is run, the honest statement is: *the reference has no measured result; this repository
has a measured baseline on a 500-pair subset.*
