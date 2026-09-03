# Touch-Ex-Results

This private repository stores results from experiments conducted using the [Touch-Ex](https://github.com/gemixin/touch-ex) pipeline. It contains saved experiment metadata, plots, and notebooks for analysing completed experiments.

## Project Structure

- `results/` contains experiment metadata, evaluation outputs, and plots, organised into directories by target label
- `acc_f1_tables/` contains CSV summaries of accuracy and weighted F1 for each target-label experiment
- `mae_rmse_r2_tables/` contains CSV summaries of MAE, RMSE, and R² for each regression experiment
- `plots/` contains plots comparing results across experiments
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

## Results Format

Each target-label directory stores an `experiments.parquet` file with one row per run. Records include the run and model configuration, training history, predictions, and task-specific metrics. Classification results include target labels, accuracy, and weighted F1, while regression results include the regression target, MAE, RMSE, and R². For compact per-experiment views, `acc_f1_tables/` provides classification CSVs and `mae_rmse_r2_tables/` provides regression CSVs.

Where available, every run reports three evaluation splits:

| Split | Measures |
| --- | --- |
| Standard test | In-distribution performance on labels represented during training |
| Unseen matched | Generalisation to unseen variants of represented objects |
| Unseen related | Generalisation to related but distinct objects |

The result files retain loss, predictions, ground-truth values, and the relevant classification or regression metrics for each split. Classification results additionally retain expected labels for the unseen evaluation splits.

`results/test_data_configs/` follows a different format: it contains the outputs from the exploratory `test_data_configs.ipynb` notebook in the [Touch-Ex repository](https://github.com/gemixin/touch-ex), rather than per-run `experiments.parquet` records.

## Experiment Summaries

### Object and Object-Region Classification

Each experiment in this section was run separately for the `object` and `object_region` target labels.

Unless stated otherwise, experiments used the default input pipeline: the `pad_224` transform with background subtraction, dataset normalisation and no training augmentations.

#### Experiment 1 - fine-tuned model comparison

Six model types were trained end to end using their default settings. The baseline used a learning rate of `3e-4`; the other models used `2e-5`.

#### Experiment 2 - frozen-backbone model comparison

Five model types were evaluated with their backbones frozen. The baseline CNN was excluded because it can only be trained end to end. A learning rate of `2e-4` was used.

#### Experiment 3 - ResNet-18 augmentation sweep

ResNet-18 was fine-tuned using `center_crop_224` instead of the default `pad_224` transform, with a learning rate of `2e-5`. The sweep compares no augmentation, colour jitter, random resized crop, and a combination of colour jitter and random resized crop. Centre cropping was used as the evaluation transform to remain compatible with random resized cropping during training: when training images are cropped, test images should also be cropped, but deterministically and consistently.

#### Experiment 4 - ResNet-18 background-subtraction sweep

ResNet-18 was fine-tuned with the `center_crop_224` transform and a learning rate of `2e-5`. All training augmentations were enabled: colour jitter, horizontal flip, and random resized crop. The sweep compares training with and without background subtraction. Runs were trained for up to 25 epochs with early stopping (`early_stopping_patience`: `5`; `early_stopping_min_delta`: `0.1`).

#### Experiment 5 - ResNet-18 padded augmentation sweep

ResNet-18 was fine-tuned using the default `pad_224` transform and a learning rate of `2e-5`. The sweep compares no augmentation, colour jitter, horizontal flip, and the combination of colour jitter with horizontal flip.

#### Experiment 6 - ResNet-18 colour-jitter settings sweep

ResNet-18 was fine-tuned using the default `pad_224` transform and a learning rate of `2e-5`. The sweep compares two colour-jitter configurations: the SSVTP settings (brightness and contrast of `0.9–1.1`, hue of `0.05`, and saturation of `0.2`) and the T3 settings (brightness and contrast of `0.6–1.4`, hue of `0.3`, and saturation of `0.5`).

#### Experiment 7 - ResNet-18 normalisation sweep

ResNet-18 was fine-tuned using the `pad_224` transform, background subtraction, the SSVTP colour-jitter settings, and a learning rate of `2e-5`. The sweep compares no normalisation, dataset normalisation, and ImageNet normalisation. 

#### Experiment 8 - T3-Tiny augmentation sweep

T3-Tiny was fine-tuned using the `center_crop_224` transform, dataset normalisation, no background subtraction, and a learning rate of `2e-5` for 10 epochs. This tests whether matching the cropping and no-background-subtraction settings used to train the T3 encoder improves downstream performance. The sweep compares no augmentation with the full SSVTP and T3 augmentation settings: colour jitter, horizontal flip, and random resized crop.

#### Experiment 9 - T3-Tiny extended augmentation sweep

Experiment 9 repeats Experiment 8 with the same T3-Tiny model, input pipeline, and three augmentation configurations, but extends training to up to 25 epochs using a learning rate of `5e-5` and early stopping (`early_stopping_patience`: `3`; `early_stopping_min_delta`: `0.1`) to give the models more opportunity to reach their potential.

#### Experiments 10-13 - ResNet-18 candidate seed sweep

These experiments repeat the same four candidate ResNet-18 configurations using seeds `129`-`132`, to assess the stability of the results across random seeds. All runs used dataset normalisation, background subtraction, a learning rate of `2e-5`, and up to 25 epochs with early stopping (`early_stopping_patience`: `3`; `early_stopping_min_delta`: `0.1`). The candidates were `pad_224` with no augmentation (`pad_none`); `pad_224` with SSVTP colour jitter (`pad_jitter`); `pad_224` with SSVTP colour jitter and horizontal flipping (`pad_flip_jitter`); and `center_crop_224` with SSVTP colour jitter, horizontal flipping, and random resized cropping (`crop_all`).

#### Experiment 14 - T3-Tiny frozen augmentation sweep

T3-Tiny was used with its encoder frozen, the `center_crop_224` transform, dataset normalisation, and no background subtraction, to match the settings used when the encoder was pre-trained. The experiment compares no augmentation with the full SSVTP and T3 augmentation configurations: colour jitter, horizontal flip, and random resized crop. Runs use the default frozen-model learning rate of `2e-4` for up to 25 epochs, with early stopping (`early_stopping_patience`: `3`; `early_stopping_min_delta`: `0.1`).

#### Experiment 15 - ResNet-18 force-level and motion filter sweep

ResNet-18 was fine-tuned on five filtered variants of the dataset to assess how restricting the training data by contact conditions affects object and object-region classification. Three runs retain only force level `1`, `2`, or `3`, while two retain only the `sliding` or `rotation` motion. All runs used the `pad_224` transform, background subtraction, dataset normalisation, SSVTP colour jitter, and a learning rate of `2e-5` for up to 25 epochs, with early stopping (`early_stopping_patience`: `3`; `early_stopping_min_delta`: `0.1`).

#### Experiment 16 - ResNet-18 Main

*ResNet-18 Main* is the configuration selected from the candidate sweep in Experiments 10–13 and provides a reference for comparison with other models in future experiments. It uses the `pad_224` transform, background subtraction, dataset normalisation, SSVTP colour jitter, and a learning rate of `2e-5` for up to 25 epochs, with early stopping (`early_stopping_patience`: `3`; `early_stopping_min_delta`: `0.1`).

### Conditioned Object and Object-Region Classification

Each experiment in this section was run separately for the `object` and `object_region` target labels.

#### Experiment 1 - ResNet-18 with force-level conditioning

This experiment uses the *ResNet-18 Main* configuration with force level added as a conditioning input. Force-level labels (`1`, `2`, and `3`) were mapped to normalised scalar values (`0.0`, `0.5`, and `1.0`). Image and conditioning features were combined using a fusion layer with a hidden dimension of `256` and dropout of `0.2`.

### Force Level Classification

#### Experiment 1 - ResNet-18 force-level classification

This experiment uses the *ResNet-18 Main* configuration to classify the three force levels (`1`, `2`, and `3`).

### Force Regression

#### Experiment 1 - ResNet-18 force regression

This experiment uses the *ResNet-18 Main* configuration to predict continuous force in newtons (`force_n`), with `early_stopping_min_delta` reduced to `0.001` for regression.

### FSR Voltage Regression

#### Experiment 1 - ResNet-18 FSR voltage regression

This experiment uses the *ResNet-18 Main* configuration to predict the force-sensitive resistor (FSR) output voltage (`fsr_voltage`), with `early_stopping_min_delta` reduced to `0.001` for regression.

## Notes

This repository stores generated experiment outputs only. The classification and regression pipelines, model implementations, and experiment scripts are maintained in the main [Touch-Ex repository](https://github.com/gemixin/touch-ex). Use that pipeline to run new experiments, then add the generated result file here and update the experiment summary above when needed.

Model checkpoints are stored separately and are not included here.
