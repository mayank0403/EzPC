# Introduction
This folder contains code for Porthos - a semi-honest 3 party secure computation protocol.

# Setup
(Ubuntu): 
* sudo apt-get install libssl-dev
* sudo apt-get install g++
* sudo apt-get install make

# Running the protocol
- First setup Eigen library, used for fast matrix multiplication by Porthos, by running `./setup-eigen.sh`.
- To compile use `make clean && make -j`.
- Currently the codebase contains precompiled code the following 3 neural networks: ResNet-50, DenseNet-121 and SqueezeNet for ImageNet, checked into the following folder: `./src/example_neural_nets`. Toggle the flag in `./src/example_neural_nets/network_config.h` to switch the network which runs. Note that if there is more than one network flag uncommented (meaning ON) or if there is already a main file in src, the compilation will error out saying multiple declarations of main function.
- To run for example the ResNet-50 code, use the following commands:
`./party0.sh < ../Athos/Networks/ResNet/ResNet_img_input.inp`,
`./party1.sh`, and
`./party2.sh`.
Note that the first command makes use of input generated from Athos. Please refer to the Athos readme on how to do the same. 

# External Code
- `util/` directory contains code taken from SCAPI project.
- `basicSockets` files contain code writen by Roi Inbar. We have modified this code for crypTFlow.
- `tools.cpp` contains some code written by Anner Ben-Efraim and Satyanarayana. This code is majorly modified for crypTFlow.

# Notes
- If porthosSecretType != uint64_t, porthosSecretType multiplication function won't work.
- Possible instructions for installation of libssl on Mac, though untested.
-- brew install libssl
-- cd /usr/local/include
-- ln -s ../opt/openssl/include/openssl . 
Also, use the second build command for Mac to be safe. Read the Caveats part of brew install instructions, since Apple has deprecated the use of OpenSSL.

- When extracting bits out of \_\_m128i, val[0] corresponds to the LSB. The set functions of \_\_m128i take the first argument as val[0].

- Matrix multiplication assembly code only works for Intel C/C++ compiler. The non-assembly code has correctness issues when both the multiplicands are large uint64_t's

# Acknowledgement
This codebase is based on an early fork of [this github repo](https://github.com/snwagh/securenn-public).
