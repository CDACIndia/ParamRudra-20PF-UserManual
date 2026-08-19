# System  Architecture and Configuration

The PARAM Rudra 20 PF HPC System is based on the Intel Xeon Gold 6240R with a total peak performance of 20 PFLOPS. The cluster consists of compute nodes connected with the InfiniBand NDR Low-Latency, High-Bandwidth InfiniBand interconnect network. The system uses the Lustre parallel file system.

- Total number of Nodes: 3074
    - Login Nodes: 14
    - Management Nodes: 24
    - Visualization Nodes : 2
    - CPU only Nodes: 2266
    - GPU Nodes: 320
    - High Memory CPU only Nodes: 320
    - Trinetra Nodes: 128

## Login Nodes 

Login nodes are typically used for administrative tasks such as editing files, writing scripts, transferring files, managing jobs, and related activities. Users are always connected to one of the login nodes upon logging in. From a login node, users can connect to a compute node to execute interactive jobs or submit batch jobs through the Slurm workload manager for execution on compute nodes. Since the PARAM Rudra login nodes serve as the entry point for all users, they are shared resources. By default, there are limits on the CPU time and memory that each user can utilize on a login node. If either of these limits is exceeded, the user's process or job will be terminated. 


| **Specification** | **Value** |
|-------------------|-----------|
| **Number of Login Nodes** | 14 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Frequency** | 2.4 GHz |
| **Cores per Login Node** | 48 |
| **Total CPU Cores** | 672 |
| **Memory per Login Node** | 192 GB |
| **Total Memory** | 2,688 GB |

## Service Nodes

PARAM Rudra is an aggregation of a large number of nodes connected through networks. Management nodes play a crucial role in managing and monitoring every component of the PARAM Rudra system. This includes monitoring the health, load, and utilization of individual components, as well as providing essential services such as security, management, and monitoring to ensure the system functions smoothly. 

### Management Nodes

| **Specification** | **Value** |
|-------------------|-----------|
| **Number of Management Nodes** | 24 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Frequency** | 2.4 GHz |
| **Cores per Management Node** | 48 |
| **Total CPU Cores** | 1,152 |
| **Memory per Management Node** | 192 GB |
| **Total Memory** | 4,608 GB |

### Visualization Nodes

| **Specification** | **Value** |
|-------------------|-----------|
| **Number of Visualization Nodes** | 2 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Frequency** | 2.4 GHz |
| **Cores per Visualization Node** | 48 |
| **Total CPU Cores** | 96 |
| **Memory per Visualization Node** | 192 GB |
| **Total Memory** | 384 GB |

## CPU Compute Nodes

CPU nodes are the individual machines dedicated to performing computational tasks. These nodes collectively form the computational power of the cluster. All the CPU intensive activities are carried on these nodes. Users can access these nodes from the login node to run interactive or batch jobs. 

| **Specification** | **Value** |
|-------------------|-----------|
| **Number of CPU Compute Nodes** | 2,266 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Frequency** | 2.4 GHz |
| **Cores per Compute Node** | 48 |
| **Total CPU Cores** | 108,768 |
| **Memory per Compute Node** | 192 GB DDR4 (2933 MHz) |
| **Total Memory** | 435,072 GB |
| **Local Storage** | 800 GB SSD per node |

## GPU Compute Nodes

GPU Compute Nodes feature accelerators cards that offer significant acceleration for parallel computing tasks using frameworks like CUDA and OpenCL. By harnessing the computational power of modern GPUs, these nodes are utilized for tasks such as scientific simulations, deep learning, and data analytics, providing high computational power and memory.

| **Specification** | **Value** |
|-------------------|-----------|
| **Number of GPU Compute Nodes** | 320 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Frequency** | 2.4 GHz |
| **CPU Cores per Compute Node** | 48 |
| **Total CPU Cores** | 15,360 |
| **Memory per Compute Node** | 192 GB DDR4 (2933 MHz) |
| **Total Memory** | 61,440 GB |
| **Local Storage** | 800 GB SSD per node |
| **GPUs per Compute Node** | 2 × NVIDIA A100 |
| **GPU CUDA Cores per Compute Node** | 13,824 (2 × 6,912) |
| **GPU Memory** | 80 GB HBM2e per NVIDIA A100 |

