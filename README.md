# Touch-Ex Results

This private repository stores results from experiments conducted using the [Touch-Ex](https://github.com/gemixin/touch-ex) pipeline. It contains saved experiment metadata, plots, and notebooks for analysing completed experiments.

## Project structure

- `results/` contains experiment metadata, evaluation outputs, and plots, organised into directories by target label
- `acc_f1_tables/` contains CSV summaries of accuracy and weighted F1 for each target-label experiment
- `notebooks/` contains notebooks for exploring and comparing completed experiments

## Installation

Clone the repository:

```bash
git clone https://github.com/gemixin/touch-ex-results.git
cd touch-ex-results
```

Install the dependencies with pip:

```bash
python3 -m pip install -r requirements.txt
```

Or create the provided Conda environment:

```bash
conda env create -f environment.yml
conda activate touch-ex-results
```

## Results format and evaluation

Each target-label directory stores an `experiments.parquet` file with one row per run. Records include the run and model configuration, training history, target labels, predictions, and metrics. Every target-label result file uses the same schema, while its labels and task-specific configuration may differ. For a compact per-experiment view, `acc_f1_tables/` provides CSVs with standard-test and unseen-matched accuracy and weighted F1.

Where available, every run reports three evaluation splits:

| Split | Measures |
| --- | --- |
| Standard test | In-distribution performance on labels represented during training |
| Unseen matched | Generalisation to unseen variants of represented objects |
| Unseen related | Generalisation to related but distinct objects |

The result files retain accuracy, loss, weighted F1, predictions, ground-truth labels, and expected labels for each split.

`results/test_data_configs/` follows a different format: it contains the outputs from the exploratory `test_data_configs.ipynb` notebook in the [Touch-Ex repository](https://github.com/gemixin/touch-ex), rather than per-run `experiments.parquet` records.

## Experiment summaries

### Object and object region classification

Unless stated otherwise, experiments used the default input pipeline: the `pad_224` transform with background subtraction, dataset normalisation and no training augmentations.

#### Experiment 1 - fine-tuned model comparison

Six model types were trained end to end using their default settings. The baseline used a learning rate of `3e-4`; the other models used `2e-5`.

#### Experiment 2 - frozen-backbone model comparison

Five model types were evaluated with their backbones frozen. The baseline CNN was excluded because it can only be trained end to end. A learning rate of `2e-4` was used.

#### Experiment 3 - ResNet-18 augmentation sweep

ResNet-18 was fine-tuned using `center_crop_224` instead of the default `pad_224` transform, with a learning rate of `2e-5`. The sweep compares no augmentation, colour jitter, random resized crop, and a combination of colour jitter and random resized crop. Centre cropping was used as the evaluation transform to remain compatible with random resized cropping during training: when training images are cropped, test images should also be cropped, but deterministically and consistently.

### Force level classification

Coming soon...

## Notes

This repository stores generated experiment outputs only. The classification pipeline, model implementations, and experiment scripts are maintained in the main [Touch-Ex repository](https://github.com/gemixin/touch-ex). Use that pipeline to run new experiments, then add the generated result file here and update the experiment summary above when needed.

Model checkpoints are stored separately and are not included here.
