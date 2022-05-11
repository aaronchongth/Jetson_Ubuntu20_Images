# ROS2 Galactic and python bindings for TensorRT

This setup uses Jetpack 4.6.2 (TensorRT 8.2, python binding building support only started in 8).

Working docker image for Xavier NX freshly flashed with Jetpack 4.6.2 (no `apt update` or `apt upgrade`).

```bash
drun aaroncth/xavier-opencv-pytorch-ros2-galactic-desktop
drun aaroncth/xavier-opencv-pytorch-ros2-galactic-desktop-pytrt
```

Using the advice from [this issue](https://github.com/timongentzsch/Jetson_Ubuntu20_Images/issues/12), the `.whl` file was compiled (see `compile_tensorrt_python_bindings.md`), and has been committed under `assets`, users should be able to install the python bindings in the docker images for TensorRT 8.2 by calling,

```bash
git clone https://github.com/aaronchongth/Jetson_Ubuntu20_Images
pip3 install Jetson_Ubuntu20_Images/assets/tensorrt-8.2.1.8-cp38-none-linux_aarch64.whl
```

Other changes that had to be made in `aaroncth/xavier-opencv-pytorch-ros2-galactic-desktop-pytrt` would be the `gcc` and `g++` version, it looks like `gcc-7` and `g++-7` is still preferred when working with `nvcc`. Instructions from [here](https://askubuntu.com/questions/26498/how-to-choose-the-default-gcc-and-g-version) was followed, after running,

```bash
apt install build-essential

# follow instructions in link
# afterwards check with
gcc -v
g++ -v
```

Everything below this divider was from the original author.

---

# Hardware accelerated Docker images based on Ubuntu 20.04 for Jetson family

  

Nvidia Jetson images are based on Ubuntu 18.04. However, many applications and projects utilizes libraries specific to Ubuntu 20.04. Therefore, this repository provides docker images based on [`ubuntu:focal`](Dockerfile.ros2), that are able to take full advantage of the Jetson hardware (Nano, Xavier NX, Xavier AGX and Xavier TX2). All images come with full CUDA support (passthrough from the host) including TensorRT (including python bindings) and VisionWorks.

  

Furthermore the [script](scripts) folder includes system installable run-scripts to quickly iterate the build process and set the relevant docker flags at runtime.

  

The following images can be directly pulled from DockerHub without needing to build the containers yourself:

  


|                        | L4T Version | Dockerhub image                                    |
|------------------------|:-----------:|----------------------------------------------------|
| [`l4t-ubuntu20-base`](Dockerfile.base) | R32.6.1 | `timongentzsch/l4t-ubuntu20-base:latest` |
| [`l4t-ubuntu20-opencv`](Dockerfile.opencv) | R32.6.1 | `timongentzsch/l4t-ubuntu20-opencv:latest` |
| [`l4t-ubuntu20-pytorch`](Dockerfile.pytorch) | R32.6.1 | `timongentzsch/l4t-ubuntu20-pytorch:latest` |
| [`l4t-ubuntu20-ros2-base`](Dockerfile.ros2-base) | R32.6.1 | `timongentzsch/l4t-ubuntu20-ros2-base:latest` |
| [`l4t-ubuntu20-ros2-desktop`](Dockerfile.ros2-desktop) | R32.6.1 | `timongentzsch/l4t-ubuntu20-ros2-desktop:latest` |
| [`l4t-ubuntu20-zedsdk`](Dockerfile.zedsdk) | R32.6.1 | `timongentzsch/l4t-ubuntu20-zedsdk:latest` |
| [`l4t-ubuntu20-crosscompile`](Dockerfile.crosscompile) | R32.6.1 | `timongentzsch/l4t-ubuntu20-crosscompile:latest` |

  
>  **note:** make sure to run the container on the intended L4T host system. Running on older JetPack releases (e.g. r32.4.4) can cause driver issues, since L4T drivers are passed into the container.

  

To download and run one of these images, you can use the included run script from the repo:

  

``` bash

$ scripts/docker_run timongentzsch/l4t-ubuntu20-base:latest

```

  

For other configurations, below are the instructions to build and test the containers using the included Dockerfiles.

  

## Docker Default Runtime

  

To enable access to the CUDA compiler (nvcc) during `docker build` operations, add `"default-runtime": "nvidia"` to your `/etc/docker/daemon.json` configuration file before attempting to build the containers:

  

``` json

{

"runtimes": {

  "nvidia": {

    "path": "nvidia-container-runtime",

    "runtimeArgs": []

  }

 },

"default-runtime": "nvidia"

}

```

  

You will then want to restart the Docker service or reboot your system before proceeding.

  

## Build and test the images

  

To rebuild the containers from a Jetson device, first clone this repo via [`Git LFS`](https://git-lfs.github.com/):

  

``` bash

$ git clone https://github.com/timongentzsch/Jetson_Ubuntu20_Images.git

$ cd Jetson_Ubuntu20_Images

```

  

## Work with the provided scripts

  

You may want to install the provided scripts to build, run and restart containers with the right set of docker flags:

  

``` bash

$ sudo scripts/install-scripts.sh

```

  

This will enable you to quickly iterate your build process and application.

  

After that you can use following commands globally:

  

`dbuild`, `drun`, `dstart`

  

It ensures that the docker environment feels as native as possible by enabling the following features by default:

- USB hot plug

- sound

- network

- bluetooth

- GPU/cuda

- X11

  

>  **note:** refer to `--help` for the syntax
