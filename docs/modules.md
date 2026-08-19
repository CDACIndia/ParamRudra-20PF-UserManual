# Modules & Conda

PARAM Rudra exposes software through two complementary mechanisms:

- **[Spack](spack.md)** — the *primary* package manager for compilers, MPI,
  libraries and most applications (`spack load ...`).
- **Environment Modules** (`module ...`) — used to enable Spack itself, the
  **Miniconda** Python stack, and pre-built **ML/DL Conda environments**
  (PyTorch, TensorFlow, …) and some applications.

This page covers `module` and Conda. For `spack`, see the
[Spack Packages](spack.md) page.

## Environment Modules

| Command | What it does |
| --- | --- |
| `module avail` | List all available modules |
| `module load <name>` | Load a module (optionally `name/version`) |
| `module unload <name>` | Unload a module |
| `module list` | Show loaded modules |
| `module purge` | Unload everything |
| `module show <name>` | Show what a module sets |

```bash
module avail
module avail 2>&1 | grep -i conda    # module output goes to stderr — use 2>&1
module load spack                    # enable Spack
module load miniconda                # enable the Conda/Python base
module list
```

!!! tip "`module avail` prints to stderr"
    To search it, redirect: `module avail 2>&1 | grep -i <keyword>`.

## Conda / Python (Miniconda)

You typically land in a Conda **base** environment (`(base)` in your prompt).
Load the module explicitly if needed:

```bash
module load miniconda/26.7.0
conda list                # packages in the current environment
conda info --env          # list all environments
```
## Building Your Own Conda Environment

!!! warning "Don't install into `base`"
    Create your own named environments instead of modifying `base`:
    ```bash
    conda create --name myenv python=3.11
    conda activate myenv
    conda install numpy scipy
    conda deactivate
    ```
    Environments can be large — mind your **1 TiB `/home` quota**
    (`du -sh ~/.conda`).

Building your own conda environment gives you the control to manage and install your own packages, and they will be less likely to have version errors than the pip-installed packages. The easiest way to create your own environment is to clone an existing conda environment into your own directory, then modify it.

Creating an environment can take up a significant portion of your disk quota, depending on the packages installed. To ensure that you can use your conda environment properly, please familiarize yourself with all the basic conda commands.

Conda based installation provides the latest version of DL framework, however users can install their own choice of DL framework or library version locally by following below steps.

Step 1. Login to Rudra cluster by using your credential.

Step 2.  Activate conda environment. 

```bash
 $ module load miniconda/26.7.0
```
<br>
Step 3. Create the local environment myenv  (myenv is the environment name, you can give any name of your choice).

```bash
$ conda create --name myenv
```
<br>
Step 4. Activate a newly created environment. 

```bash
$ conda activate myenv
```
Step 5. Install your own DL framework / python library. <package-name> will get replaced by desired package which user wants to install

$ conda install `<package-name>`

Example: In order to install numpy we can use below command. 


```bash 
 $ conda install numpy
```

Now you can use the newly installed package in your python program.


## Submitting job using sbatch script for DL Application 

You can activate your machine learning environment, run your program, and deactivate the environment in a SLURM sbatch script. For example:


```bash
#!/bin/bash -x
#SBATCH -N 1
#SBATCH --ntasks-per-node=<np>
#SBATCH -p debug
#SBATCH -J <job_name>
#SBATCH -t 05:00:00
#SBATCH -o %j.out             # name of stdout output file(--output)
#SBATCH -e %j.err             # name of stderr error file(--error)
cd $SLURM_WORKDIR
module purge
module load miniconda/26.7.0		# load the module and environment
conda activate <env_name>	# load working environment
python <script>.py		# run python script
conda deactivate 		# deactivate environment
# end of script

```
## Pre-built ML/DL Conda environments

PARAM Rudra ships ready-to-use, **GPU-enabled** Conda environments for machine
learning, exposed as modules. Load one with `module load <ENV_NAME>`:

| Category | Environment(s) | Version |
| --- | --- | --- |
| Deep learning | `Tensorflow` / `Tensorflow-gpu` | 2.15.0 |
| | `Pytorch` / `Pytorch-gpu` | 2.2.0 / 2.2.1 |
| | `Keras` / `Keras-gpu` | 3.0.5 |
| | `Theano` / `Theano-gpu` | 1.0.5 |
| | `Caffe` / `Caffe-gpu` | 1.0 |
| Distributed DL | `Horovod` (TensorFlow / PyTorch) | 0.28.1 |
| Data science | `Rapids` | 21.06 |

```bash
module load Pytorch        # activates the PyTorch environment
python -c "import torch; print(torch.__version__, torch.cuda.is_available())"
```

Full ML/DL workflow — building your own environment, `sbatch` templates and
launching Jupyter — is on the [Machine Learning / DL](machine-learning.md) page.

## Reproducibility

Always load **explicit versions** (modules) and **hashes** (Spack) in job scripts
so runs don't silently change when defaults are updated, and record what was
loaded for provenance:

```bash
module list 2>&1          # captured into your SLURM .out file
spack find -l <pkg>       # note the exact build hash
```

Next: [Spack Packages](spack.md) or [Building Software](building.md).
