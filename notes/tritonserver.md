# Building Triton Inference Server on Azure Linux

## Understanding CUDA and GPU Architecture

CUDA (Compute Unified Device Architecture) is NVIDIA’s platform for
general-purpose parallel computing on GPUs. It allows developers to write code
in C, C++, or Python to fully leverage the parallel execution capabilities of
NVIDIA hardware.

### CUDA Cores

CUDA cores are the basic computational units within an NVIDIA GPU, analogous to
CPU cores but optimized for massively parallel workloads. Each core is capable
of simple arithmetic operations like addition and multiplication. A high-end
GPU, such as the RTX 4090, contains up to **16,384 CUDA cores**. These cores
operate under a **SIMD (Single Instruction, Multiple Data)** model, executing
the same instruction across multiple data points simultaneously — ideal for
workloads like graphics rendering, simulations, and deep learning inference.

### Tensor Cores

Modern NVIDIA GPUs also include **Tensor Cores**, which are specialized for
performing matrix operations at high throughput. These are particularly
important for deep learning tasks, such as those served by inference engines
like Triton.

### Measuring Performance

GPU performance is commonly measured in **TFLOPS** (Tera Floating Point
Operations Per Second), reflecting its ability to perform floating-point
calculations. CUDA cores contribute to the general-purpose TFLOPS, while Tensor
Cores significantly boost matrix operation throughput (e.g., in FP16 or INT8).

### CUDA Toolkit

To build GPU-accelerated applications, the CUDA Toolkit provides essential
components:

- **NVCC** (CUDA compiler)
- **CUDA libraries** (e.g., cuBLAS, cuDNN)
- **Debugging and profiling tools**

The toolkit must be installed and properly configured in the build environment
when compiling software like `tritonserver` with GPU support.

## Understanding the Triton Build System and Base Image

To prepare for building `tritonserver` on Azure Linux, it’s useful to first
understand how Triton is built in a standard environment such as Ubuntu 22.04.
The Triton Inference Server source repository includes a build automation script
named `build.py` that orchestrates the build process using containerized
environments.

### `build.py` Overview

The `build.py` script is the main entry point for building Triton. Running it
from within the `server/` directory triggers a series of steps that prepare and
build the Triton binaries. When executed with the `--dryrun` flag, the script
will stop after generating several intermediate files, allowing inspection and
customization. These generated files are:

- `docker_build.sh`
- `cmake_build.sh`
- `Dockerfile`

These files appear in the `server/build/` subdirectory and represent the staged
process to build `tritonserver` inside a Docker container.

### Docker-Based Build Process

The build follows two main stages:

1. **Creating the Build Environment (`tritonserver_buildbase`)**:
   - The `docker_build.sh` script builds a Docker image named `tritonserver_buildbase`.
   - This image is based on a prebuilt NVIDIA container (e.g.,
     `<xx.yy>-py3-min`) pulled from the NVIDIA NGC container registry.
   - The base image is a black box that contains CUDA, cuDNN, TensorRT, and
     other Triton-specific dependencies.

2. **CMake Build in Container**:
   - Once the base image is built, `cmake_build.sh` is executed inside the
     `tritonserver_buildbase` container.
   - This script uses CMake to compile Triton’s core components:
     - The shared library: `libtritonserver.so`
     - The server binary: `tritonserver`

These binaries are built inside the containerized environment and are later
extracted or used for image construction.

### Identifying Dependencies

By reviewing the Dockerfile used to build the `tritonserver_buildbase` image,
it's possible to extract a list of runtime and build-time dependencies required
for the Triton server, including:

- CUDA toolkit and driver
- cuBLAS
- cuDNN
- TensorRT
- Additional utility libraries and headers (e.g., protobuf, gRPC, Python headers)

This information is essential for replicating or adapting the build process in
environments such as Azure Linux, where package sources and base images differ
from Ubuntu.

## Adapting the Triton Build for Azure Linux

