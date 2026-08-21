# PARAM Rudra — 20 PetaFlop System User Manual

![C-DAC PARAM Rudra — 20 PetaFlop HPC System](assets/img/home1.png){ loading=lazy }

Welcome to the user documentation for **PARAM Rudra**, a ~20 PetaFlop
HPC system operated under the **National Supercomputing Mission (NSM)** by
**C-DAC**. This guide walks you through everything from your first login to
running large-scale CPU, high-memory and GPU jobs through the SLURM batch
system.

[:material-file-pdf-box: Download the full manual (PDF)](assets/PARAM-Rudra-20PF-User-Manual.pdf){ .md-button .md-button--primary }
[:material-server-network: System architecture](configuration.md#architecture-diagram){ .md-button }

*PARAM Rudra is implemented by C-DAC under the National Supercomputing Mission,
supported by MeitY and DST, Government of India.*{ .cobrand-caption }

!!! tip "New here? Start with these three pages"
    1. [Getting Access](access.md) — Visit the PARAM Rudra Open OnDemand (OOD) portal.
    2. [Environment](environment.md) — modules, shell and your `/home` & `/scratch` directories.
    3. [Batch System (SLURM)](batch.md) — never run compute on the login node; submit jobs instead.



## System at a glance

| Resource | Count | Node name prefix |
| --- | --- | --- |
| **Total nodes** | **3,074** | — |
| **Total compute nodes** | **2,906** | — |
| CPU-only nodes (2× Xeon Gold 6240R, 48c, 192 GB) | 2,266 | `cbcn####` |
| GPU nodes (+ 2× NVIDIA A100 80 GB) | 320 | `cbgpu####` |
| High-memory nodes (768 GB RAM) | 320 | `cbhm####` |
| Trinetra nodes(2× Xeon Gold 6240R, 48c, 192 GB) | 128 | —
| Peak performance | ~20 PFLOPS | — |
| Interconnect | **InfiniBand NDR** | — |
| Storage | **Lustre** (20 PiB + 10 PiB archival) | — |
| OS / scheduler | Rocky Linux 9.6 / SLURM 25.11.6 | — |
| Login nodes | 14 (`login01…`) | — |

<div class="grid cards" markdown>

- :material-login: **[Getting Access](access.md)**

    Visit the PARAM Rudra Open OnDemand (OOD) portal.

- :material-server-network: **[System Configuration](configuration.md)**

    Node types, partitions, interconnect and storage layout.

- :material-package-variant: **[Modules & Spack](modules.md)**

    Load compilers, libraries and applications with `module` and `spack`.

- :material-hammer-wrench: **[Building Software](building.md)**

    Intel/GNU compilers, MPI wrappers, CUDA (A100 `sm_80`), MKL, OpenACC.

- :material-calendar-clock: **[Batch System (SLURM)](batch.md)**

    Partitions, `sbatch` scripts, `srun`, dependencies and interactive jobs.

- :material-expansion-card: **[GPU Computing](gpu.md)**

    Requesting A100 GPUs, CUDA MPS, multi-GPU and multi-node runs.

- :material-brain: **[Machine Learning / DL](machine-learning.md)**

    Pre-built PyTorch/TensorFlow Conda envs and Jupyter on GPU nodes.

- :material-flask: **[Applications](applications/index.md)**

    GROMACS, LAMMPS, WRF, OpenFOAM, NAMD and more via Spack.

- :material-speedometer: **[Best Practices & Performance](best-practices.md)**

    Compiler tuning (GCC/Intel LLVM/NVHPC), OpenMP, MPI, case studies.

</div>

## The golden rules

!!! danger "Do not run jobs on the login node"
    Running compute-heavy processes on a login node degrades service for
    everyone and **will result in your process being terminated without prior
    notice** (repeat offences may cost you account access). Always submit work
    through SLURM — see the [Batch System](batch.md) page.


!!! note
    **$HOME:** `/home/<group_name>/<user_name>/`

    **$SCRATCH:** `/scratch/<group_name>/<user_name>/`


!!! warning "`/scratch` is purged"
    As per the policy, files stored in /scratch will be retained for only one week, after which they will be permanently deleted.
    `/scratch` is fast working space, **not** long-term
    storage. Back up important results elsewhere — see [Data Management](data.md).

## Quick reference card

```bash
# Connect: Visit the PARAM Rudra Open OnDemand (OOD) portal.


# Software: Spack (primary) + modules
module load spack
. /home/apps/spack/share/spack/setup-env.sh
spack find                   # installed packages
spack load <pkg>             # load one
module load miniconda        # Python / Conda ML environments

# SLURM essentials
sinfo                        # partition / node status
squeue -u $USER              # your jobs
sbatch job.slurm             # submit a batch job
salloc ...                   # interactive allocation
scancel <jobid>              # cancel a job

# Always specify your accounting/project code
#SBATCH -A <your_account>
```

---

!!! note "About this manual"
    This is a **community/user-maintained** guide for the C-DAC PARAM Rudra
    20 PetaFlop system, grounded in the **official C-DAC PARAM Rudra User
    Manual** plus the live login banner and SLURM configuration. Where a value
    may change over time (versions, quotas, hashes), the page tells you the
    command to confirm it on the system. The authoritative source is always the
    **login banner** and the C-DAC support desk (`rudrasupport@cdac.in`).
    Corrections are welcome via
    [pull request](https://github.com/samcom12/paramrudra-user-manual).
