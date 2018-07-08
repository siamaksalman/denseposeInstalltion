# Installing DensePose
Because installing DensePose is hard, I provided a simple installation guid based on [DensePose Installation](https://github.com/facebookresearch/DensePose/blob/master/INSTALL.md) and the provided [Dockerfile](https://github.com/facebookresearch/DensePose/blob/master/docker/Dockerfile)


**Requirements:**

- NVIDIA GPU, Linux, Python2
- Caffe2, various standard Python packages, and the COCO API; Instructions for installing these dependencies are found below

**Notes:**

- We are using CUDA 9.0 with cuDNN 7.1 for this tutorial


## Getting Started

1. Installing a fresh [Ubuntu 16.04](http://releases.ubuntu.com/16.04/)
  1. updating ubuntu:
  ```bash
  sudo apt-get update
  sudo apt-get upgrade
  ```
  2. reboot for booting up to the last kernel version
  ```bash
  $ reboot
  ```
2. We need to have linux kernel 4.4.0 since it's the only kernel that is supported for CUDA 9.0 and ubuntu repositories only provide linux source for that kernel. For Ubuntu 16.04 LTS on x86-64, both the HWE kernel (4.13.x for 16.04.4) and the server LTS kernel (4.4.x) are supported in CUDA 9.2. Visit https://wiki.ubuntu.com/Kernel/Support for more information.
```bash
sudo apt-get install build-essential
sudo apt-get install linux-image-extra-virtual
sudo apt-get install linux-source
reboot
```
make sure you are booting into the right kernel version ( 4.4.0 ).
since we loaded kernel 4.4.0 we should see the output of uname something like this:
```bash
uname -r
``` 
```
4.4.0- 
```

then we should install the kernel headers
```bash
sudo apt-get source linux-image-$(uname -r)
sudo apt-get install linux-headers-$(uname -r)
```

3. Downloading [cuda 9.0 runfile](https://developer.nvidia.com/cuda-90-download-archive)
4. Download [cudnn7](https://developer.nvidia.com/cudnn) ( possiblly 7.1 )
5. install cuda 9.0:
  1. Create a file at /etc/modprobe.d/blacklist-nouveau.conf with the following contents
  ```
  blacklist nouveau
  options nouveau modeset=0
  ```
  2. Regenerate the kernel initramfs:
  ```bash
  sudo update-initramfs -u
  ```
  3. reboot to runlevel 3 
  adding character 3 to the end of the grub bootloader at boot time will get you to the runlevel 3

  4. go to you cuda directory
  ```bash
  sudo sh ./cuda-installer
  ```

5. installing cudnn 7 using nvidia documentation 
  1. it's quite easy using the [tar file](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html)
  ```bash
  tar -xzvf cudnn-9.0-linux-x64-v7.tgz
  ```
  Copy the following files into the CUDA Toolkit directory.
  ```bash
  sudo cp cuda/include/cudnn.h /usr/local/cuda/include
  sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
  sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
  ```

6. next we have to download [Nvidia NCCL for ubuntu](https://developer.nvidia.com/nccl)
  1. installing nccl using [this guide](https://docs.nvidia.com/deeplearning/sdk/nccl-install-guide/index.html)

7. we should also download [anaconda](https://conda.io/docs/user-guide/install/download.html#anaconda-or-miniconda) to install caffe2  
  1. Download Python 2.7 version from [this link](https://www.anaconda.com/download/)
  ```bash
  chmod +x Anaconda2-5.2.0-Linux-x86_64.sh
  ./Anaconda2-5.2.0-Linux-x86_64.sh
  ```
  2. the installation will ask you for some location but for testing the default ones will work
  3. it will install anaconda to your home directory


## Result





## With Love

[TerraUnity Team](http://terraunity.com)![alt text](http://terraunity.com/wp-content/uploads/2015/03/TerraUnity_Logo.png "TerraUnity Logo")

