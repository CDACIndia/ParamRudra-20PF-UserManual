# Running Apptainer Containers

Apptainer provides a portable way to execute software in isolated containers on both CPU and GPU compute nodes without requiring root privileges.

## Prerequisites

Load the Apptainer module if it is not already available.

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

You only need to download the image once.

Apptainer can pull images directly from Docker/OCI registries and stores them in the Singularity Image Format (SIF). :contentReference[oaicite:0]{index=0}

---

# Running on a CPU Compute Node

## Request a CPU Interactive Job

Example:

```bash
srun --pty -N1 -n1 --cpus-per-task=4 bash
```

Adjust the resource request according to your allocation.

---

## Execute Commands Inside the Container

Print the operating system information:

```bash
apptainer exec ubuntu.sif cat /etc/os-release
```

Run a shell inside the container:

```bash
apptainer shell ubuntu.sif
```

Example session:

```bash
Apptainer> uname -a
Apptainer> python3 --version
Apptainer> exit
```

You can also execute the container's default runscript:

```bash
apptainer run ubuntu.sif
```

The `exec`, `shell`, and `run` commands are the primary methods for executing applications inside Apptainer containers. :contentReference[oaicite:1]{index=1}

---

# Running on a GPU Compute Node

## Request a GPU Interactive Job

Example:

```bash
srun --pty -N1 --gres=gpu:1 bash
```

Use the GPU resource request appropriate for your cluster.

---

## Enable NVIDIA GPU Support

When running on NVIDIA GPU nodes, use the `--nv` option.

Verify GPU visibility:

```bash
apptainer exec --nv ubuntu.sif nvidia-smi
```

If the GPU has been allocated correctly, the output should display the GPU information.

The `--nv` flag automatically exposes NVIDIA devices and required CUDA libraries inside the container. :contentReference[oaicite:2]{index=2}

---

# Useful Examples

Display the current working directory inside the container:

```bash
apptainer exec ubuntu.sif pwd
```

List files from your current directory:

```bash
apptainer exec ubuntu.sif ls
```

Run Python:

```bash
apptainer exec ubuntu.sif python3
```

---

# Using Bind Mounts

Your home directory is typically available automatically. To bind additional directories:

```bash
apptainer exec \
    --bind /path/on/host:/workspace \
    ubuntu.sif \
    ls /workspace
```

---

# Cleaning the Cache

Downloaded OCI layers are stored in the Apptainer cache.

View cache:

```bash
apptainer cache list
```

Clean cache:

```bash
apptainer cache clean
```

---

# Summary

| Task | Command |
|------|---------|
| Download container | `apptainer pull ubuntu.sif docker://ubuntu:22.04` |
| Open shell | `apptainer shell ubuntu.sif` |
| Execute command | `apptainer exec ubuntu.sif <command>` |
| Run default application | `apptainer run ubuntu.sif` |
| Use NVIDIA GPU | `apptainer exec --nv ubuntu.sif nvidia-smi` |
| Clean cache | `apptainer cache clean` |