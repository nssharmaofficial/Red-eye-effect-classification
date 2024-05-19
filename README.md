# Red eye effect classification

The goal of the assignment is to create a classifier of human eye images which will distinguish images with the red-eye effect.

## Contents

1. [Environment setup](#environment-setup)
1. [Dataset](#dataset)
1. [Model](#model)
1. [Training](#training)

## Environment setup

To build a Docker image:

```bash
docker build -t red-eye-effect-classification/python:v1 -f Dockerfile .
```

To run a Docker container:

```bash
docker run --gpus device=0 -it --entrypoint bash -p 8888:8888 -v "$(pwd)":/red-eye-effect-classification red-eye-effect-classification/python:v1
```

## Dataset

The training and test images are saved in folder `data`.

The dataset contains:

- 80 `normal` training images (label = 0)
- 20 `red` training images (label = 1)
- 50 `normal` test images (label = 0)
- 50 `red` test images (label = 1)

The `dataset.py` script handles the loading, transforming, and batching of images. Running it sets up the dataset paths, creates a custom dataset and data loader with transformations and visualizes some batches of images by saving them to the `dataset_output_images` directory.

![Example of `normal` eye](readme_images/example_normal_eye.png) ![Example of `red` eye](readme_images/example_red_eye.png)

## Model

The CNN is structured with four convolutional layers followed by two fully connected layers. Each convolutional layer is paired with batch normalization and a LeakyReLU activation function. A dropout layer is included before the final fully connected layer to reduce the risk of overfitting.

It has 52042 trainable parameters and the architecture is as follows:

```text
Original:  torch.Size([8, 3, 32, 32])
Conv1:  torch.Size([8, 8, 16, 16])
Conv2:  torch.Size([8, 16, 8, 8])
Conv3:  torch.Size([8, 32, 4, 4])
Conv4:  torch.Size([8, 64, 2, 2])
Out:  torch.Size([8, 2])
```

The `model.py` contains the definition of a CNN designed to classify images into `normal` and `red` eye categories. Running it initializes the setup, loads the datasets, creates a DataLoader, and then instantiates the CNN model. It prints the number of trainable parameters and the input and output size for a batch of images.

## Training

To start training the model, run the `train.py` script. The script:

The model weights are saved in `/checkpoints` and the validation images with true and predicted labels are saved at the end of training in `/training_output_images`.

![Predicted validation images](readme_images/predicted_validation_images.png)

We implemented [learning rate gradual warmup](https://arxiv.org/pdf/1706.02677). Gradual warmup is a technique used to improve the stability of the training process by slowly increasing the learning rate from a small value to a target value over a specified number of epochs. In this project, the `GradualWarmupScheduler` class is implemented to handle this warmup phase. The learning rate is gradually increased to the target value over the first few epochs, and then the normal learning rate schedule resumes.