With the Triton build process understood from the Ubuntu workflow, the next step
is adapting that process to the Azure Linux environment. This stage introduces
unique constraints, particularly in terms of disk space and GPU configuration.

### Base Image Size Limitation

Azure Linux’s default core image typically has a disk size of around **4GB**,
which is significantly smaller than the **128GB** used during initial
Ubuntu-based builds. This limited space is insufficient for compiling
`tritonserver`, which requires many large dependencies, source code, and build
artifacts.

To resolve this, the **Image Customizer** tool (internally developed) can
be used to expand the image size before provisioning:

- Customize the image using Prism to increase the OS disk size to a more
  suitable value (e.g., 128GB).
- This prepares the base image for heavy build workloads while keeping the
  environment aligned with Azure Linux’s standards.

### GPU Support in Azure Linux

Compiling and testing `tritonserver` with GPU support requires a VM with access
to an NVIDIA GPU and a correctly configured CUDA environment. Configuring GPU
drivers and CUDA manually on Azure Linux is possible, but can be
**time-consuming and error-prone** due to driver compatibility and kernel
dependency issues.

To simplify the setup, an Azure VM with a pre-attached GPU can be provisioned using the Azure CLI:

```sh
az vm create \
  --subscription CoreOS_AzureLinux_SiliconPlatform \
  --resource-group <resource_group_name> \
  --name azlinux-test-vm \
  --image MicrosoftCBLMariner:azure-linux-3:azure-linux-3:latest \
  --os-disk-size-gb 128 \
  --size Standard_D16pds_v5 \
  --public-ip-sku Standard \
  --admin-username <user_name> \
  --admin-password <pwd> \
  --location westus2 \
  --tags AzSecPackAutoConfigReady=true
```

## Installing Tritonserver Build Dependencies on Azure Linux

To build `tritonserver` on Azure Linux, a comprehensive set of development tools
and libraries must be installed. This includes system packages, CUDA-related
toolkits, and deep learning libraries such as cuDNN and TensorRT.

### Base Development Packages

Install the following packages using `tdnf`:

```sh
sudo tdnf install -y \
  gcc gcc-c++ make kernel-headers kernel-devel \
  python3 python3-pip python3-devel \
  git binutils glibc-devel \
  c-ares-devel abseil-cpp-devel \
  cmake alib-devel openssl-devel libcurl-devel \
  protobuf protobuf-devel \
  libxml2 libxml2-devel \
  re2 re2-devel rapidjson-devel \
  boost-devel numactl-devel
```

## Installing CUDA and Related Libraries

### CUDA Driver

```sh
sudo tdnf install -y cuda
nvidia-smi
```

### CUDA Toolkit

```sh
wget https://developer.download.nvidia.com/compute/cuda/12.8.1/local_installers/cuda_12.8.1_570.124.06_linux.run
sudo sh cuda_12.8.1_570.124.06_linux.run
nvcc --version
```

### Installing cuDNN

```sh
tar -xvf cudnn-linux-x86_64-9.8.0.87_cuda12-archive.tar.xz
sudo cp cudnn-*-archive/include/cudnn*.h /usr/local/cuda/include
sudo cp -p cudnn-*-archive/lib/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

### Installing TensorRT

```sh
tar -xvf TensorRT-10.9.0.34.Linux.x86_64-gnu.cuda-12.zip

python3 -m pip install \
  tensorrt-10.9.0.34-cp312-none-linux_x86_64.whl \
  tensorrt_dispatch-10.9.0.34-cp312-none-linux_x86_64.whl \
  tensorrt_lean-10.9.0.34-cp312-none-linux_x86_64.whl
```

## Initiating the Tritonserver Build on Azure Linux

```sh
git clone --recursive https://github.com/triton-inference-server/server.git
cd server
```

```sh
./build.py -v \
  --no-container-build \
  --build-dir=./build \
  --enable-all
```
