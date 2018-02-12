## Setting up a Deep Learning Machine from Scratch (Software)
A detailed guide to setting up your machine for deep learning research. Includes instructions to install drivers, tools and various deep learning frameworks. This was tested on a 64 bit machine with Nvidia Titan X, running Ubuntu 14.04

There are several great guides with a similar goal. Some are limited in scope, while others are not up to date. This guide is based on (with some portions copied verbatim from):
* [Caffe Installation for Ubuntu](https://github.com/tiangolo/caffe/blob/ubuntu-tutorial-b/docs/install_apt2.md)
* [Running a Deep Learning Dream Machine](http://graphific.github.io/posts/running-a-deep-learning-dream-machine/)

### Table of Contents
* [Basics](#basics)
* [Nvidia Drivers](#nvidia-drivers)
* [CUDA](#cuda)
* [cuDNN](#cudnn)
* [Python Packages](#python-packages)
* [Tensorflow](#tensorflow)
* [OpenBLAS](#openblas)

### Basics
* First, open a terminal and run the following commands to make sure your OS is up-to-date

        sudo apt-get update  
        sudo apt-get upgrade  
        sudo apt-get install build-essential cmake g++ gfortran git pkg-config python-dev software-properties-common wget
        sudo apt-get autoremove 
        sudo rm -rf /var/lib/apt/lists/*

### Nvidia Drivers
* Find your graphics card model

        lspci | grep -i nvidia

* Go to the [Nvidia website](http://www.geforce.com/drivers) and find the latest drivers for your graphics card and system setup. You can download the driver from the website and install it, but doing so makes updating to newer drivers and uninstalling it a little messy. Also, doing this will require you having to quit your X server session and install from a Terminal session, which is a hassle. 
* We will install the drivers using apt-get. Check if your latest driver exists in the ["Proprietary GPU Drivers" PPA](https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa). Note that the latest drivers are necessarily the most stable. It is advisable to install the driver version recommended on that page. Add the "Proprietary GPU Drivers" PPA repository. At the time of this writing, the latest version is 361.42, however, the recommended version is 352:

        sudo add-apt-repository ppa:graphics-drivers/ppa
        sudo apt-get update
        sudo apt-get install nvidia-352

* Restart your system

        sudo shutdown -r now
        
* Check to ensure that the correct version of NVIDIA drivers are installed

        cat /proc/driver/nvidia/version
        
### CUDA
* Download CUDA 7.5 from [Nvidia](https://developer.nvidia.com/cuda-toolkit). Go to the Downloads directory and install CUDA

        sudo dpkg -i cuda-repo-ubuntu1404*amd64.deb
        sudo apt-get update
        sudo apt-get install cuda
        
* Add CUDA to the environment variables

        echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
        echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
        source ~/.bashrc
        
* Check to ensure the correct version of CUDA is installed

        nvcc -V
        
* Restart your computer

        sudo shutdown -r now
        
#### Checking your CUDA Installation (Optional)
* Install the samples in the CUDA directory. Compile them (takes a few minutes):

        /usr/local/cuda/bin/cuda-install-samples-7.5.sh ~/cuda-samples
        cd ~/cuda-samples/NVIDIA*Samples
        make -j $(($(nproc) + 1))
        
**Note**: (`-j $(($(nproc) + 1))`) executes the make command in parallel using the number of cores in your machine, so the compilation is faster

* Run deviceQuery and ensure that it detects your graphics card and the tests pass

        bin/x86_64/linux/release/deviceQuery
        
### cuDNN
* cuDNN is a GPU accelerated library for DNNs. It can help speed up execution in many cases. To be able to download the cuDNN library, you need to register in the Nvidia website at [https://developer.nvidia.com/cudnn](https://developer.nvidia.com/cudnn). This can take anywhere between a few hours to a couple of working days to get approved. Once your registration is approved, download **cuDNN v4 for Linux**. The latest version is cuDNN v5, however, not all toolkits support it yet.

* Extract and copy the files

        cd ~/Downloads/
        tar xvf cudnn*.tgz
        cd cuda
        sudo cp */*.h /usr/local/cuda/include/
        sudo cp */libcudnn* /usr/local/cuda/lib64/
        sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
        
### Check
* You can do a check to ensure everything is good so far using the `nvidia-smi` command. This should output some stats about your GPU

### Python Packages
* Install some useful Python packages using apt-get. There are some version incompatibilities with using pip install and TensorFlow ( see https://github.com/tensorflow/tensorflow/issues/2034)
 
        sudo apt-get update && apt-get install -y python-numpy python-scipy python-nose \
                                                python-h5py python-skimage python-matplotlib \
		                                python-pandas python-sklearn python-sympy
        sudo apt-get clean && sudo apt-get autoremove
        rm -rf /var/lib/apt/lists/*
 

### Tensorflow
* This installs v0.8 with GPU support. Instructions below are from [here](https://www.tensorflow.org/versions/r0.8/get_started/os_setup.html)

        sudo apt-get install python-pip python-dev
        sudo pip install --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.8.0-cp27-none-linux_x86_64.whl

* Run a test to ensure your Tensorflow installation is successful. When you execute the `import` command, there should be no warning/error.

        python
        >>> import tensorflow as tf
        >>> exit()
      
### OpenBLAS 
* OpenBLAS is a linear algebra library and is faster than Atlas. This step is optional, but note that some of the following steps assume that OpenBLAS is installed. You'll need to install gfortran to compile it.

        mkdir ~/git
        cd ~/git
        git clone https://github.com/xianyi/OpenBLAS.git
        cd OpenBLAS
        make FC=gfortran -j $(($(nproc) + 1))
        sudo make PREFIX=/usr/local install
        
* Add the path to your LD_LIBRARY_PATH variable

        echo 'export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
        

