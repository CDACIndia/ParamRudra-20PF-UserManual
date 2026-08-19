# Running Apptainer Containers with Slurm

Apptainer allows users to execute containerized applications on both CPU and GPU compute nodes without requiring root privileges.

## Prerequisites

Load the Apptainer module.

```bash
module load apptainer
```

Verify the installation:

```bash
apptainer --version
```

---

# Download a Test Container

Download a small Ubuntu container from Docker Hub.

```bash
apptainer pull ubuntu.sif docker://ubuntu:22.04
```

This creates the container image:

```
ubuntu.sif
```

The image only needs to be downloaded once.

---

# Running on a CPU Compute Node

Create a Slurm batch script named `cpu_apptainer.slurm`.

```bash
#!/bin/bash
#SBATCH --job-name=apptainer-cpu
#SBATCH --nodes=1
#SBATCH --partition=debug
#SBATCH --cpus-per-task=4
#SBATCH --time=00:05:00
#SBATCH --output=cpu-%j.out

module load apptainer

echo "Host:"
hostname

echo
echo "Container OS:"
apptainer exec ubuntu.sif cat /etc/os-release

echo
echo "Kernel:"
apptainer exec ubuntu.sif uname -a

echo
echo "Python:"
apptainer exec ubuntu.sif python3 --version
```

Submit the job:

```bash
sbatch cpu_apptainer.slurm
```

Check the queue:

```bash
squeue -u $USER
```

After the job completes, inspect the output:

```bash
cat cpu-<jobid>.out
```

---

# Running on a GPU Compute Node

Create a Slurm batch script named `gpu_apptainer.slurm`.

```bash
#!/bin/bash
#SBATCH --job-name=apptainer-gpu
#SBATCH --nodes=1
#SBATCH --partition=gpu-debug
#SBATCH --gres=gpu:1
#SBATCH --time=00:05:00
#SBATCH --output=gpu-%j.out

module load apptainer

echo "Host:"
hostname

echo
echo "GPU Information:"
apptainer exec --nv ubuntu.sif nvidia-smi
```

Submit the job:

```bash
sbatch gpu_apptainer.slurm
```

After the job finishes:

```bash
cat gpu-<jobid>.out
```

If the GPU has been allocated successfully, the output will display information about the NVIDIA GPU available inside the container.

---

# Running Your Own Application

Replace the command passed to `apptainer exec` with your application.

For example,

```bash
apptainer exec ubuntu.sif ./my_program
```

or

```bash
apptainer exec --nv ubuntu.sif python3 train.py
```

---

# Useful Commands

| Task | Command |
|------|---------|
| Download container | `apptainer pull ubuntu.sif docker://ubuntu:22.04` |
| Submit CPU job | `sbatch cpu_apptainer.slurm` |
| Submit GPU job | `sbatch gpu_apptainer.slurm` |
| Check queue | `squeue -u $USER` |
| View output | `cat cpu-<jobid>.out` |
| Execute command | `apptainer exec ubuntu.sif <command>` |
| Execute with NVIDIA GPU | `apptainer exec --nv ubuntu.sif <command>` |