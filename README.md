# Touch-Ex Results

This private repository stores results from experiments conducted using the [Touch-Ex](https://github.com/gemixin/touch-ex) pipeline. It contains saved experiment metadata, plots, and notebooks for analysing completed experiments.

## Project structure

- `results/` contains experiment metadata, evaluation outputs, and plots
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

## Notes

This repository stores generated experiment outputs only. The classification pipeline, model implementations, and experiment scripts are maintained in the main [Touch-Ex repository](https://github.com/gemixin/touch-ex).

Model checkpoints are stored separately and are not included here.