# StreakFocus-YOLO

StreakFocus-YOLO is a YOLOv8s detector for mineral streaks (the Quartzity class) and the other surface defects in sawn timber. Training uses a 1024 input, vertical flip, and reduced color jitter. The SPPF block is replaced by focal modulation.

The focal modulation module code will be released upon publication.

## Images

This repository does not include images. Download the Kodytek et al. wood surface defect dataset from Zenodo:

<https://zenodo.org/records/4694695>

The dataset is described in *F1000Research* 10:581, <https://doi.org/10.12688/f1000research.52903.2>.

## Split

The partition is an image-level random split made before augmentation: seed 42, ratio 0.70/0.10/0.20, **12,792 / 1,827 / 3,656** images (train / val / test). Those counts are the full kept subset (18,275 images) recorded in `splits/splits.json` and `splits/class_stats.json`.

Augmentation runs only on the fly during training. The saved lists are the original images, so validation and test are not augmented copies.

## Layout

```
configs/data.yaml                 # dataset root, split dirs, 8 class names
configs/yolov8s-focal.yaml        # YOLOv8s with focal modulation in place of SPPF
configs/train_*.yaml              # Ultralytics args.yaml dumps from training runs
splits/train.txt                  # one image filename per line
splits/val.txt
splits/test.txt
splits/splits.json                # seed, ratio, and the same ids without .jpg
splits/class_stats.json           # image and box counts
```

`configs/yolov8s-focal.yaml` keeps the YOLOv8s graph and replaces node 9 (SPPF) with `FocalModulation`. The 1024-input runs (`configs/train_boost_hr1024_aug_focal*.yaml`) set `imgsz: 1024`, vertical flip (`flipud: 0.5`), and reduced color jitter (`hsv_s: 0.35`, `hsv_v: 0.2`).

## Classes

Class indices in `configs/data.yaml`:

| Index | Name |
| ---: | --- |
| 0 | Quartzity |
| 1 | Live_Knot |
| 2 | Marrow |
| 3 | Resin |
| 4 | Dead_Knot |
| 5 | Knot_with_crack |
| 6 | Knot_missing |
| 7 | Crack |

## Recreate the YOLO folders

`configs/data.yaml` points at `./data/wood_yolo_full` with `train/images`, `val/images`, and `test/images`. Build that tree from the split lists (images and labels stay outside git):

```
data/wood_yolo_full/
  train/images/    train/labels/
  val/images/      val/labels/
  test/images/     test/labels/
```

Each line of `splits/train.txt`, `splits/val.txt`, and `splits/test.txt` is an image filename such as `100000000.jpg`. Copy that file into the matching `images/` directory, and copy the YOLO label with the same stem (`100000000.txt`) into `labels/`. `splits/splits.json` lists the same images as stems, without the extension.

Label rows are Ultralytics format: `class_id x_center y_center width height`, coordinates normalized to the image, class ids in the order above.

Zenodo ships the source pictures as BMP archives (`Images1.zip`–`Images10.zip`) and bounding boxes in `Bouding_Boxes.zip` (`<image>_anno.txt`). Convert those files to the filenames in the split lists, then copy them into the folders above.

## Citation

Placeholder for the paper, which is under review:

> Zeng, Chenyang. StreakFocus-YOLO: a streak-oriented resolution, augmentation, and context recipe for real-time mineral-streak detection in sawn timber. *Journal of Electronic Imaging*. Under review.

Dataset:

> Kodytek et al. A large-scale image dataset of wood surface defects for automated vision-based quality control processes. *F1000Research* 10:581. <https://doi.org/10.12688/f1000research.52903.2>. Files: <https://zenodo.org/records/4694695>.
