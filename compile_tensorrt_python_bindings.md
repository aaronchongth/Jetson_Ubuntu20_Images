# How to compile the .whl file

Using advice from [this issue](https://github.com/timongentzsch/Jetson_Ubuntu20_Images/issues    /12),

Run the image without `pytrt`,

```bash
drun aaroncth/xavier-opencv-pytorch-ros2-galactic-desktop
```

Follow almost all the commands in the gist listed in the issue,

```bash
apt-get update && apt-get install -yqq git build-essential curl wget cmake

EXT_PATH=~/external && \
  mkdir -p $EXT_PATH && cd $EXT_PATH && \
  git clone https://github.com/pybind/pybind11.git && \
  cd pybind11 && git checkout f7b4996

PYTHON_VERSION=`python3 -c 'import platform;print(platform.python_version())'` && \
  EXT_PATH=~/external && \
  mkdir Python-${PYTHON_VERSION} && wget -qO- https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz | tar xvz --strip-components=1 -C Python-${PYTHON_VERSION} && \
  mkdir -p ${EXT_PATH}/python3.8/include && mv Python-${PYTHON_VERSION}/Include/* ${EXT_PATH}/python3.8/include/ && rm -rf Python-${PYTHON_VERSION} && \
  cp /usr/include/aarch64-linux-gnu/python3.8/pyconfig.h ~/external/python3.8/include/

mkdir /workspace && cd /workspace && git clone https://github.com/NVIDIA/TensorRT.git && \
  cd TensorRT && git checkout 6f38570 && \
  mkdir -p /workspace/TensorRT/python/include/onnx

cd /workspace/TensorRT/python && \
  cp -r /usr/local/cuda-10.2/targets/aarch64-linux/include/* ./include/ && \
  cp -r /usr/include/aarch64-linux-gnu/* ./include/onnx/ && \
  EXT_PATH=/root/external PYTHON_MAJOR_VERSION=3 PYTHON_MINOR_VERSION=8 TARGET_ARCHITECTURE=aarch64 ./build.sh && \
  cp ./build/dist/* /mnt/.
```

The `.whl` file can then be found in `/mnt`. This can then be reused in other containers or images.
