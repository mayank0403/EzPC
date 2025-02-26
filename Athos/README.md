# Introduction
This folder contains code for Athos - an end-to-end compiler from TensorFlow to a variety of secure computation protocols.

# Requirements/Setup 
Below we list the packages required to get Athos up and running. We only mention the packages on which the system has been tested thus far. We will update this in due time with more information on requirements. 
- axel: download manager used to download pre-trained models `sudo apt-get install axel`.
- python3.6
- TensorFlow 1.11
- Numpy

# Directory structure
The codebase is organized as follows:
- `HelperScripts`: This folder contains numerous helper scripts which help from automated setup of ImageNet/CIFAR10 dataset to finding accuracy from output files. Please refer to each of the scripts for further instructions on how to use them.
- `Networks`: This folder contains the code in TensorFlow of the various benchmarks/networks we run in CrypTFlow. Among other networks, it includes code for ResNet, DenseNet, SqueezeNet for ImageNet dataset, SqueezeNet for CIFAR10 dataset, Lenet, Logistic Regression, and a chest x-ray demo network.
- `SeeDot`: This contains code for SeeDot, a high-level intermediate language on which Athos performs various optimizations before compiling to MPC protocols.
- `TFCompiler`: This contains python modules which are called from the TensorFlow code for the dumping of TensorFlow metadata (required by Athos for compilation to MPC protocols).
- `TFEzPCLibrary`: This contains library code written in EzPC for the TensorFlow nodes required during compilation.
- `CompileTF.sh`: The Athos compilation script. Try `./CompileTF.sh --help` for options.
- `Paths.config`: This can be used to override the default folders for EzPC and Porthos.

# Usage
Here we provide an example on how to use Athos to compile TensorFlow based ResNet-50 code to Porthos semi-honest 3PC protocol. The relevant TensorFlow code for ResNet-50 can be found in `./Networks/ResNet/ResNet_main.py`.
- Refer to `./Networks/ResNet/README.md` for instructions on how to download and extract the ResNet-50 pretrained model from the official TensorFlow model page.
- `cd ./Networks/ResNet && python3 ResNet_main.py --runPrediction True --scalingFac 12 --saveImgAndWtData True && cd -`
Runs the ResNet-50 code written in TensorFlow to dump the metadata which is required by Athos for further compilation. 
This command execution should result in 2 files which will be used for further compilation - `./Networks/ResNet/graphDef.mtdata` and `./Networks/ResNet/sizeInfo.mtdata`. In addition, a file `./Networks/ResNet/ResNet_img_input.inp` should be created which contains the ResNet-50 pretrained-model and image in a format which can be used later by Porthos.
- `./CompileTF.sh -b 64 -s 12 -t PORTHOS -f ./Networks/ResNet/ResNet_main.py`
Using the `CompileTF.sh` script to compile the model to Porthos. This should result in creation of the file - `./Networks/ResNet/ResNet_main_64_porthos0.cpp`.
- `cp ./Networks/ResNet/ResNet_main_64_porthos0.cpp ../Porthos/main.cpp`
Copy the compiled file to Porthos.
- `cd ../Porthos && make clean && make -j` 
- Finally run the 3 parties. Open 3 terminals and run the following in each for the 3 parties.
`./party0.sh < ../Athos/Networks/ResNet/ResNet_img_input.inp` ,
`./party1.sh` ,
`./party2.sh`.
Once the above runs, the final answer for prediction should appear in the output of party1. For the sample image, this answer should be 249 for ResNet and 248 for DenseNet/SqueezeNet.

Instructions on how to run the particular TensorFlow model in `./Networks` can vary. Please refer to the appropriate readme in each model folder to get more insights. But once that is done, the further compilation commands are the same.

# Preprocessing images and running inference on ImageNet validation dataset
- First setup the ImageNet validation dataset using the script provided in `./HelperScripts/Prepare_ImageNet_Val.sh`. This sets up the ImageNet validation dataset in the folder - `./HelperScripts/ImageNet_ValData`.
- Each of the network folders - `./Networks/ResNet`, `./Networks/DenseNet` and `./Networks/SqueezeNetImgNet` is provided with these folders:
	* `PreProcessingImages`: This folder contains code for preprocessing the images. Code borrowed from the appropriate repository from where the model code is taken
and modified for our purposes (check the apt network folder for more details on the source of the model).
	* `AccuracyAnalysisHelper`: This contains further scripts for automating ImageNet dataset preprocessing and inference. Check the apt scripts for more information.