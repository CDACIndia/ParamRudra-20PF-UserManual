# Running Enroot Containers with Slurm

Enroot is a lightweight container runtime for OCI/Docker containers. On this cluster, Enroot is installed natively on the **GPU compute nodes** and is **not available on the login or CPU compute nodes**.

> **Note**
> - Enroot is available **only on GPU compute nodes**.
> - No module needs to be loaded.
> - Container images should be imported and created from within a GPU job.

---

# Create an Enroot Container

Create a Slurm batch script named `enroot_create.slurm`.

```bash
#!/bin/bash
#SBATCH --job-name=enroot-create
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --gres=gpu:1
#SBATCH --time=00:10:00
#SBATCH --output=enroot-create-%j.out

echo "Importing Ubuntu container..."

enroot import docker://ubuntu:22.04

echo "Creating container..."

enroot create --name ubuntu ubuntu+22.04.sqsh

echo "Done."
```

Submit the job:

```bash
sbatch enroot_create.slurm
```

This creates:

```
ubuntu+22.04.sqsh
ubuntu
```

These steps only need to be performed once.

---

# Running a Container on a GPU Compute Node

Create a Slurm batch script named `gpu_enroot.slurm`.

```bash
#!/bin/bash
#SBATCH --job-name=enroot-gpu
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --gres=gpu:1
#SBATCH --time=00:05:00
#SBATCH --output=gpu-%j.out

echo "Host:"
hostname

echo
echo "Container OS:"
enroot start ubuntu cat /etc/os-release

echo
echo "GPU Information:"
enroot start ubuntu nvidia-smi
```

Submit the job:

```bash
sbatch gpu_enroot.slurm
```

View the output after completion:

```bash
cat gpu-<jobid>.out
```

If the GPU has been allocated successfully, `nvidia-smi` will display the NVIDIA GPU visible inside the container.

---

# Running Your Own Application

Replace the command passed to `enroot start` with your application.

For example:

```bash
enroot start ubuntu python3 train.py
```

or

```bash
enroot start ubuntu ./my_program
```

---

# Removing the Container

Remove the container filesystem:

```bash
enroot remove ubuntu
```

To remove the downloaded image:

```bash
rm ubuntu+22.04.sqsh
```

---

# Summary

| Task | Command |
|------|---------|
| Import image | `enroot import docker://ubuntu:22.04` |
| Create container | `enroot create --name ubuntu ubuntu+22.04.sqsh` |
| Submit creation job | `sbatch enroot_create.slurm` |
| Submit GPU job | `sbatch gpu_enroot.slurm` |
| Run a command | `enroot start ubuntu <command>` |
| Remove container | `enroot remove ubuntu` |