## High Memory Compute Nodes

High Memory Compute nodes are specialized nodes designed to handle workloads that require a large amount of memory.

| **Specification** | **Value** |
|-------------------|-----------|
| **Number of High Memory Compute Nodes** | 320 |
| **Processor** | 2 × Intel® Xeon® Gold 6240R |
| **CPU Base Frequency** | 2.4 GHz |
| **CPU Cores per Node** | 48 |
| **Total CPU Cores** | 15,360 |
| **System Memory per Node** | 768 GB DDR4 (2933 MHz) |
| **Total System Memory** | 245,760 GB |
| **Local Storage** | 800 GB SSD per node |

## Trinetra Nodes

It consists of 128 nodes connected with an indigenously developed Trinetra High performance interconnect. These nodes are connected in 5D torus topology to enable high bandwidth low latency communication.

| **Trinetra Nodes: 128** | |
|---|---|
| 2 × Intel Xeon G-6240R<br>Cores = 48, 2.4 GHz | Total Cores = 6144 cores |
| Memory = 192 GB, DDR4 2933 MHz | Total Memory = 24576 GB |
| SSD = 800 GB (local) per node | |


## Storage

- Based on the Lustre parallel file system.
- The storage subsystem provides a total usable capacity of 20 PiB Primary Storage and 10 PiB Archival Storage.


## Architecture diagram

![PARAM Rudra system architecture — users connect via SSH/2FA to login and service nodes, which reach CPU, GPU and high-memory compute over the InfiniBand NDR fabric, backed by Lustre storage](assets/img/ParamArchitecture.png){ loading=lazy }

## Headline numbers

| Metric | Value |
| --- | --- |
| Peak performance | **~20 PFLOPS**  |
| Total nodes | 3,074 |
| Compute nodes | **2,906** (2,266 CPU + 320 GPU + 320 high-memory) |
| Login nodes | 14 |
| Management nodes | 24 |
| Visualization nodes | 2 |
| Interconnect | InfiniBand **NDR** (primary) + 10 Gbps Ethernet (secondary) |
| Parallel filesystem | **Lustre** — 20 PiB primary + 10 PiB archival |
| Operating system | Rocky Linux 9.6 |
| Scheduler | SLURM 25.11.6 |

## Node types and per-node hardware

| Node class | Count | CPU | Cores/node | Memory/node | Local SSD | GPUs | Partition |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CPU compute** | 2,266 | 2× Intel Xeon Gold 6240R @ 2.4 GHz | 48 | 192 GB DDR4-2933 | 800 GB | — | `cpu` |
| **GPU compute** | 320 | 2× Intel Xeon Gold 6240R @ 2.4 GHz | 48 | 192 GB DDR4-2933 | 800 GB | **2× NVIDIA A100 (80 GB HBM2e)** | `gpu` |
| **High-memory** | 320 | 2× Intel Xeon Gold 6240R @ 2.4 GHz | 48 | **768 GB** | 800 GB | — | `hm` |
| **Login** | 14 | 2× Intel Xeon Gold 6240R @ 2.4 GHz | 48 | 192 GB | — | — (interactive gateway) |



Each **A100** provides 80 GB HBM2e and 6,912 CUDA cores; with 2 per GPU node that
is 13,824 CUDA cores and 160 GB of GPU memory per node.

!!! tip "Confirm live specs on an allocated node"
    Specs above are from the official system documentation. To verify what a
    given job actually sees, run these **on a compute node** (inside a job), not
    on the login node:
    ```bash
    lscpu                 # sockets, cores, model name
    free -h               # memory
    numactl --hardware    # NUMA layout
    nvidia-smi            # GPUs (on a gpu node)
    ```

## Operating System 
The operating system on PARAM Rudra is Linux – Rocky 9.6

A robust network infrastructure is essential for implementing the basic functionalities of a cluster. These functionalities include:

- Management functionalities, such as monitoring, troubleshooting, starting and stopping various components of the cluster. The network/ or portion of the network that implements this functionality is referred to as the Management fabric.
Ensuring fast read/ writes access to the storage, the network or portion of the network that implements this functionality is referred to as the storage fabric.

