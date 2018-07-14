# Installing DensePose
Because installing DensePose is hard, I provided a simple installation guid based on [DensePose Installation](https://github.com/facebookresearch/DensePose/blob/master/INSTALL.md) and the provided [Dockerfile](https://github.com/facebookresearch/DensePose/blob/master/docker/Dockerfile).


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
        reboot
        ```
2. We need to have linux kernel 4.4.0 since it's the only kernel that is supported for CUDA 9.0 and ubuntu repositories only provide linux source for that kernel. For Ubuntu 16.04 LTS on x86-64, both the HWE kernel (4.13.x for 16.04.4) and the server LTS kernel (4.4.x) are supported in CUDA 9.2. Visit <https://wiki.ubuntu.com/Kernel/Support> for more information.
    ```bash
    sudo apt-get install build-essential
    sudo apt-get install linux-image-extra-virtual
    sudo apt-get install linux-source
    reboot
    ```
    make sure you are booting into the right kernel     version ( 4.4.0 ).
    since we loaded kernel 4.4.0 we should see the output     of uname something like this:
    ```bash
    uname -r
    ``` 
    ```
    4.4.0-128-generic
    ```

    then we should install the kernel headers
    ```bash
    sudo apt-get source linux-image-$(uname -r)
    sudo apt-get install linux-headers-$(uname -r)
    ```

3. Downloading [cuda 9.0 runfile](https://developer.nvidia.com/cuda-90-download-archive)
4. Download [cudnn7](https://developer.nvidia.com/cudnn) ( possibly 7.1 )
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

6. installing cudnn 7 using nvidia documentation 
    1. it's quite easy using the [tar file](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html)
        ```bash
        tar -xzvf cudnn-9.0-linux-x64-v7.tgz
        ```
        Copy the following files into the CUDA Toolkit directory.
        ```bash
        sudo cp cuda/include/cudnn.h      /usr/local/cuda/include
        sudo cp cuda/lib64/libcudnn*      /usr/local/cuda/lib64
        sudo chmod a+r  /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
        ```

7. Next we have to download [Nvidia NCCL for ubuntu](https://developer.nvidia.com/nccl)
    1. installing nccl using [this guide](https://docs.nvidia.com/deeplearning/sdk/nccl-install-guide/index.html)

8. We should also download [anaconda](https://conda.io/docs/user-guide/install/download.html#anaconda-or-miniconda) to install caffe2  
    1. Download Python 2.7 version from [this link](https://www.anaconda.com/download/)
        ```bash
        chmod +x Anaconda2-5.2.0-Linux-x86_64.sh
        ./Anaconda2-5.2.0-Linux-x86_64.sh
        ```
    2. The installation will ask you for some location but for testing the default ones will work
    3. It will install anaconda to your home directory
    4. We assume that anaconda is installed in our home directory and the path looks like this:
        ```bash
        /home/terra/anaconda2  
        ```
        adding the path to our environment path
        ```bash
        
        export PATH="/home/terra/anaconda2/bin${PATH:+:${PATH}}"
        export PATH="/usr/local/cuda/bin${PATH:+:${PATH}}"
        export LD_LIBRARY_PATH=/usr/local/cuda/lib64\
                                ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
        ```
9. Installing caffe2
    1. By running this command conda will try to install caffe2 with cuda support.
        ```bash
        conda install -c caffe2 caffe2-cuda9.0-cudnn7
        ```
    2. Verifing caffe2 installation:
        ```bash
        python2 -c 'from caffe2.python import     workspace; print    (workspace.NumCudaDevices())'
        ```
10. Install the [COCO API](https://github.com/cocodataset/cocoapi):

    ```
    # COCOAPI=/path/to/clone/cocoapi
    git clone https://github.com/cocodataset/cocoapi.git    $COCOAPI
    cd $COCOAPI/PythonAPI
    # Install into global site-packages
    make install
    # Alternatively, if you do not have permissions or    prefer
    # not to install the COCO API into global     site-packages
    python2 setup.py install --user
    ```
    
    Note that instructions like `#    COCOAPI=/path/to/install/cocoapi` indicate that you   should pick a path where you'd like to have the    software cloned and then set an environment variable    (`COCOAPI` in this case) accordingly.


11. Finally Installing Densepose

    Clone the Densepose repository:

    ```
    # DENSEPOSE=/path/to/clone/densepose
    git clone https://github.com/facebookresearch/densepose $DENSEPOSE
    ```

    Install Python dependencies:

    ```
    pip install -r $DENSEPOSE/requirements.txt
    ```

    Set up Python modules:

    ```
    cd $DENSEPOSE && make
    ```

    Check that Detectron tests pass (e.g. for [`SpatialNarrowAsOp test`](tests/test_spatial_narrow_as_op.py)):

    ```
    python2 $DENSEPOSE/detectron/tests/test_spatial_narrow_as_op.py
    ```

    Build the custom operators library:

    ```
    cd $DENSEPOSE && make ops
    ```

    Check that the custom operator tests pass:

    ```
    python2 $DENSEPOSE/detectron/tests/test_zero_even_op.py
    ```
    ### Fetch DensePose data.
    Get necessary files to run, train and evaluate DensePose.
    ```
    cd $DENSEPOSE/DensePoseData
    bash get_densepose_uv.sh
    ```
    For training, download the DensePose-COCO dataset:
    ```
    bash get_DensePose_COCO.sh
    ```
    For evaluation, get the necessary files:
    ```
    bash get_eval_data.sh
    ```
    ## Setting-up the COCO dataset.

    Create a symlink for the COCO dataset in your `datasets/data` folder.
    ```
    ln -s /path/to/coco $DENSEPOSE/detectron/datasets/data/coco
    ```

    Create symlinks for the DensePose-COCO annotations

    ```
    ln -s $DENSEPOSE/DensePoseData/DensePose_COCO/densepose_coco_2014_minival.json $DENSEPOSE/detectron/datasets/data/coco/annotations/
    ln -s $DENSEPOSE/DensePoseData/DensePose_COCO/densepose_coco_2014_train.json $DENSEPOSE/detectron/datasets/data/coco/annotations/
    ln -s $DENSEPOSE/DensePoseData/DensePose_COCO/densepose_coco_2014_valminusminival.json $DENSEPOSE/detectron/datasets/data/coco/annotations/
    ```

    Your local COCO dataset copy at `/path/to/coco` should have the following directory structure:

    ```
    coco
    |_ coco_train2014
    |  |_ <im-1-name>.jpg
    |  |_ ...
    |  |_ <im-N-name>.jpg
    |_ coco_val2014
    |_ ...
    |_ annotations
      |_ instances_train2014.json
      |_ ...
    ```
## Results

### First Try
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/first/TGC2018_3.jpg" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/first/demo_im.jpg" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/first/demo_im_INDS.png" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/first/demo_im_IUV.png" width="200">


### Second Try

<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/second/photo_2018-07-14_13-05-46.jpg" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/second/demo_im.jpg" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/second/demo_im_INDS.png" width="200">
<img src="https://raw.githubusercontent.com/siamaksalman/denseposeInstalltion/master/second/demo_im_IUV.png" width="200">


## Issues
Feel free to open an issue if you find anything that needs to be improved. I will try to keep this guide updated.

## With Love

[TerraUnity Team](http://terraunity.com)</br>
<img src="http://terraunity.com/wp-content/uploads/2015/03/TerraUnity_Logo.png" width="150">


<a href=https://discord.gg/tCyWVhn>
<img src="https://camo.githubusercontent.com/6a38501083280e001a6dc5daf56b7debae02dade/68747470733a2f2f646973636f72646170702e636f6d2f6173736574732f66633062303166653130613062386336303266623031303664383138396439622e706e67" width="150"></a>

