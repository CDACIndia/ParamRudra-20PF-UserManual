# Machine Learning / Deep Learning

Most of the popular python-based ML/DL libraries are installed on the PARAM Rudra system. Users while developing and testing their applications, can use conda based python installation.

For the conda environment different modules are prepared. Users can check the list of the modules by using “module avail” command. Shown below is an example of loading conda environments in the current bash shell and continuing with application development. 

Once logged into PARAM Rudra HPC Cluster, check which all libraries are available, loaded in the current shell. To check list of modules loaded in current shell, use the command given below:

```bash
$ module list 
```

To check all modules available on the system, but not loaded currently, use the command given below:

```bash
$ module avail
```

Defaults libraries and framework specific conda environment has been made available for user to start with application development which is installed with most of the popular python packages as shown below

Loading the Conda Base Module and Activating the Environments

In order to use base conda environment we first, access and load the miniconda module, which provides access to the base environment which is installed with default packages:

```bash
$ module load miniconda
```

To see the list of other packages installed, use the command given below,

```bash
$ conda list 
```

We provide multiple conda environments that include basic machine learning packages, as well as common image processing and natural language processing packages, for your machine learning projects.

The following table shows currently available conda environments with their version (all include GPU support):

| Category | Module(s) | Version |
| --- | --- | --- |
| Deep learning | `Tensorflow` / `Tensorflow-gpu` | 2.15.0 |
| | `Pytorch` / `Pytorch-gpu` | 2.2.0 / 2.2.1 |
| | `Keras` / `Keras-gpu` | 3.0.5 |
| | `Theano` / `Theano-gpu` | 1.0.5 |
| | `Caffe` / `Caffe-gpu` | 1.0 |
| Distributed DL | `Horovod` (TF / PyTorch) | 0.28.1 |
| Data science | `Rapids` | 21.06 |

Also available: cuDNN, NumPy, SciPy, scikit-learn.

```bash
module load Pytorch
python -c "import torch; print(torch.__version__, torch.cuda.is_available())"
```

Useful Conda commands:

```bash
conda info --env              # list environments
conda list -n <env>           # packages in an environment
conda deactivate              # leave the active environment
```

To activate any one of the environments we can load it on PARAM Rudra, load module “ENV_NAME” as shown below: 
```bash
$ module load <ENV_NAME>
```

Once the **ENV_NAME** module is loaded, end-users can use all libraries inside their python program.  Users can load those libraries using the “module load” command and use them for their applications. 

Example: To activate Pytorch environment we can load it on PARAM Rudra, using module load Pytorch as shown below: 

```bash
$ module load Pytorch
```

This will activate Pytorch environment in which users can use pytorch library and its related functionalities

Useful Conda Commands

After loading the module, you will have access to conda commands, including:

```bash
$ conda info --env

```

shows available environments.

```bash
$ conda list -n env_name

```

Shows installed packages within an environment.

```bash
(env_name)$ conda deactivate

```

Deactivates an environment after loading.

For more detailed documentation, see the Conda website.

Managing and Installing Python Packages in Conda Environments

You have two options to install your own Python packages in our machine learning environment:

- Use the pip tool to install them directly

- Build your own conda environment

Consider the benefits and disadvantages of each method, before choosing which works best for you.

**NOTE:** Use Conda primarily for environment management, especially in scientific computing and data science projects where non-Python dependencies are common.

Use pip for installing Python packages from PyPI when you don't need the advanced environment management features provided by Conda.








## Build your own environment

Building your own conda environment gives you the control to manage and install your own packages, and they will be less likely to have version errors than the pip-installed packages. The easiest way to create your own environment is to clone an existing conda environment into your own directory, then modify it.

Creating an environment can take up a significant portion of your disk quota, depending on the packages installed. To ensure that you can use your conda environment properly, please familiarize yourself with all the basic conda commands.

Conda based installation provides the latest version of DL framework, however users can install their own choice of DL framework or library version locally by following below steps.

**Step 1.** Login to Rudra cluster by using your credential.

**Step 2.**  Activate conda environment. 

```bash
$ module load miniconda
```

**Step 3.** Create the local environment myenv  (myenv is the environment name, you can give any name of your choice).

```bash
$ conda create --name myenv
```

**Step 4.** Activate a newly created environment. 

```bash
$ conda activate myenv
```

**Step 5.** Install your own DL framework / python library. <package-name> will get replaced by desired package which user wants to install

$ conda install `<package-name>`

Example: In order to install numpy we can use below command. 

```bash
$ conda install numpy 
```

Now you can use the newly installed package in your python program.

## Submitting a DL job with `sbatch`

Activate the environment inside the job, run, then deactivate:

```bash
#!/bin/bash -x
#SBATCH -N 1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=8
#SBATCH --gres=gpu:1              # request a GPU (drop for CPU-only)
#SBATCH -p gpu                    # gpu partition
#SBATCH -A myproject              # your account
#SBATCH -J dl-train
#SBATCH -t 05:00:00
#SBATCH -o %j.out
#SBATCH -e %j.err

cd $SLURM_SUBMIT_DIR
module purge
module load miniconda/26.7.0
conda activate myenv              # or: module load Pytorch
python train.py
conda deactivate
```

For **multi-node, multi-GPU** distributed training (PyTorch DDP / Horovod across
the InfiniBand fabric), see the
[multi-node GPU example](gpu.md#multi-node-gpu-distributed-jobs) and
[example #7](examples.md#7-multi-node-multi-gpu-pytorch-ddp).

## Launching a Jupyter notebook (SSH tunnel)

Run Jupyter on a compute node and open it in your **local** browser via an SSH
tunnel. Never run it on the login node.

**1. Grab a GPU node interactively**

```bash
salloc --nodes=1 --time=1:00:00 --gres=gpu:1 --partition=gpu-small -A myproject
squeue --me                       # note the assigned node, e.g. cbgpu0044
ssh cbgpu0044                     # hop onto it
```

**2. Start the notebook on the compute node**

```bash
module load miniconda/26.7.0
jupyter notebook --ip=0.0.0.0 --port=8888 --no-browser
# copy the token printed in the terminal
```

**3. From your local machine, open a tunnel through the login node**

```bash
ssh -p 4422 -t -t <username>@paramrudra.cdacb.in \
    -L 8888:localhost:8888 ssh cbgpu0044 -L 8888:localhost:8888
```

**4. Open the notebook locally**

Visit `http://localhost:8888` in your browser and paste the token.

!!! note "Use the port and node SLURM actually assigned"
    Replace `8888` and `cbgpu0044` with your assigned port/node. If the port is
    taken, pick another (e.g. 8889) consistently across all three steps.

Next: [Applications](applications/index.md).
