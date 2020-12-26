## Deep learning project template
A convenient starting template for most deep learning projects. Built with <b>PyTorch Lightning</b> and <b>Weights&Biases</b>.<br>
Click on <b>"Use this template"</b> button above to initialize new repository.<br>

## Features
- Predefined folder structure
- Storing project configuration in a convenient way ([project_config.yaml](project/project_config.yaml))
- Storing many run configurations in a convenient way ([run_configs.yaml](project/run_configs.yaml))
- All advantages of PyTorch Lightning
- Weights&Biases integration:
    - ~~Automatically stores all relevant code, configs and model checkpoints in Weights&Biases cloud~~ (TODO)
    - Hyperparameter search with Weights&Biases sweeps ([execute_sweep.py](project/template_utils/execute_sweep.py))
- Automates the whole training process, you only need to create model and datamodule and specify them in [run_configs.yaml](project/run_configs.yaml)
- Scheduling execution of many runs/experiments ([execute_all_runs.py](project/template_utils/execute_all_runs.py))
- Built in requirements ([requirements.txt](requirements.txt))
- Built in conda environment initialization ([conda_env.yaml](conda_env.yaml))
- Built in package setup ([setup.py](setup.py))
- Example with MNIST digits classification
- Useful example callbacks ([callbacks.py](project/template_utils/callbacks.py))
<br>


## Project structure
The directory structure of new project looks like this: 
```
├── project
│   ├── data                    <- Data from third party sources
│   │
│   ├── logs                    <- Logs generated by Weights&Biases and PyTorch Lightning
│   │
│   ├── notebooks               <- Jupyter notebooks
│   │
│   ├── template_utils          <- Different utilities
│   │   ├── callbacks.py            <- Custom callbacks
│   │   ├── wandb_callbacks.py      <- Custom Weights&Biases related callbacks
│   │   ├── execute_sweep.py        <- Special file for executing Weights&Biases sweeps
│   │   ├── execute_all_runs.py     <- Special file for executing all specified runs one after the other
│   │   ├── initializers.py         <- Initializers for different modules
│   │   └── predict_example.py      <- Example of inference with trained model 
│   │
│   ├── datamodules            <- All your datamodules should be located here!
│   │   ├── example_datamodule      <- It's best to locate each datamodule in separate folder!
│   │   │   ├── datamodule.py           <- Contains class of type 'LightningDataModule'
│   │   │   ├── transforms.py           <- Some optional file
│   │   │   └── ...
│   │   ├── ...
│   │   └── ...
│   │
│   ├── models                  <- All your models should be located here!
│   │   ├── example_model           <- It's best to locate each model in separate folder!
│   │   │   ├── lightning_module.py     <- Contains class of type 'LightningModule'
│   │   │   └── models.py               <- Model architectures used by 'LightningModule'
│   │   ├── ...
│   │   └── ...
│   │
│   ├── project_config.yaml     <- Project configuration
│   ├── run_configs.yaml        <- Configurations of different runs/experiments
│   └── train.py                <- Train model with chosen run configuration
│
├── .gitignore
├── LICENSE
├── README.md
├── conda_env.yaml
├── requirements.txt
└── setup.py
```
<br>


## Project config parameters ([project_config.yaml](project/project_config.yaml))
Example project configuration:
```yaml
num_of_gpus: -1             <- '-1' to train on all GPUs available, '0' to train on CPU

loggers:
    wandb:
        project: "project_name"     <- wandb project name
        entity: "some_name"         <- wandb entity name
        log_model: True             <- set True if you want to upload ckpts to wandb automatically
        log_gradients: False        <- set True to log gradient histograms
        offline: False              <- set True if you want to store all data locally

default_callbacks:
    ModelCheckpoint:
        monitor: "val_acc"      <- name of the logged metric which determines when model is improving
        save_top_k: 1           <- save k best models (determined by above metric)
        save_last: True         <- additionaly always save model from last epoch
        mode: "max"             <- can be "max" or "min"
    EarlyStopping:
        monitor: "val_acc"      <- name of the logged metric which determines when model is improving
        patience: 5             <- how many epochs of not improving until training stops
        mode: "max"             <- can be "max" or "min"

printing:
    progress_bar_refresh_rate: 5    <- refresh rate of training bar in terminal
    weights_summary: "top"          <- print summary of model (alternatively "full")
    profiler: False                 <- set True if you want to see execution time profiling

data_dir: "data/"           <-  path to data folder
logs_dir: "logs/"           <-  path to logs folder
```
<br>