- Ensuring fast I/O operations, such as connecting to other clusters and connecting the cluster to various users on the campus LAN. The network or portion of the network that implements this functionality is referred to as the I/O Fabric.

- Ensuring High-Bandwidth, Low-latency communication among processors is essential for achieving high-scalability. The network or portion of the network that implements this functionality is referred to as Message Passing Fabric.

Technically, all the above functionalities can be implemented in a single network. However, for optimal performance, economic suitability, and meeting specific requirements, these functionalities are implemented using two different networks based on different technologies, as explained below:


## Interconnect

### Primary Interconnection Network

**InfiniBand: NDR**

Computing nodes of PARAM Rudra are interconnected by a high-bandwidth, low-latency interconnect network, specifically InfiniBand. It is a high-performance communication architecture owned by Mellanox, offers low communication latency, low power consumption and a high throughput. All the nodes are connected via the InfiniBand interconnect network.

### Secondary Interconnection Network

**Gigabit Ethernet:   10 Gbps /1 Gbps**

The secondary network in an HPC system provides a dedicated communication channel for system management and operational traffic. It typically uses Ethernet switches to interconnect compute, login, management, and other infrastructure nodes. The network handles tasks such as monitoring, provisioning, administration, and general system communication without interfering with compute traffic. Dedicated switches and network links improve reliability, scalability, and network isolation within the HPC environment.



### Network infrastructure 

Storage is a **Lustre** parallel filesystem:

| Path | Purpose | Quota (soft) | Backed up? | Purge |
| --- | --- | --- | --- | --- |
| `$HOME` | Code, scripts, small inputs, results to keep | **1 TiB** | Per site policy | No |
| `$SCRATCH` | High-performance working space for jobs | **1 TiB** | **No** | As per the policy, files stored in /scratch will be retained for only one week, after which they will be permanently deleted. |

Total usable capacity is 20 PiB (primary) + 10 PiB (archival). See [Data Management](data.md) for quotas, Lustre striping and the
purge policy.

## Software stack

**Software Stack** is an aggregation of software components that work together to accomplish various tasks. These tasks can range from facilitating users in executing their jobs to enabling system administrators to manage the system efficiently. Each software component within the stack is equipped with the necessary tools to achieve its specific task, and there may be multiple components of different flavors for different sub-tasks. Users have the flexibility to mix and match these components according to their preferences. For users, the primary focus is on preparing executables, executing them with their datasets, and visualizing the output. This typically involves compiling codes, linking them with communication libraries, math libraries, and numerical algorithm libraries, preparing executables, running them with desired datasets, monitoring job progress, collecting results, and visualizing output.

System administrators, on the other hand, are concerned with ensuring optimal resource utilization. To achieve this, they may require installation tools, health-check tools for all components, efficient schedulers, and tools for resource allocation and usage monitoring.

The software stack provided with this system has a wide range of software components that meet the needs of both users and administrators. Figure 2 illustrates the components of the software stack.

C-CHAKSHU, a multi-cluster management tool designed to help administrators operate the HPC System efficiently. It also enables the users to monitor system metrics relating to CPU, storage, interconnects, file system and application-specific utilization from a single dashboard. For more information, please follow the link:  **[CHAKSHU Dashboard](https://chakshu.paramrudra.cdacb.in/)**.
<br>
<br>


![Software Stack -](assets/img/SoftwareStack.png){ loading=lazy}
*Figure 2 : Software Stack.*
<br>
<br>


| Functional area | Component(s) |
| --- | --- |
| Operating system | Rocky Linux 9.6 (x86_64) |
| Provisioning / cluster manager | xCAT |
| Monitoring | **C-CHAKSHU**, Nagios, Ganglia |
| Resource manager | SLURM 25.11.6 |
| I/O | Lustre client |
| Interconnect stack | Mellanox InfiniBand (MLNX_OFED) |
| Compilers | GNU (gcc/g++/gfortran), Intel oneAPI (icx/icpx/ifx) |
| MPI | MVAPICH, OpenMPI, MPICH, Intel MPI |
| Package manager | **Spack** (primary), Environment Modules, Miniconda |


