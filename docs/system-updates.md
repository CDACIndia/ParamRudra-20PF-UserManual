# System Update Notice

!!! info "Source and status"
    This page consolidates updated figures from a **C-DAC PARAM Rudra 20 PF
    facility summary** (2026). Several values here **supersede or differ from**
    numbers documented elsewhere in this manual. Items marked
    :material-alert:{ title="verify" } **need confirmation against the live
    system** before the corresponding pages are updated — please verify with the
    [support desk](support.md) (`rudrasupport@cdac.in`) and, once confirmed, fold
    them into the relevant pages.

## Hardware configuration (updated)

- **Total nodes: 3,074** :material-alert: — *(earlier documented: 2,906 compute
  nodes; the additional ~168 nodes likely cover login/service/other roles —
  confirm the exact breakdown).*
- **2,266 CPU-only** compute nodes — 2× Intel Xeon Gold 6240R, 48 cores, 192 GB RAM.
- **320 GPU** nodes — 2× NVIDIA A100 per node.
- **320 high-memory** nodes — **768 GB DDR5** RAM.
- **Storage: 20 PiB primary** + **10 PiB archival** :material-alert: — *(earlier
  documented: 10 PiB primary + 10 PiB archival)*, on the **Lustre** parallel
  filesystem.

## Operating environment (updated)

- **OS:** Rocky Linux 9.6
- **Workload manager:** **SLURM 25.11.6** :material-alert: — *(earlier
  documented: SLURM 23.11.10)*
- **Package manager:** Spack

## Access methods (updated)

Three documented ways to reach the system:

1. **Open OnDemand web portal** — **<https://ood.paramrudra.cdacb.in>**, with
   two-factor authentication. *(New — not previously documented.)*
2. **SSH** on port **4422**, using **passwordless ED25519 key pairs**
   :material-alert: — *(the current [Getting Access](access.md) page documents
   password + Google Authenticator 2FA per the official manual; confirm whether
   the system now uses key-based passwordless SSH, TOTP 2FA, or both).*
3. **File transfers** via **SCP** or **WinSCP** (port 4422).

Example key-based login:

```bash
# Generate an Ed25519 key pair (once, on your machine)
ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519_rudra

# Connect
ssh -i ~/.ssh/id_ed25519_rudra -p 4422 <username>@paramrudra.cdacb.in
```

## SLURM partitions (updated)

:material-alert: **Confirm before relying on these** — they differ from the
`cpu` / `hm` / `gpu` partitions documented on the
[Batch System](batch.md) and [Resource Management](resource-management.md) pages.

| Partition | Purpose | Max walltime (indicative) |
| --- | --- | --- |
| `debug` | Short test/debug runs | 1 hour |
| `terai` | General compute | up to 24 hours |
| `shiwalik` | General compute | up to 24 hours |
| `himachal` | Compute | *(confirm)* |
| `himadri` | Compute | *(confirm)* |
| GPU queues | GPU-accelerated jobs | *(confirm)* |

Job priority incorporates **age, fair-share, job-size, and QoS** factors. Submit
work as SLURM batch scripts specifying nodes, tasks, walltime and partition; the
system supports **sequential, OpenMP, MPI, hybrid (MPI+OpenMP), and
GPU-accelerated** workloads.

## Development tools

**Compilers:**

- GNU (`gcc`, `g++`, `gfortran`) — 8.5.0 … 15.2.0
- Intel oneAPI — 2022.2.0, 2024.2.1
- NVIDIA CUDA — 11.8.0, 12.2.2, 13.1.1

**Debugging:** `gdb` for source-level debugging (breakpoints, watchpoints,
variable inspection) — see [Debugging](debugging.md).

## ML / DL support (updated)

Conda environments provide **PyTorch, TensorFlow, JAX** :material-alert: *(JAX is
newly listed)* and other popular ML frameworks with GPU support pre-installed.
See [Machine Learning / DL](machine-learning.md).

## Storage quotas (updated)

:material-alert: **These differ from the current [Data Management](data.md) page**
(which documents 50 GB `/home`, 200 GB `/scratch`, and a 3-month scratch purge):

| Area | Quota | Retention |
| --- | --- | --- |
| `/home` | **500 GB** | persistent |
| `/scratch` | **1 TB** | **files auto-deleted after 1 week** |

!!! danger "Scratch retention may be much shorter than before"
    If confirmed, `/scratch` files are removed after **one week** of inactivity
    (vs. the 3-month policy documented elsewhere). Back up results promptly and
    verify the current policy before running long campaigns.

## Support

Technical support: **`rudrasupport@cdac.in`**.

---

!!! note "For reviewers/maintainers"
    Once each :material-alert: item above is confirmed against the live system,
    update the corresponding page (Configuration, Getting Access, Batch System,
    Resource Management, Data Management, Machine Learning) and then this notice
    can be trimmed to only the still-pending items.
