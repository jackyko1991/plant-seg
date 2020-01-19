![alt text](./plantseg/gui/logo.png)
# PlantSeg
[PlantSeg](plantseg) is a tool for cell instance aware segmentation in densely packed 3D volumetric images.
The pipeline uses a two stages segmentation strategy (Neural Network + Segmentation).
The pipeline is tuned for plant cell tissue acquired with confocal and light sheet microscopy.
Pre-trained models are provided.  

## Getting Started
### Prerequisites
* Linux
* Nvidia GPU + CUDA (Optional)

### Install Anaconda python
- First step required to use the pipeline is installing anaconda python.
 If you already have a working anaconda setup you can go directly to next item. 
To download anaconda python open a terminal and type:
```Bash
$  wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh
```
Then start the install by typing:
```bash
$ bash ./Anaconda3-2019.10-Linux-x86_64.sh
```
Follow the instructions to complete the anaconda installation.

### Install PlantSeg using conda
The tool can installed by executing on the terminal
```
conda create -n plant-seg -c lcerrone -c abailoni -c cpape -c awolny -c conda-forge plantseg
```

### Pipeline Usage (command line):
Our Pipeline is completely configuration file based and does not require any coding.
To run it is necessary to activate the correct anaconda environment. (Assuming the pipeline is setup in 
the default location) Type in the terminal
```bash
$ conda activate plant-seg
```
Now to run the pipeline simply type
```bash
$ plantseg --config ./examples/config.yaml
```

### Pipeline Usage (GUI):
Our Pipeline can be called by using a basic user interface that allow for dynamically run the pipeline.
To run it is necessary to activate the correct anaconda environment. (Assuming the pipeline is setup in 
the default location) Type in the terminal
```bash
$ conda activate plant-seg
```
Now to run the GUI simply type
```bash
$ plantseg --gui
```

### Guide to Custom Configuration File:
The configuration file defines all the operations in our pipeline together with the data to be processed.
Please refer to [config.yaml](examples/config.yaml) for a sample configuration of the pipeline and detailed explanation
of all of the parameters.

Some key design choices:
* `path` attribute: is used to define either the file to process or the directory containing the data.
* `preprocessing` attribute: contains a simple set of possible operation you would need to run on your data before the neural network. 
If data are ready for neural network processing either delete the entire section or set `state: False` in order to skip this step.
* `prediction` attribute: contains all parameters relevant for predicting the neural network. 
In the [models](plantseg/models/README.md) directory we list details of all pre-trained models we provide.
* `segmentation` attribute: contains all parameters needed to run the Multicut pipeline. 
All other pipelines can be run with the same configuration file style but due to anaconda environment incompatibilities 
installing a further environment is needed.
All instructions are in [segmentation](plantseg/segmentation/README.md) directory.

## Troubleshooting:
* **Import Error while predicting**: This could be caused by a non standard location of the 
[pytorch-3dunet](https://github.com/hci-unihd/pytorch-3dunet) directory.
Please edit line 7 of [predict.py](plantseg/predictions/predict.py) with your custom path.
```python
pytorch_3dunet_default_directory = "/CUSTOM_PATH/pytorch-3dunet/"
```

### Setup on Linux:
 To activate the environment restart your terminal.

- Now we can download and configure the pipeline. 
```bash
$ git clone https://github.com/hci-unihd/plant-seg
$ mkdir ~/.plantseg_models/configs/
```
The PlantSeg related files (models, configs) will be placed inside your home directory under `~/.plantseg_models`. 
Create a new environment for PlantSeg by typing:
```bash
$ conda create -n plant-seg python=3.7
$ conda activate plant-seg
$ conda install -c conda-forge h5py pywget pyyaml scikit-learn tifffile scikit-image scipy=1.2
```
Install MultiCut dependency:
```bash
$ conda install -c conda-forge -c cpape elf
```
Install GASP dependency:
```bash
$ conda create -n GASP_env -c abailoni -c conda-forge gasp
```
Our pipeline uses the pytorch library for the CNN predictions. To benefit from the GPU performance boost the right
 version of pytorch must be installed. 
 If your machine has a working version of CUDA installed you can check the version 
 by executing in the terminal:
```` 
cat /usr/local/cuda/version.txt
````
according to the output one must execute on of the following:
```
conda install pytorch torchvision cpuonly -c pytorch # if no gpu is installed in the computer
# or
conda install pytorch torchvision cudatoolkit=9.2 -c pytorch # if your cuda version is 9.2
# or
conda install pytorch torchvision cudatoolkit=10.1 -c pytorch # if your cuda version is 10.1
```
Lastly install the remaining dependencies using:
```
pip install --upgrade tensorflow
pip install tensorboardX
pip install "pillow<7"
```
If you want to use a custom location please look at the troubleshooting guide.

## Training on New Data:
For training new models we rely on the [pytorch-3dunet](https://github.com/hci-unihd/pytorch-3dunet). 
A similar configuration file can be used for training on new data, all detailed instructions can be found  in the repo.
When the network is trained it is enough to copy the following file inside a directory:
* configuration file used for training named: config_train.yml
* model_best.torch
* model_last.torch

The later two files are automatically generated during training and contains all neural networks parameters.
The directory created have to be placed in the default location
```bash
$ ~/.plantseg_models/
```
Now you can simply use your model for prediction by editing the [model_name:](examples/config.yaml)
 key in the prediction config file.\
If you want your model to be part of the open-source zoo of models please contact us.

## Citation:
