## Introduction

SYCL is a modern C++-based heterogeneous programming model that allows
the same application to target different types of processing devices,
including CPUs and GPUs.

In this case study, SYCL is configured on the 20 PF cluster with:

-   Custom LLVM/Clang compiler
-   SYCL support
-   Native CPU backend
-   NVIDIA CUDA backend
-   Unified Runtime
-   CUDA 13.1.1
-   MPICH for distributed-memory parallelism
-   SLURM for job scheduling

The complete software stack is installed at:

```bash
/home/apps/softwares/sycl-stack
```

The test programs are maintained under:

```bash
/home/apps/softwares/sycl-stack/test
```

**All the programs are also located in the Github repo.**

Github repo: [https://github.com/HPC-Stack/SYCL-case-study](https://github.com/HPC-Stack/SYCL-case-study)


<a id="org148b6b4"></a>

## SYCL Software Stack

The installed stack contains the following major components:

```
    SYCL Stack
    |
    +-- LLVM / Clang
    |   +-- clang
    |   +-- clang++
    |   +-- SYCL compiler
    |
    +-- Unified Runtime
    |   +-- CPU adapter
    |   +-- CUDA adapter |
    +-- CUDA 13.1.1
    |
    +-- MPICH
    |   +-- mpicc
    |   +-- mpicxx
    |   +-- mpirun
    |   +-- mpiexec
    |
    +-- SLURM
        +-- Job allocation
        +-- CPU execution
        +-- GPU execution
        +-- Multi-node execution
```

<a id="org699cc51"></a>

## Installation Location

The complete installation is available at:

```bash
ls -l /home/apps/softwares/sycl-stack
```

```
total 36
drwxr-xr-x 4 cdacapp01 cdacapp01 4096 Sep 29 15:06 build
drwxr-xr-x 2 cdacapp01 cdacapp01 4096 Sep 29 15:09 cuda
drwxr-xr-x 2 cdacapp01 cdacapp01 4096 Sep 29 15:09 env
drwxr-xr-x 4 cdacapp01 cdacapp01 4096 Sep 29 15:08 install
-rwxr-xr-x 1 cdacapp01 cdacapp01 7655 Sep 29 15:10 setup-env.sh
drwxr-xr-x 4 cdacapp01 cdacapp01 4096 Sep 29 15:01 src
drwxr-xr-x 4 cdacapp01 cdacapp01 4096 Sep 30 10:01 test
drwxr-xr-x 3 cdacapp01 cdacapp01 4096 Sep 29 15:09 tools
```

The important directories are:

```
/home/apps/softwares/sycl-stack/
|
+-- install/
|   |
|   +-- llvm/
|   |
|   +-- mpich/
|
+-- test/
|
+-- setup-env.sh
```

The custom SYCL compiler is located under:

```bash
/home/apps/softwares/sycl-stack/install/llvm/bin
```

The MPICH installation is located under:

```bash
/home/apps/softwares/sycl-stack/install/mpich
```


<a id="org3c66a9c"></a>

## Loading the SYCL Environment

There are two ways to enable the environment.


<a id="org6e29cee"></a>

### Using the Module

The recommended method on the cluster is to use the module.

First check the available SYCL module:

```bash
module avail sycl
```

```
--------------------------- /home/apps/modulefiles ----------------------------
    sycl/cpu_gpu

If the avail list is too long consider trying:

"module --default avail" or "ml -d av" to just list the default modules.
"module overview" or "ml ov" to display the number of modules for each name.

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

### Load the SYCL environment:

```bash
module load sycl/cpu_gpu
```

```
============================================================
        Custom SYCL + CUDA + MPI Environment Loaded
============================================================
Installation:
    SYCL_STACK  = /home/apps/softwares/sycl-stack
    SYCL_ROOT   = /home/apps/softwares/sycl-stack/install/llvm
    MPI_ROOT    = /home/apps/softwares/sycl-stack/install/mpich
    CUDA_HOME   = /home/apps/spack/opt/spack/linux-cascadelake/cuda-13.1.1-fqqyj4heehd2l44w4i6kspr7dbnmdvgi
Compilers:
    clang       = /home/apps/softwares/sycl-stack/install/llvm/bin/clang
    clang++     = /home/apps/softwares/sycl-stack/install/llvm/bin/clang++
    mpicc       = /home/apps/softwares/sycl-stack/install/mpich/bin/mpicc
    mpicxx      = /home/apps/softwares/sycl-stack/install/mpich/bin/mpicxx
CUDA:
    nvcc        = /home/apps/spack/opt/spack/linux-cascadelake/cuda-13.1.1-fqqyj4heehd2l44w4i6kspr7dbnmdvgi/bin/nvcc
MPI:
    mpirun      = /home/apps/softwares/sycl-stack/install/mpich/bin/mpirun
    mpiexec     = /home/apps/softwares/sycl-stack/install/mpich/bin/mpiexec
SYCL:
    sycl-ls     = /home/apps/softwares/sycl-stack/install/llvm/bin/sycl-ls
    Target      = nvptx64-nvidia-cuda
Manual environment:
    source /home/apps/softwares/sycl-stack/setup-env.sh
============================================================
    Environment Ready
============================================================
```

After loading the module, check the compilers:

```bash
which clang
which clang++
which mpicc
which mpicxx
which mpirun
which nvcc
which sycl-ls
```

The expected paths should point to:

```bash
/home/apps/softwares/sycl-stack/install/llvm/bin/
/home/apps/softwares/sycl-stack/install/mpich/bin/
```


<a id="org6a81c11"></a>

### Loading the Environment Manually

The complete environment can also be loaded directly using:

```bash
source /home/apps/softwares/sycl-stack/setup-env.sh
```
    
```
============================================================
        Custom SYCL + CUDA + MPI Environment Loaded
============================================================

Installation:
    SYCL_STACK      = /home/apps/softwares/sycl-stack
    SYCL_ROOT       = /home/apps/softwares/sycl-stack/install/llvm
    MPI_ROOT        = /home/apps/softwares/sycl-stack/install/mpich
    CUDA_HOME       = /home/apps/spack/opt/spack/linux-cascadelake/cuda-13.1.1-fqqyj4heehd2l44w4i6kspr7dbnmdvgi

SYCL Compiler:
    clang           = /home/apps/softwares/sycl-stack/install/llvm/bin/clang
    clang++         = /home/apps/softwares/sycl-stack/install/llvm/bin/clang++
    sycl-ls         = /home/apps/softwares/sycl-stack/install/llvm/bin/sycl-ls

CUDA:
    nvcc            = /home/apps/spack/opt/spack/linux-cascadelake/cuda-13.1.1-fqqyj4heehd2l44w4i6kspr7dbnmdvgi/bin/nvcc

MPI:
    mpicc           = /home/apps/softwares/sycl-stack/install/mpich/bin/mpicc
    mpicxx          = /home/apps/softwares/sycl-stack/install/mpich/bin/mpicxx
    mpiexec         = /home/apps/softwares/sycl-stack/install/mpich/bin/mpiexec
    mpirun          = /home/apps/softwares/sycl-stack/install/mpich/bin/mpirun

MPI Version:
MPICH Version:      4.3.2
MPICH Release date: Mon Oct  6 11:14:20 AM CDT 2025
MPICH ABI:          17:2:5
MPICH Device:       ch4:ofi
MPICH configure:    --prefix=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/mpich CC=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang CXX=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang++ --with-device=ch4:ofi --enable-fortran=no
MPICH CC:           /scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang     -O2
MPICH CXX:          /scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang++   -O2
MPICH F77:            
MPICH FC:             
MPICH features:     threadcomm

SYCL Runtime:
    CUDA adapter    = /home/apps/softwares/sycl-stack/install/llvm/lib/libur_adapter_cuda.so.0.12.0
    CPU adapter     = /home/apps/softwares/sycl-stack/install/llvm/lib/libur_adapter_native_cpu.so.0.12.0

MPI Include:
    /home/apps/softwares/sycl-stack/install/mpich/include

MPI Library:
    /home/apps/softwares/sycl-stack/install/mpich/lib

============================================================
    Environment Ready
============================================================
```
    

This sets the required:

-   LLVM paths
-   SYCL paths
-   CUDA paths
-   MPI paths
-   Runtime library paths
-   Include paths
-   Compiler variables


<a id="org0f6909b"></a>

## Verify the SYCL Compiler

After loading the environment:

```bash
clang++ --version
```

```
DPC++ compiler 7.2.0 (pre-release) build based on:
clang version 24.0.0git (https://github.com/intel/llvm.git 17c6a87c9a5dcd3a85187ded445a695bdf1faf1a)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /home/apps/softwares/sycl-stack/install/llvm/bin
Build config: +assertions
```

Check the SYCL compiler:

```bash
clang++ -fsycl --version
```

```
DPC++ compiler 7.2.0 (pre-release) build based on:
clang version 24.0.0git (https://github.com/intel/llvm.git 17c6a87c9a5dcd3a85187ded445a695bdf1faf1a)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /home/apps/softwares/sycl-stack/install/llvm/bin
Build config: +assertions
```

Check the MPI compiler:

```bash
mpicxx --version
```

```
DPC++ compiler 7.2.0 (pre-release) build based on:
clang version 24.0.0git (https://github.com/intel/llvm.git 17c6a87c9a5dcd3a85187ded445a695bdf1faf1a)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin
Build config: +assertions
```

Check MPICH:

```bash
mpichversion
```

```
MPICH Version:      4.3.2
MPICH Release date: Mon Oct  6 11:14:20 AM CDT 2025
MPICH ABI:          17:2:5
MPICH Device:       ch4:ofi
MPICH configure:    --prefix=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/mpich CC=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang CXX=/scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang++ --with-device=ch4:ofi --enable-fortran=no
MPICH CC:           /scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang     -O2
MPICH CXX:          /scratch/nsmapplication/cdacapp01/rAbhishek/apps/sycl-stack/install/llvm/bin/clang++   -O2
MPICH F77:            
MPICH FC:             
MPICH features:     threadcomm
```


<a id="org9406544"></a>

## Discover Available SYCL Devices

SYCL provides the `sycl-ls` utility for discovering available devices.

Run:

```bash
sycl-ls
```

```
[native_cpu:cpu][native_cpu:0] SYCL_NATIVE_CPU, SYCL Native CPU 0.1 [0.0.0]
```

Depending on the node, devices may include:

```
    CPU
    GPU
    NVIDIA CUDA device
```

For example, the environment may report a CPU device and an NVIDIA
CUDA device.

This command is particularly useful before running a SYCL application
because it verifies that the SYCL runtime can discover the required
backend.


<a id="org1e87d26"></a>

## Basic SYCL Program

Create a file:

```bash
cd /home/apps/softwares/sycl-stack/test

vim hello.cpp
```

Example SYCL program:

```cpp
#include <sycl/sycl.hpp>
#include <iostream>

int main() {
    sycl::queue q;

    std::cout << "Running on: "
                << q.get_device().get_info<sycl::info::device::name>()
                << "\n";

    q.single_task([]() {
        // SYCL kernel
    }).wait();

    std::cout << "Hello World from SYCL!\n";

    return 0;
}
```


<a id="org98d9441"></a>

## Compile a SYCL Program

The basic SYCL compilation command is:

```bash
clang++ -fsycl -fsycl-targets=native_cpu hello.cpp -o hello
```

Run:

```bash
./hello
```

```
    Running on: SYCL Native CPU
    Hello World from SYCL!
```

The default SYCL device selection depends on the available runtime
and selector used by the application.


<a id="orgd8628d4"></a>

## CPU SYCL Compilation

For explicitly targeting the native CPU backend:

```bash
clang++ \
    -fsycl \
    -fsycl-targets=native_cpu \
    hello.cpp \
    -o hello_cpu
```

Run:

```bash
./hello_cpu
```

The executable will use the SYCL native CPU backend.


<a id="org7b3f9d9"></a>

## Explicit CPU Device Selection

A program can explicitly select the CPU device.

Example:

```cpp
#include <sycl/sycl.hpp>
#include <iostream>

int main()
{
    sycl::queue q(sycl::cpu_selector_v);

    auto device = q.get_device();

    std::cout << "CPU Device: "
                << device.get_info<
                        sycl::info::device::name>()
                << std::endl;

    q.parallel_for(
        sycl::range<1>(100),
        [=](sycl::id<1> i) {
            // CPU work
        }
    ).wait();

    return 0;
}
```

Compile:

```bash
clang++ \
    -fsycl \
    -fsycl-targets=native_cpu \
    hello_cpu.cpp \
    -o hello_cpu
```

Run:

```bash
./hello_cpu
```

```bash
CPU Device: SYCL Native CPU
```


<a id="orgc539522"></a>

## NVIDIA GPU SYCL Compilation

The application can explicitly select the GPU using:

```cpp
sycl::queue q(sycl::gpu_selector_v);
```

For NVIDIA GPUs, use the CUDA SYCL target:

```bash
clang++ \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    hello.cpp \
    -o hello_gpu
```

Run:

```bash
./hello_gpu
```


<a id="orge30e674"></a>

## GPU Device Information

A GPU SYCL program can print information about the selected device.

```cpp
#include <sycl/sycl.hpp>
#include <iostream>

int main()
{
    sycl::queue q(sycl::gpu_selector_v);

    auto device = q.get_device();

    std::cout << "Device : "
                << device.get_info<
                        sycl::info::device::name>()
                << std::endl;

    std::cout << "Vendor : "
                << device.get_info<
                        sycl::info::device::vendor>()
                << std::endl;

    std::cout << "Driver : "
                << device.get_info<
                        sycl::info::device::driver_version>()
                << std::endl;

    return 0;
}
```

Compile:

```bash
clang++ \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    device_info_gpu.cpp \
    -o device_info_gpu
```


<a id="org0269808"></a>

## SLURM Job Submission

The cluster uses SLURM for resource allocation.

A typical workflow is:

```
    Login node
        |
        | sbatch
        v
    SLURM
        |
        +-- Node 1
        |
        +-- Node 2
        |
        +-- Node 3
        |
        +-- Node 4
```


<a id="orgdc091d0"></a>

## Single-Node GPU SYCL Job

Example SLURM script:

```bash
#!/bin/bash
#SBATCH --job-name=sycl-gpu
#SBATCH --partition=gpu-debug
#SBATCH --gres=gpu:1
#SBATCH --nodes=1
#SBATCH --ntasks=1
##SBATCH --cpus-per-task=8
#SBATCH --time=00:30:00
#SBATCH --output=sycl-gpu-%j.out
#SBATCH --error=sycl-gpu-%j.err

module load syc/cpu_gpu

echo "========================================"
echo "SYCL GPU JOB"
echo "========================================"

hostname
nvidia-smi

echo
echo "SLURM_JOB_ID      = $SLURM_JOB_ID"
echo "SLURM_CPUS_PER_TASK = $SLURM_CPUS_PER_TASK"

echo
echo "SYCL Devices:"
sycl-ls

echo
echo "Running GPU executable..."

./$1
```

Submit:

```bash
sbatch gpu.sh device_info_gpu
```

```bash
Submitted batch job 85293
```


<a id="org0880228"></a>

## Slurm scripts


<a id="orgf5e76b6"></a>

### cpu.sh

```bash
#!/bin/bash
#SBATCH --job-name=sycl-cpu
#SBATCH --partition=debug
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=48
#SBATCH --time=00:30:00
#SBATCH --mem=170G
#SBATCH --output=sycl-cpu-%j.out
#SBATCH --error=sycl-cpu-%j.err

source /home/apps/softwares/sycl-stack/setup-env.sh

echo "========================================"
echo "SYCL CPU JOB"
echo "========================================"

hostname

echo
echo "SLURM_JOB_ID      = $SLURM_JOB_ID"
echo "SLURM_CPUS_PER_TASK = $SLURM_CPUS_PER_TASK"

echo
echo "SYCL Devices:"
sycl-ls

echo
echo "Running CPU executable..."

./$1
```

Submit:

```bash
sbatch gpu.sh device_info_gpu
```

```bash
Submitted batch job 85293
```


<a id="org9710f67"></a>

### gpu.sh

```bash
#!/bin/bash
#SBATCH --job-name=sycl-gpu
#SBATCH --partition=gpu-debug
#SBATCH --gres=gpu:1
#SBATCH --nodes=1
#SBATCH --ntasks=1
##SBATCH --cpus-per-task=8
#SBATCH --time=00:30:00
#SBATCH --output=sycl-gpu-%j.out
#SBATCH --error=sycl-gpu-%j.err

module load syc/cpu_gpu

echo "========================================"
echo "SYCL GPU JOB"
echo "========================================"

hostname
nvidia-smi

echo
echo "SLURM_JOB_ID      = $SLURM_JOB_ID"
echo "SLURM_CPUS_PER_TASK = $SLURM_CPUS_PER_TASK"

echo
echo "SYCL Devices:"
sycl-ls

echo
echo "Running GPU executable..."

./$1
```

Submit:

```bash
sbatch gpu.sh device_info_gpu
```

```bash
Submitted batch job 85293
```


<a id="org70b8341"></a>

### multinode.sh

```bash
#!/bin/bash

#SBATCH --job-name=sycl-mpi-multi
#SBATCH --partition=debug
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=4
#SBATCH --cpus-per-task=8
#SBATCH --time=00:30:00
#SBATCH --mem=170G
#SBATCH --output=sycl-mpi-multi-%j.out
#SBATCH --error=sycl-mpi-multi-%j.err


# ============================================================
# Load custom SYCL + CUDA + MPICH environment
# ============================================================
module load sycl/cpu_gpu


mpirun \
    -np "$SLURM_NTASKS" \
    ./"$1"
```

Submit:

```bash
sbatch gpu.sh device_info_gpu
```

```bash
Submitted batch job 85293
```


<a id="org0611fe1"></a>

## Example: Vector Addition

A simple SYCL vector addition program is useful for testing the
environment.

```cpp
#include <sycl/sycl.hpp>
#include <iostream>
#include <vector>

int main()
{
    const size_t N = 1000000;

    std::vector<float> A(N, 1.0f);
    std::vector<float> B(N, 2.0f);
    std::vector<float> C(N, 0.0f);

    sycl::queue q;

    float *d_A = sycl::malloc_device<float>(N, q);
    float *d_B = sycl::malloc_device<float>(N, q);
    float *d_C = sycl::malloc_device<float>(N, q);

    q.memcpy(d_A, A.data(), N * sizeof(float));
    q.memcpy(d_B, B.data(), N * sizeof(float));

    q.wait();

    q.parallel_for(
        sycl::range<1>(N),
        [=](sycl::id<1> i) {
            d_C[i] = d_A[i] + d_B[i];
        }
    ).wait();

    q.memcpy(C.data(), d_C, N * sizeof(float)).wait();

    std::cout << "C[0] = "
                << C[0]
                << std::endl;

    sycl::free(d_A, q);
    sycl::free(d_B, q);
    sycl::free(d_C, q);

    return 0;
}
```


<a id="org7cdc2c5"></a>

## Compile Vector Addition for CPU

```bash
clang++ \
    -fsycl \
    -fsycl-targets=native_cpu \
    vector_add.cpp \
    -o vector_add_cpu
```

Run:

```bash
sbatch cpu.sh vector_add_cpu
```

```bash
Submitted batch job 85300
```


<a id="orgddd182d"></a>

## Compile Vector Addition for GPU

```bash
clang++ \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    vector_add.cpp \
    -o vector_add_gpu
```

Run:

```bash
sbatch gpu.sh vector_add_gpu
```

```bash
Submitted batch job 85313
```


<a id="orge3ca0ff"></a>

## SYCL and MPI

SYCL provides parallelism inside a node.

MPI provides distributed-memory communication between processes and
nodes.

Therefore, a multi-node application can use:

```
                MPI
                 |
       +---------+---------+
       |                   |
     Node 1              Node 2
       |                   |
     SYCL                 SYCL
       |                   |
    CPU/GPU             CPU/GPU
```

MPI handles communication between nodes while SYCL performs
computation on the CPU or GPU available to each MPI process.


<a id="orgb3b0dae"></a>

## MPI + SYCL Compilation

The custom MPICH installation provides:

```bash
mpicc
mpicxx
mpirun
mpiexec
```

For a C++ SYCL + MPI program:

```bash
mpicxx \
    -fsycl \
    program.cpp \
    -o program
```

For an NVIDIA GPU target:

```bash
mpicxx \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    program.cpp \
    -o program
```


<a id="orgcc83607"></a>

## Simple MPI + SYCL Example

The following example calculates a local value using SYCL and then
uses MPI to combine the results from all MPI ranks.

```cpp
#include <sycl/sycl.hpp>
#include <mpi.h>

#include <iostream>
#include <vector>

int main(int argc, char **argv)
{
    MPI_Init(&argc, &argv);

    int rank;
    int size;

    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    const int N = 1000000;

    sycl::queue q(sycl::cpu_selector_v);

    std::vector<int> data(N, 1);

    int *device_data =
        sycl::malloc_device<int>(N, q);

    q.memcpy(
        device_data,
        data.data(),
        N * sizeof(int)
    ).wait();

    int local_sum = 0;

    sycl::buffer<int> result_buf(
        &local_sum,
        sycl::range<1>(1)
    );

    q.submit([&](sycl::handler &h) {

        auto result =
            result_buf.get_access<
                sycl::access::mode::write>(h);

        h.parallel_for(
            sycl::range<1>(1),
            [=](sycl::id<1>) {

                int sum = 0;

                for (int i = 0; i < N; ++i)
                    sum += device_data[i];

                result[0] = sum;
            }
        );

    }).wait();

    int global_sum = 0;

    MPI_Reduce(
        &local_sum,
        &global_sum,
        1,
        MPI_INT,
        MPI_SUM,
        0,
        MPI_COMM_WORLD
    );

    if (rank == 0) {

        std::cout
            << "MPI ranks : "
            << size
            << std::endl;

        std::cout
            << "Global sum : "
            << global_sum
            << std::endl;
    }

    sycl::free(device_data, q);

    MPI_Finalize();

    return 0;
}
```


<a id="orgcb2d92d"></a>

## Compile MPI + SYCL Program

CPU version:

```bash
mpicxx \
    -fsycl \
    -fsycl-targets=native_cpu \
    mpi_sycl.cpp \
    -o mpi_sycl_cpu
```

GPU version:

```bash
mpicxx \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    mpi_sycl.cpp \
    -o mpi_sycl_gpu
```


<a id="orgf9e8793"></a>

## Running MPI Programs Interactively

For a small test:

```bash
mpirun -np 4 ./mpi_sycl_cpu
```

This starts four MPI ranks.

For multi-node execution, the resources should be obtained from
SLURM before launching MPI.


<a id="orgc981a94"></a>

## Multi-Node MPI + SYCL Job

For multi-node execution using the installed MPICH environment:

```bash
#!/bin/bash

#SBATCH --job-name=sycl-mpi-multi
#SBATCH --partition=debug
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=4
#SBATCH --cpus-per-task=8
#SBATCH --time=00:30:00
#SBATCH --mem=170G
#SBATCH --output=sycl-mpi-multi-%j.out
#SBATCH --error=sycl-mpi-multi-%j.err


# ============================================================
# Load custom SYCL + CUDA + MPICH environment
# ============================================================
module load sycl/cpu_gpu


mpirun \
    -np "$SLURM_NTASKS" \
    ./"$1"
```

Submit:

```bash
sbatch multinode.sh mpi_sycl_cpu
```

```bash
Submitted batch job 85325
```


<a id="orgb39b4ba"></a>

## Multi-Node GPU + MPI + SYCL

The same hybrid programming model can be extended to multiple GPU
nodes.

Conceptually:

```
                 MPI
                  |
       +----------+----------+
       |                     |
     Node 1                Node 2
       |                     |
    MPI Rank 0             MPI Rank 1
       |                     |
     SYCL GPU              SYCL GPU
       |                     |
    NVIDIA GPU             NVIDIA GPU
```

Compile:

```bash
mpicxx \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    mpi_sycl.cpp \
    -o mpi_sycl_gpu
```

A corresponding SLURM job can request multiple nodes and one GPU per
MPI process, depending on the GPU resource configuration of the
cluster.


<a id="org0cfe4e1"></a>

## Checking the Environment Inside a Job

Always verify the environment inside the allocated job.

```bash
echo "$SYCL_STACK"
echo "$SYCL_ROOT"
echo "$MPI_ROOT"
echo "$CUDA_HOME"
```

Check executables:

```bash
which clang++
which sycl-ls
which mpicxx
which mpirun
which nvcc
```

Check devices:

```bash
sycl-ls
```


<a id="org8886ae9"></a>

## Example Workflow: CPU

Complete CPU workflow:

```bash
module load sycl/cpu_gpu
```
    
```bash
cd /home/apps/softwares/sycl-stack/test
```
    
```bash
clang++ \
    -fsycl \
    -fsycl-targets=native_cpu \
    program.cpp \
    -o program_cpu
```
    
```bash
sbatch cpu.sh program_cpu
```


<a id="org56121af"></a>

## Example Workflow: NVIDIA GPU

Complete GPU workflow:

```bash
module load sycl/cpu_gpu
```
    
```bash
cd /home/apps/softwares/sycl-stack/test
```
    
```bash
clang++ \
    -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    program.cpp \
    -o program_gpu
```
    
```bash
sbatch gpu.sh program_gpu
```


<a id="org944c43c"></a>

## Example Workflow: Multi-Node MPI + SYCL

Compile:

```bash
module load sycl/cpu_gpu
```
    
```bash
cd /home/apps/softwares/sycl-stack/test
```
    
```bash
mpicxx \
    -fsycl \
    -fsycl-targets=native_cpu \
    mpi_sycl.cpp \
    -o mpi_sycl_cpu
```

Submit:

```bash
sbatch multinode.sh mpi_sycl_cpu
```


<a id="org488d4cc"></a>

## Matrix Multiplication Multinode CPU Test
Code available here : [https://github.com/HPC-Stack/SYCL-case-study/blob/main/matmul_mpi_sycl_cpu.cpp](https://github.com/HPC-Stack/SYCL-case-study/blob/main/matmul_mpi_sycl_cpu.cpp)

### Compile

```bash
mpicxx -fsycl -fsycl-targets=native_cpu matmul_mpi_sycl_cpu.cpp -o matmul_mpi_sycl_cpu
```

### Submit

```bash
sbatch multinode.sh matmul_mpi_sycl_cpu
```
    
```
Submitted batch job 85345
```

### Output

```
SLURM_CLUSTER_NAME = rudra20pf-hpc
SLURM_ARRAY_JOB_ID =
SLURM_ARRAY_TASK_ID =
SLURM_ARRAY_TASK_COUNT =
SLURM_ARRAY_TASK_MAX =
SLURM_ARRAY_TASK_MIN =
SLURM_JOB_ACCOUNT = cdac
SLURM_JOB_ID = 85345
SLURM_JOB_NAME = sycl-mpi-multi
SLURM_JOB_NODELIST = cbcn[0102-0105]
SLURM_JOB_USER = cdacapp01
SLURM_JOB_UID = 80014
SLURM_JOB_PARTITION = debug
SLURM_TASK_PID = 563015
SLURM_SUBMIT_DIR = /home/apps/softwares/sycl-stack/test
SLURM_CPUS_ON_NODE = 32
SLURM_NTASKS = 16
SLURM_TASK_PID = 563015
==========================================
Rank 12 -> Rank 13 -> Rank 14 -> Rank 15 -> SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
Rank 9 -> Rank 10 -> Rank 8 -> Rank 11 -> SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
============================================
        MPI + SYCL MULTI-NODE CPU
        MATRIX MULTIPLICATION
============================================

MPI processes : 16
Matrix size   : 20000 x 20000
SYCL backend  : CPU

Rank Rank 1 -> Rank 2 -> Rank 3 -> 0 -> SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
Rank 4 -> Rank 5 -> Rank 6 -> Rank 7 -> SYCL Native CPU
SYCL Native CPU
SYCL Native CPU
SYCL Native CPU

============================================
                    RESULTS
============================================
MPI processes : 16
Matrix size   : 20000 x 20000
Execution time: 55.52 seconds
Performance   : 288.19 GFLOPS
Verification  : PASSED
============================================
```


<a id="org2d85eeb"></a>

## Matrix Multiplication Multinode GPU Test
Code available here : [https://github.com/HPC-Stack/SYCL-case-study/blob/main/matmul_mpi_sycl_gpu.cpp](https://github.com/HPC-Stack/SYCL-case-study/blob/main/matmul_mpi_sycl_gpu.cpp)

### Compile

```bash
mpicxx -fsycl -fsycl-targets=nvptx64-nvidia-cuda -fsycl-id-queries-range=size_t matmul_mpi_sycl_gpu.cpp -o matmul_mpi_sycl_gpu
```

### Submit

```bash
#!/bin/bash

#SBATCH --job-name=sycl-mpi-multi
#SBATCH --partition=gpu-debug
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=2
#SBATCH --gres=gpu:2
#SBATCH --cpus-per-task=8
#SBATCH --time=00:30:00
#SBATCH --mem=170G
#SBATCH --output=sycl-mpi-multi-%j.out
#SBATCH --error=sycl-mpi-multi-%j.err


# ============================================================
# Load custom SYCL + CUDA + MPICH environment
# ============================================================
module load sycl/cpu_gpu


mpirun \
    -np "$SLURM_NTASKS" \
    ./"$1" $2

```

```bash
sbatch multinode_gpu.sh matmul_mpi_sycl_gpu 70000
```

```
Submitted batch job 85371
```

### Output

```
==========================================
SLURM_CLUSTER_NAME = rudra20pf-hpc
SLURM_ARRAY_JOB_ID =
SLURM_ARRAY_TASK_ID =
SLURM_ARRAY_TASK_COUNT =
SLURM_ARRAY_TASK_MAX =
SLURM_ARRAY_TASK_MIN =
SLURM_JOB_ACCOUNT = cdac
SLURM_JOB_ID = 85371
SLURM_JOB_NAME = sycl-mpi-multi
SLURM_JOB_NODELIST = cbgpu[0021-0022]
SLURM_JOB_USER = cdacapp01
SLURM_JOB_UID = 80014
SLURM_JOB_PARTITION = gpu-debug
SLURM_TASK_PID = 3736434
SLURM_SUBMIT_DIR = /home/apps/softwares/sycl-stack/test
SLURM_CPUS_ON_NODE = 16
SLURM_NTASKS = 4
SLURM_TASK_PID = 3736434
==========================================
============================================
        MPI + SYCL MULTI-NODE GPU
        MATRIX MULTIPLICATION
============================================

MPI processes : 4
Matrix size   : 70000 x 70000
MPI ranks/node: 2
GPUs/rank     : 1

Rank 0 | Local rank 0 | GPU 0 | Rank 1 | Local rank 1 | GPU 1 | NVIDIA A100 80GB PCIe
NVIDIA A100 80GB PCIe
Rank 2 | Local rank 0 | GPU 0 | NVIDIA A100 80GB PCIe
Rank 3 | Local rank 1 | GPU 1 | NVIDIA A100 80GB PCIe

============================================
                    RESULTS
============================================
MPI processes : 4
MPI ranks/node: 2
Total GPUs    : 4
Matrix size   : 70000 x 70000
Execution time: 47.53 seconds
Performance   : 14434.33 GFLOPS
Verification  : PASSED
============================================
```


<a id="orgb9eaa89"></a>

## Summary

The 20 PF cluster provides a complete SYCL programming environment
supporting CPU, NVIDIA GPU, MPI, and SLURM.

The environment can be loaded using:

```bash
module load sycl/cpu_gpu
```

or manually:

```bash
source /home/apps/softwares/sycl-stack/setup-env.sh
```

SYCL CPU compilation:

```bash
clang++ -fsycl \
    -fsycl-targets=native_cpu \
    program.cpp \
    -o program_cpu
```

SYCL NVIDIA GPU compilation:

```bash
clang++ -fsycl \
    -fsycl-targets=nvptx64-nvidia-cuda \
    program.cpp \
    -o program_gpu
```

MPI + SYCL compilation:

```bash
mpicxx -fsycl \
    -fsycl-targets=native_cpu \
    program.cpp \
    -o program
```

Multi-node applications are submitted through SLURM and launched
using the installed MPICH runtime.

This provides a complete workflow for developing, compiling,
testing, and executing heterogeneous SYCL applications on the
20 PF supercomputing infrastructure.

