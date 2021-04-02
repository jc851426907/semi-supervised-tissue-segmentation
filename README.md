# ReCo - Regional Contrast

This repository contains the source code of Regional Contrast (ReCo) and baselines from the paper, [Bootstrapping Semantic Segmentation with Regional Contrast](https://shikun.io/assets/reco/reco.pdf), introduced by [Shikun Liu](https://shikun.io/), [Shuaifeng Zhi](https://shuaifengzhi.com/), [Edward Johns](https://www.robot-learning.uk/), and [Andrew Davison](https://www.doc.ic.ac.uk/~ajd/).


## Datasets
ReCo is evaluated with three datasets: **CityScapes**, **PASCAL VOC** and **SUN RGB-D**, from which **CityScapes** and **PASCAL VOC** are further evaluated with partial label mode. To begin with, please first create three directories `cityscapes`, `voc_dataset` and `sun` under `dataset` folder.

-- For CityScapes dataset, please download the original dataset from the [official CityScapes site](https://www.cityscapes-dataset.com/downloads/) and place it to the corresponding `dataset/cityscapes` folder.

-- For Pascal VOC dataset, please download the original training images from the [official PASCAL site](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar) and the augmented labels [here](http://vllab1.ucmerced.edu/~whung/adv-semi-seg/SegmentationClassAug.zip). Place the folder `JPEGImages` and `SegmentationClassAug` into the corresponding `dataset/cityscapes` folder.

-- For SUN RGB-D datsaet, please download the train dataset [here](http://www.doc.ic.ac.uk/~ahanda/SUNRGBD-train_images.tgz), test dataset [here](http://www.doc.ic.ac.uk/~ahanda/SUNRGBD-test_images.tgz) and labels [here](https://github.com/ankurhanda/sunrgbd-meta-data/raw/master/sunrgbd_train_test_labels.tar.gz). And place them into the corresponding `dataset/sun` folder. 

After making sure all datasets having been placed and downlowned successfully, run each processing file `python dataset/{DATASET}_preprocess.py` in the dataset respectively to prepare each dataset. The preprocessing file also includes generating partial label for Cityscapes and Pascal dataset with three random seeds. Feel free to modify the partial label size and random seed to suit your own research setting.

For the lazy ones: just download the pre-processed datasets here [CityScapes](https://www.dropbox.com/sh/1eeq4qi9g2n6la2/AAD4IK1oskNPUzfTuusMqfb7a?dl=0), [Pascal VOC](https://www.dropbox.com/sh/gaoqumpylcci3he/AABjenlsGet060yhGXVxobE4a?dl=0) and [SUN RGB-D](https://www.dropbox.com/sh/miq8361xxbricp5/AAD8E74uWKwELbHmhAyGshCfa?dl=0).

## Training Supervised and Semi-supervised Models
In this paper, we introduce two different training modes for semi-supervised learning.
1. Full Labels Partial Dataset: A sparse subset of training images has full ground-truth labels, with the remaining data unlabelled.
2. Partial Labels Full Dataset: All images have some labels, but covering only a sparse subset of pixels.

Running the following four files would reprentating each mode respective:
```
python train_sup.py             # Supervised learning with full labels.
python train_semisup.py         # Semi-supervised learning with full labels.
python train_sup_partial.py     # Supervised learning with partial labels.
python train_semisup_patial.py  # Semi-supervised learning with partial labels.
```

## Important Flags
Both supervised and semi-supervised methods can be trained with different flags (hyper-parameters) by adding `--{FLAG_NAME} {FLAG_OPTION}` when running each training script. Below are the breif introduction to some important flags for the experiments.

| Flag Name        | Usage  |  Comments |
| ------------- |-------------| -----|
| `num_labels`     | number of labelled images in the training set, choose `0` for training all labelled images  | only available in the full label mode,  |
| `partial`     |  percentage of labeled pixels for each class in the training set, choose `p0, p1, p5, p25` for training 1, 1%, 5%, 25% labelled pixels respecitvely  | only available in the partial label mode |
| `num_negatives` | number of negative keys sampled for each class in each mini-batch | only applied when training with ReCo loss|
| `num_queries` | number of queries sampled for each class in each mini-batch | only applied when training with ReCo loss|
| `output_dim` | output dimension for pixel-level representation | only applied when training with ReCo loss|
| `temp` | temperature used in contrastive learning | only applied when training with ReCo loss|
| `apply_aug` | semi-supervised methods for data augmentation, choose `cutout, cutmix, classmix](https://arxiv.org/abs/2007.07936)` | only available in the semi-supervised methods for our implementations for [CutOut, CutMix](https://arxiv.org/abs/1906.01916) and [ClassMix](https://arxiv.org/abs/2007.07936)|
| `weak_threshold` | weak threshold `delta_w` in active sampling | only applied when training with ReCo loss|
| `strong_threshold` | strong threshold `delta_s` in active sampling | only applied when training with ReCo loss|
| `apply_reco` | toggle on or off | apply with our proposed ReCo loss|

Training with the fewest label setting in each dataset with full label mode presented in the paper:
```
python train_semisup.py --dataset pascal --num_labels 60 --apply_aug classmix --apply_reco
python train_semisup.py --dataset cityscapes --num_labels 20 --apply_aug classmix --apply_reco
python train_semisup.py --dataset sun --num_labels 50 --apply_aug classmix --apply_reco
```

Training with the fewest label setting in each dataset with partial label mode presented in the paper:
```
python train_semisup_partial.py --dataset pascal --partial p0 --apply_aug classmix --apply_reco
python train_semisup_partial.py --dataset cityscapes --partial p0 --apply_aug classmix --apply_reco
python train_semisup_partial.py --dataset sun --partial p0 --apply_aug classmix --apply_reco
```

### Other Notices
1. We observe that the performance for the full label mode semi-supervised learning in CityScapes dataset is not stable across different machines, for which all baselines might drop 2-5% performance, though the ranking for all methods keeps the same. Different GPUs in the same machine do not affact the performance.  The performance for other datasets in the full label mode and all datasets in the partial label mode are consistent. 
2.  Please use `--seed 0, 1, 2` to accurately reproduce our results with the exact same labelled and unlabelled split we used in our experiments.

## Citation
If you found this code/work to be useful in your own research, please considering citing the following:

PLACEHOLDER.



