![](images/rust-mmdeploy-logo.png)

[![crates.io](https://img.shields.io/crates/v/mmdeploy)](https://crates.io/crates/mmdeploy)

## Introduction

Safe MMDeploy Rust wrapper.

## News

- (2022.9.29) This repo has been added to the [OpenMMLab ecosystem](https://openmmlab.com/ecosystem).
- (2022.9.27) This repo has been added to the [MMDeploy CI](https://github.com/open-mmlab/mmdeploy/blob/master/.github/workflows/rust_api.yml).

## Prerequisites

To make sure the building of this repo successful, you should install some pre-packages.

The following guidance is tested on Ubuntu OS on x86 device.

**Step 0.** Install Rust if you don't have.

```bash
apt install curl
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

**Step 1.** Install Clang and Rust required by `Bindgen`.

```bash
apt install llvm-dev libclang-dev clang
```

**Step 2.** Download and install pre-built mmdeploy package. Currently, `mmdeploy-sys` is built upon the pre-built package of `mmdeploy` so this repo only supports OnnxRuntime and TensorRT backends. Don't be disappoint, the script of building from source is ongoing, and after finishing that we can deploy models with all backends supported by `mmdeploy` in Rust.

```bash
apt install wget
```

**If you want to deploy models with OnnxRuntime:**

```bash
# Download and link to MMDeploy-onnxruntime pre-built package
wget https://github.com/open-mmlab/mmdeploy/releases/download/v0.9.0/mmdeploy-0.9.0-linux-x86_64-onnxruntime1.8.1.tar.gz
tar -zxvf mmdeploy-0.9.0-linux-x86_64-onnxruntime1.8.1.tar.gz
pushd mmdeploy-0.9.0-linux-x86_64-onnxruntime1.8.1
export MMDEPLOY_DIR=$(pwd)/sdk
export LD_LIBRARY_PATH=$MMDEPLOY_DIR/sdk/lib:$LD_LIBRARY_PATH
popd

# Download and link to OnnxRuntime engine
wget https://github.com/microsoft/onnxruntime/releases/download/v1.8.1/onnxruntime-linux-x64-1.8.1.tgz
tar -zxvf onnxruntime-linux-x64-1.8.1.tgz
cd onnxruntime-linux-x64-1.8.1
export ONNXRUNTIME_DIR=$(pwd)
export LD_LIBRARY_PATH=$ONNXRUNTIME_DIR/lib:$LD_LIBRARY_PATH
```

**If you want to deploy models with TensorRT:**

Pay attention to the version of cuda: 11. So this script is only supported for machines with cuda-11.x.

```bash
# Download and link to MMDeploy-tensorrt pre-built package
wget https://github.com/open-mmlab/mmdeploy/releases/download/v0.9.0/mmdeploy-0.9.0-linux-x86_64-cuda11.1-tensorrt8.2.3.0.tar.gz
tar -zxvf mmdeploy-0.9.0-linux-x86_64-cuda11.1-tensorrt8.2.3.0.tar.gz
pushd mmdeploy-0.9.0-linux-x86_64-cuda11.1-tensorrt8.2.3.0
export MMDEPLOY_DIR=$(pwd)/sdk
export LD_LIBRARY_PATH=$MMDEPLOY_DIR/sdk/lib:$LD_LIBRARY_PATH
popd

# Download and link to TensorRT engine
# !!! Download TensorRT-8.2.3.0 CUDA 11.x tar package from NVIDIA, and extract it to the current directory. This link maybe helpful: https://developer.nvidia.com/nvidia-tensorrt-8x-download.
export TENSORRT_DIR=$(pwd)/TensorRT-8.2.3.0
export LD_LIBRARY_PATH=${TENSORRT_DIR}/lib:$LD_LIBRARY_PATH

# Download and link to CUDA and cuDNN libraries
# !!! Download cuDNN 8.2.1 CUDA 11.x tar package from NVIDIA, and extract it to the current directory. This two links are maybe helpful: CUDA: https://developer.nvidia.com/cuda-downloads; cuDNN: https://developer.nvidia.com/rdp/cudnn-download.
export CUDNN_DIR=$(pwd)/cuda
export LD_LIBRARY_PATH=$CUDNN_DIR/lib64:$LD_LIBRARY_PATH
```

**Step 3.** (Optional) Install OpenCV required by examples.

```bash
apt install libopencv-dev
```

**Step 4.** (Optional) Download converted onnx models by `mmdeploy-converted-models`.
```bash
apt install git-lfs
git clone https://github.com/liu-mengyang/mmdeploy-converted-models --depth=1
```


## Quickstart

Please read the previous section to make sure the required packages have been installed before using this crate.

Update your *Cargo.toml*

```toml
mmdeploy = "0.9.0"
```

## APIs for MM Codebases

Good news: Now, you can use Rust language to build your fantastic applications powered by MMDeploy!
Take a look by running some examples! In these examples, `CPU` is the default inference device. If you choose to deploy models on `GPU`, you will replace all `cpu` in test commands with `cuda`.

### Convert Models

You can 

* Directly use converted models [here](https://github.com/liu-mengyang/mmdeploy-converted-models) ^_^
* Or follow [MMDeploy documentation](https://mmdeploy.readthedocs.io/en/latest/get_started.html#convert-model) to install and convert appropriate models

### Classifier API

Deploy image classification models converted by MMDeploy.

The example deploys a ResNet model converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example classifier cpu ../mmdeploy-converted-models/resnet ./images/demos/mmcls_demo.jpg
```

### Detector API

Deploy object detection models converted by MMDeploy.

The example deploys a FasterRCNN model converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example detector cpu ../mmdeploy-converted-models/faster-rcnn-ort ./images/demos/mmdet_demo.jpg
```

A rendered result we can take a look located in the current directory and is named `output_detection.png`.

![](images/output_detection.png)

### Segmentor API

Deploy object segmentation models converted by MMDeploy.

The example deploys a DeepLabv3 model converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example segmentor cpu ../mmdeploy-converted-models/deeplabv3 ./images/demos/mmseg_demo.png
```

A rendered result we can take a look located in the current directory and is named `output_segmentation.png`.

![](images/output_segmentation.png)

### Pose detector API

Deploy pose detection models converted by MMDeploy.

The example deploys an HRNet model converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example pose_detector cpu ../mmdeploy-converted-models/hrnet ./images/demos/mmpose_demo.jpg
```

A rendered result we can take a look located in the current directory and is named `output_pose.png`.

![](images/output_pose.png)

### Rotated detector API

Deploy rotated detection models converted by MMDeploy.

The example deploys a RetinaNet model converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example rotated_detector cpu ../mmdeploy-converted-models/retinanet ./images/demos/mmrotate_demo.jpg
```

A rendered result we can take a look located in the current directory and is named `output_rotated_detection.png`.

![](images/output_rotated_detection.png)

### OCR API

Deploy text detection and text recognition models converted by MMDeploy.

The example deploys a DBNet model for detection and a CRNN model for recognition both converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example ocr cpu ../mmdeploy-converted-models/dbnet ../mmdeploy-converted-models/crnn ./images/demos/mmocr_demo.jpg
```

A rendered result we can take a look located in the current directory and is named `output_ocr.png`.

![](images/output_ocr.png)

### Restorer API

Deploy restorer models converted by MMDeploy.

The example deploys an EDSR model for restoration converted by the ONNXRUNTIME target on a CPU device.

```bash
cargo run --example restorer cpu ../mmdeploy-converted-models/edsr ./images/demos/mmediting_demo.png
```

A rendered result we can take a look located in the current directory and is named `output_restorer.png`.

![](images/output_restorer.png)

### TOSupport List

- [x] Classifier
- [x] Detector
- [x] Segmentor
- [x] Pose Detector
- [x] Rotated Detector
- [x] Text Detector
- [x] Text Recognizer
- [x] Restorer

### TODO List

- [x] PR for contributing a rust-mmdeploy-CI into MMDeploy
- [x] Test with TensorRT prebuilt package
- [ ] Documentation of rust-mmdeploy and rust-mmdeploy-sys
- [ ] Tutorial of rust-mmdeploy