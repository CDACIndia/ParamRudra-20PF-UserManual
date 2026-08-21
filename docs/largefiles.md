# Handling Large Files Efficiently on HPC Clusters

A practical guide to Lustre file striping for read/write-intensive jobs

## 1. Overview

Reading and writing files that are gigabytes (GB) in size can take substantially longer than expected on the cluster's shared storage system. This is because, by default, a file is served through a single RAID controller (i.e., a single storage target), which limits the achievable I/O bandwidth regardless of how many compute nodes or processes are accessing it.

This slowdown can be avoided through explicit action by the user: striping the file across multiple storage pools, known as Object Storage Targets (OSTs), before it is written. This manual explains what striping is, when to use it, and how to apply it correctly for both output and input files.

## 2. Background: Why Large-File I/O Can Be Slow

The cluster's parallel file system (Lustre) stores file data on OSTs, each backed by its own RAID controller and disk set. By default, a newly created file is placed entirely on a single OST.

This default behavior means that for a single, non-striped file:

- All reads and writes to that file are funneled through one RAID controller.
- Throughput is capped at the bandwidth of that single controller, no matter how many parallel processes or nodes are involved.
- Large files (GB scale) and files that are accessed concurrently by many processes are the most affected, since they place sustained, heavy demand on that single controller.

!!! Key-idea
     Striping spreads a file's data across several OSTs so that reads and writes can proceed in parallel across multiple controllers, improving both bandwidth and latency.

## 3. Understanding File Striping

Striping is configured with the Lustre command-line tool lfs setstripe, using two key parameters:

| **Option** | **Meaning** | **Notes** |
|---|---|---|
| `-c` | Stripe count | Number of OSTs the file is spread across, e.g. `-c 8` |
| `-S` | Stripe size | Amount of data written to one OST before moving to the next, e.g. `-S 32m` (32 MB) |

## 4. Critical Rule: Striping Must Be Set Before File Creation

File striping is a property assigned when a file is created. It does not take effect on files that already exist, and it cannot be applied retroactively by running lfs setstripe on an existing file.

As a result, striping must be applied in one of two ways:

1. Set the stripe layout on the destination directory or the target filename before the file is created (for new output files).
2. Create a new, correctly striped copy of an existing file, then replace the original with that copy (for pre-existing input files).

!!! Note
    Common mistake:  Running lfs setstripe directly on a file that already exists has no effect on its layout. The file must not exist yet at the time the command is run.

## 5. How to Stripe Files

### 5.1 Output Files (created during job execution)

For files your job will generate — result files, checkpoints, logs written at scale, etc. — set the stripe layout immediately before the job creates the file, using the intended output filename or its parent directory:

`lfs setstripe -c 8 -S 32m <myfile>`

Because Lustre files inherit their parent directory's default striping at creation time, it is often simplest to set the desired striping on the output directory once, before the job runs; every new file created inside it will then inherit that layout automatically:

`lfs setstripe -c 8 -S 32m <output_directory>/`

### 5.2 Input Files (pre-existing files you need to stripe)

Since striping cannot be applied to a file that already exists, restriping an input file means creating a new, striped file and copying the data into it:

- Set the desired stripe layout at the target path — the target file must not exist yet:

`lfs setstripe -c 8 -S 32m <myfile>        // <myfile> must not already exist`

- Copy the original data into that newly created, striped file, e.g.:

`cp <original_file> <myfile>`

- Once the copy is verified, replace or remove the original as appropriate for your workflow.

## 6. Which Files Should Be Striped

Not every file benefits from striping — applying it indiscriminately (especially to many small files) adds metadata overhead. Consider striping when a file meets either of the following criteria:

- Size: the file is on the order of gigabytes (GB) or larger.
- Shared, concurrent access: the file is read from or written to concurrently by many parallel processes (e.g. a common dataset accessed by an MPI job). 

!!! Note
    Rule of thumb:  Small files (KB–low MB) and files accessed by a single process generally do not need striping and should be left at the default layout.

## 7. Verifying Stripe Settings

Before and after applying striping, confirm the current layout with:

`lfs getstripe <myfile>`

This reports the stripe count, stripe size, and the specific OSTs the file's data is placed on, allowing you to confirm the setting took effect as intended.

## 8. Choosing Stripe Count and Stripe Size

- Stripe count (-c): a higher count spreads I/O across more OSTs and controllers, which helps large files and highly concurrent shared access. A commonly used starting point is -c 4 to -c 8; very large or heavily shared files may warrant more.
- Stripe size (-S): controls the chunk written to each OST before moving to the next. 32m (32 MB) is a reasonable general-purpose default; workloads with very large sequential I/O may benefit from larger stripe sizes.
!!! Note
    Recommendation:  If you are unsure which values to use, check with your cluster's HPC support or documentation, as optimal values can depend on the storage system's specific OST count and configuration.

## 9. Common Pitfalls

- Running lfs setstripe on a file that already exists — this silently has no effect on the file's layout.
- Setting striping only after the job has already created the output file — the file will remain on a single OST.
- Over-striping many small files — this adds unnecessary metadata overhead without a performance benefit.
- Forgetting to verify with lfs getstripe — always confirm the layout was actually applied before relying on it for a production run.

## 10. Quick Reference

| **Task** | **Command** |
|---|---|
| Stripe a new output file | `lfs setstripe -c 8 -S 32m <myfile>` |
| Stripe a directory (inherited by new files) | `lfs setstripe -c 8 -S 32m <output_directory>/` |
| Stripe an existing input file | `lfs setstripe -c 8 -S 32m <newfile> && cp <original> <newfile>` |
| Check current striping | `lfs getstripe <myfile>` |

!!! Tip
    For questions or storage-tuning guidance beyond this manual, contact your HPC systems/storage support team.





