## Run config parameters ([run_configs.yaml](project/run_configs.yaml))
You can store many run configurations in this file.<br>
Example run configuration:
```yaml
SIMPLE_CONFIG_EXAMPLE_MNIST:
    seed: 1234
    trainer:
        args:
            max_epochs: 10
    model:
        load_from:
            model_path: "models/simple_mnist_classifier/lightning_module.py"
            model_class: "LitModel"
        hparams:
            lr: 0.001
            weight_decay: 0.00001
            input_size: 784
            output_size: 10
            lin1_size: 256
            lin2_size: 256
            lin3_size: 128
    datamodule:
        load_from:
            datamodule_path: "datamodules/mnist_datamodule/datamodule.py"
            datamodule_class: "MNISTDataModule"
        hparams:
            batch_size: 64
            train_val_test_split: [55_000, 5_000, 10_000]
```
To start training with this configuration run:<br>
    `python train.py --run_config_name SIMPLE_CONFIG_EXAMPLE_MNIST`

Each run configuration needs to contain sections `trainer`, `model` and `datamodule`.<br> 
Every other section is optional! (see [run_configs.yaml](project/run_configs.yaml) for more advanced config example with optional sections)<br>


Every parameter specified in model hparams section will be passed to your model class and can be retrieved through `hparams` dictionary (see example with [simple_mnist_classifier](project/models/simple_mnist_classifier/lightning_module.py)).<br>

Every parameter specified in datamodule hparams section will be passed to your datamodule class and can be retrieved through `hparams` dictionary (see example with [mnist_datamodule](project/datamodules/mnist_datamodule/datamodule.py)).<br>
<br>


## Workflow
1. Create PyTorch Lightning model
2. Create PyTorch Lightning datamodule
3. Create new run config in [run_configs.yaml](project/run_configs.yaml)
    - specify path to your model class
    - specify path to your datamodule class
    - you can add there any hyperparameters you want!
3. Configure your project in [project_config.yaml](project/project_config.yaml)
4. Run training with chosen run config<br>
    ```bash
    python train.py --run_config SIMPLE_CONFIG_EXAMPLE_MNIST
    ```
<br><br>


### DELETE EVERYTHING ABOVE FOR YOUR PROJECT  
 
---

<div align="center">    
 
# Your Project Name     

[![Paper](http://img.shields.io/badge/paper-arxiv.1001.2234-B31B1B.svg)](https://www.nature.com/articles/nature14539)
[![Conference](http://img.shields.io/badge/NeurIPS-2019-4b44ce.svg)](https://papers.nips.cc/book/advances-in-neural-information-processing-systems-31-2018)
[![Conference](http://img.shields.io/badge/ICLR-2019-4b44ce.svg)](https://papers.nips.cc/book/advances-in-neural-information-processing-systems-31-2018)
[![Conference](http://img.shields.io/badge/AnyConference-year-4b44ce.svg)](https://papers.nips.cc/book/advances-in-neural-information-processing-systems-31-2018)  

</div>

## Description
What it does

## How to run
First, install dependencies
```bash
# clone project
git clone https://github.com/YourGithubName/your-repo-name
cd your-repo-name

# optionally create conda environment
conda update conda
conda env create -f conda_env.yaml -n your_env_name
conda activate your_env_name

# install requirements
pip install -r requirements.txt
```

Next, you can train model without logging
```bash
# train model without Weights&Biases
# choose run config name from project/run_configs.yaml
cd project
python train.py --no_wandb --run_config_name SIMPLE_CONFIG_EXAMPLE_MNIST
```

Or you can train model with Weights&Biases logging
```yaml
# set project and entity names in project/project_config.yaml
loggers:
    wandb:
        project: "your_project_name"
        entity: "your_wandb_username_or_team"
```
```bash
# train model with Weights&Biases
# choose run config name from project/run_configs.yaml
cd project
python train.py --run_config_name SIMPLE_CONFIG_EXAMPLE_MNIST
```

Optionally you can install project as a package with [setup.py](setup.py)
```bash
pip install -e .
```
<br>


#### PyCharm setup
- open this repository as PyCharm project
- set project interpreter:<br> 
`Ctrl + Shift + A -> type "Project Interpreter"`
- mark folder "project" as sources root:<br>
`right click on directory -> "Mark Directory as" -> "Sources Root"`
- set terminal emulation:<br> 
`Ctrl + Shift + A -> type "Edit Configurations..." -> select "Emulate terminal in output console"`
- run training:<br>
`right click on train.py file -> "Run 'train'"`

#### VS Code setup
- TODO
