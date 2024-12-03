---
title: "How to Structure a Deep Learning Reseach Project"
date: 2024-12-03
slug: guide-pytorch
category: research
summary:
author: Mehdi Adjal Zakaria
description:
cover:
  image:
  alt:
  caption:
  relative: true
showtoc: true
draft: false
---
# Introduction

When working on a research project especially large scale ones it is necessary to organize the work and the choice frameworks from the beginning, inorder to not get overwhelmed by the sheer volume of experiments and to keep track of the diffrent aspects of your research project ( improvments, experiments ... ).

In this short article I will be presenting a standard simple way of organizing your work and experiments, the choice of frameworks and the overall flow.

*Note : this is not a must follow kind of article think of it as an advice that can be modified to tailor your needs*

# What Deep learning framework I should use ?
Selecting the right framework is crucial for efficient model development and scaling. If your training process primarily involves standard optimization loops, such as forward passes and loss computation (e.g., training a ResNet on ImageNet), PyTorch Lightning is an excellent choice. It streamlines code, minimizes boilerplate (i.e., sections of code that are repeated in multiple places with little to no variation), and makes scaling across multiple GPUs or nodes as simple as adding a Trainer(gpus=4) configuration. 

However, if you anticipate needing significant customizations to the training loop or optimization process—such as implementing a GAN with separate generator and discriminator training steps or integrating a custom reinforcement learning algorithm—plain PyTorch might be better. While it requires more effort to set up, it provides greater flexibility, allowing you to design bespoke workflows tailored to these advanced scenarios without being constrained by predefined workflows.

*Note : PyTorch Lightning can still handle significant customizations to the training loop. It provides flexibility through well-defined hooks like `training_step`, `validation_step`. That said, while Lightning simplifies many customizations, there might still be edge cases where its structure could feel restrictive. In such edge cases some prefer JAX even over PyTorch.*

# Files Structure

The following is a suggested file structure for a deep learning research project. This structure organizes the project into clearly defined components, making it modular, scalable, and easier to maintain. It is designed to accommodate multiple tasks or experiments, shared utilities, and reproducible workflows.

```
Project
├── utils/
|   ├── dataset.py         # Contains data loading and preprocessing functions or classes.
|   └── utils.py           # General-purpose utility functions shared across the project.
├── data/
│   ├── raw/               # Directory for storing raw datasets.
│   └── processed/         # Directory for storing processed datasets or intermediate files.
├── layers/
│                          # Directory for custom neural network layers or modules.
├── experiments/
│   ├── task1/
│   │   ├── train.py       # Script for training models for Task 1.
│   │   ├── eval.py        # Script for evaluating models for Task 1.
│   │   └── results/         # Directory for storing Task 1 outputs.
|   |       └── run1/
│   │           ├── logs/      # Training logs, such as loss and metric curves.
│   │           └── checkpoints/ # Saved model weights or checkpoints.
│   └── task2/
│       ├── train.py       # Script for training models for Task 2.
│       ├── eval.py        # Script for evaluating models for Task 2.
│       └── results/         # Directory for storing Task 1 outputs.
|           └── run1/
│               ├── logs/      
│               └── checkpoints/ 
├── trainers/
│   ├── base_trainer.py    # Abstract or base class for shared training logic.
│   ├── task1_trainer.py   # Trainer implementation specific to Task 1.
│   └── task2_trainer.py   # Trainer implementation specific to Task 2.
├── scripts/               # Auxiliary scripts, such as running mass experiments in an HPC.
├── requirements.txt       # List of dependencies required for the project.
└── README.md              # Documentation for setting up and using the project.

```

### Key Features
* **Modularity:** The project is divided into logical components (utils, trainers, experiments) to ensure each piece has a specific role.
* **Reusability:** Shared utilities, like dataset.py and utils.py, centralize functionality used across tasks.
* **Task Separation:** Each task has its own directory under experiments/, keeping training, evaluation, and results organized.
* **Reproducibility:** The data/ directory and results/ subdirectories store raw data, processed data, and experiment outputs whith checkpointing in a reproducible way.
* **Flexibility:** Custom layers, trainers, and scripts are separated to support project scalability and experimentation.
This structure ensures that the project remains manageable and adaptable, even as it grows in complexity or scope.

*Note: Needless to say the structor can be tailored to your need, but overall this may give you an idea on how generally a well structured project would looklike ( e.g., when using Lightning you may think of abstracting the Lightning Module, you may choose to put the experiments in their own seperate directory...)*.

# Argument Parser in Deep Learning Projects
When working on deep learning projects, it's a good practice to use an argument parser for managing hyperparameters and configuration settings. An argument parser provides a clear, flexible, and maintainable way to handle command-line arguments (e.g., hyperparameters like learning rate, batch size, and number of hidden units). Instead of hardcoding these parameters or passing them manually every time, you can specify them when running a script, making your code more reusable and scalable.

For instance, if you're training a model, you can run a command like:

```
python train.py --learning_rate 0.001 --batch_size 32 --epochs 20 --hidden_size 128
```

*Note: check the [lightning doc](https://pytorch-lightning.readthedocs.io/en/latest/common/hyperparameters.html#argparser-best-practices) to get a more detailed overview on the matter.*

# Adding Flag Files to Track Experiment Status
In deep learning research, it's common to run multiple experiments, sometimes over long periods. To avoid re-running experiments that have already been completed, or to prevent wasting time on experiments that failed or were interrupted, it's a good practice to track the status of each run using flag files. These flag files help manage experiment state, ensuring that experiments aren't unnecessarily repeated and allowing you to quickly identify the status of any particular run.

### Why Use Flag Files?
Flag files serve as markers for experiment states, such as:

* ``finished.out``: Indicates that the experiment has successfully completed.
* ``interrupted.out``: Marks that the experiment was interrupted, possibly due to a crash, power failure, or manual termination.
* ``failed.out``: Signals that the experiment ended in failure due to some error or poor performance.
These flag files are placed in the experiment's result directory or a dedicated folder and can be checked before starting an experiment to determine whether it needs to be re-run.

### How to Implement Flag Files
1. Creating Flag Files: During the experiment, you can create a flag file at the end of the script if it completes successfully. In case of interruption or failure, the corresponding flag file is created to reflect the experiment's current state.

2. Checking Flag Files: Before starting a new experiment, you can check if a flag file exists for the particular experiment. If it does, you can skip the experiment or handle it accordingly.

# Overall Best Practices for Deep Learning Research Projects
Here are some additional best practices to improve the workflow, reproducibility, and trackability of your deep learning projects:

* Tracking your experiments, hyperparameters, metrics, and visualizations is essential for deep learning research. Tools like Weights & Biases (W&B) or TensorBoard are invaluable in this regard.
* Deep learning models are highly dependent on random initialization (e.g., weight initialization, batch shuffling, etc.). To ensure that your experiments are reproducible, it's important to set a random seed for all libraries that involve randomness.
* Always document your code clearly

# Conclusion
By following these practices, you not only improve the quality and reproducibility of your research, but also make it easier to scale, collaborate, and iterate on your experiments. Experiment tracking, reproducibility through seed setting, and efficient file management (e.g., flag files, modular code) are essential for deep learning research, ensuring that you can confidently build upon your work.

That was it, good luck with your work, feel free to share any thoughts.

Thank you and Free Palestine