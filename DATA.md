# Data

**No data is included in this repository.** Download the datasets yourself and point the script
or notebook at them (`--data-dir`, `--chexnet-dir`).

## Chest X-rays, Indiana University

- Source: [Kaggle: raddar/chest-xrays-indiana-university](https://www.kaggle.com/datasets/raddar/chest-xrays-indiana-university)
- Original collection: Demner-Fushman et al., *Preparing a collection of radiology examinations
  for distribution and retrieval*, JAMIA, 2016 (distributed through the NIH Open-i service).
- Files used:
  - `indiana_projections.csv`: one row per image (`uid`, `filename`, `projection` = Frontal/Lateral).
  - `indiana_reports.csv`: one row per study (`uid`, `findings`, `impression`, `MeSH`, ...).
  - `images/images_normalized/*.png`: the X-ray images.
- Images and reports are joined on `uid`. One study usually has a frontal and a lateral image that
  share the same report, which is why the train/validation split is grouped by `uid`.
- In one run, 7,470 image files were found and 7,466 image/report pairs remained after matching.
- Reports contain `XXXX` placeholders where the dataset authors removed identifying text. These are
  kept as ordinary tokens in this project.
- Check the licence and terms on the Kaggle page and the Open-i site before redistributing any
  of the data or derived images.

## CheXNet weights (optional)

- Source: [Kaggle: sinamhd9/chexnet-weights](https://www.kaggle.com/datasets/sinamhd9/chexnet-weights)
- CheXNet: Rajpurkar et al., *CheXNet: Radiologist-Level Pneumonia Detection on Chest X-Rays with
  Deep Learning*, 2017 (arXiv:1711.05225).
- Used to initialise the frozen DenseNet-121 image encoder. If no `.h5` file is found, the encoder
  is randomly initialised and a warning is printed.
