# TRAFFIC SIGN CLASSIFICATION
This project builds a machine learning model using the python language and leveraging on a convolutional neural network for classification of 58 distinct traffic signs. Each image is converted to a tensor within Python script "traffic.py" to aid training.

## Prerequisites
The ML operations within this repo was implemented using Azure machine learning (AML) studio and Azure Devops. A more general approach is to implement the CI/CD with docker but due to technical issues during the creation of this project, Azure DevOps was adopted. The following prerequisites are required to make this repository work:
- Azure subscription
- Owner or contributor access to the Azure subscription
- Azure [DevOps account](https://dev.azure.com/)
- Create an Azure resource group
- Azure workspace within that resource group
- Create a compute instance/cluster running in [AML studio](https://ml.azure.com)


## Data Preparation
- The [data](https://www.kaggle.com/datasets/ahemateja19bec1025/traffic-sign-dataset-classification?resource=download&select=labels.csv) for this project was downloaded from the Kaggle site. It contains
58 distinct traffic signs resulting in 58 labels to be classified
- The data in the DATA folder is split into two seperate folders, 'train' and 'val', holding 80% and 20% respectively, of the total set.
- Folders 'train' and 'val' are then imported to the python script, taffic.py.

## Python script
- The traffic.py file contains the python code used to develop this model.
- The [requirements](https://github.com/tobsiee/HAck/blob/main/requirements.txt) 
for this file can be installed by:
```python
pip install requirements.txt
```
- Within this script, the [PyTorch framework](https://pytorch.org/) is adopted. It is a widely utilised framework for building and training neural networks and can be imported by:
```python
    import torch as torch
```
## Neural Network - The Model
- A pretrained CNN, RESNET-50 is used to train the model via transfer learning - output size is adjusted to meet the number of classes intended for classification. It contains 50 layers as the name connotes; 48 Convolutional layers, 1 MaxPool and 1 Average Pool layer.
```python
    resnet50 = models.resnet50(pretrained=True)
```
- For faster re-training of the model, support for NVIDIA GPU's is integrated within the [traffic.py](https://github.com/tobsiee/HAck/blob/main/traffic.py) script in line 71 as: 
```python
resnet50 = resnet50.to('cuda:0')  for NVIDIA GPU
```
Note that it needs to be uncommented for it to function.
- The Adam optimizer is used with this network and its learning rate is kept at a standard value of 0.001 but can be adjusted 
```python
optimizer = optim.Adam(resnet50.parameters(), lr=0.001)
```
- The model is then saved in folder named 'models'

## CI/CD Pipeline
- For integrating and deploying this model automatically, the azure DevOps tool is utilised
- A script named 'start_experiment.py' defines the environment, model, Script run configuration and retrieves the python script, 'traffic.py', for retraining the model
- Also wihin this script, the azure compute instance created is defined as well as the workspace, subscription ID, and resoure-group are specified. This can always be modified.
```python

compute_name = 'hackcompute22'

experiment_name = 'my-experiment' 
environment_name = 'my-environment'

model_name = 'my-model'
model_path = 'outputs/model.pkl'


source_directory = '.'

script_path = 'Hack Partners/traffic.py'


subscription_id = '4f67948b-2ff9-49ee-bf1f-90c32dc7545e'
resource_group  = 'cloud-shell-storage-westeurope'
workspace_name  = 'Hack_Workspace'
```

- The Yml file, [azure-pipelines.yml](https://github.com/tobsiee/HAck/blob/main/azure-pipelines.yml) then runs this script, 'start_experiment.py'
- Within this file, the GitHub branch where the pipeline retrieves and runs within is specified - 'main'. In the long run, this could be changed. The python 3.9 version is also specified to be used and the Azure mnachine learning sdk is installed.
- The pipeline file triggers the [experiment file](https://github.com/tobsiee/HAck/blob/main/start_experiment.py) to run thereby re-training the model in the [traffic.py](https://github.com/tobsiee/HAck/blob/main/traffic.py) script at intervals when a push to the traffic.py file is made.


# update file
