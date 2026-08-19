# FAQ & Support

# Getting Help – PARAM Rudra Support  

The PARAM Rudra Support Center is the official helpdesk portal for reporting issues, requesting assistance, and tracking the status of support requests related to the PARAM Rudra High Performance Computing (HPC) system. It is provided under the National Supercomputing Mission by C-DAC.

This manual explains, step by step, how to raise a new support ticket, choose the correct help topic, describe your issue effectively, and monitor your ticket until it is resolved.

!Note 
    The Support Center uses Single Sign-On (SSO). If you are already logged in to your PARAM Rudra account, you will not need to log in again separately to raise a ticket.

## 2. Accessing the Support Center

There are two quick ways to reach the Support Center from the PARAM Rudra portal.

### 2.1 Using the Help Menu

1.   Log in to the PARAM Rudra portal as usual.
2.   Locate the Help menu in the top-right corner of the navigation bar.
3.   Click Help to expand the drop-down menu.

![help](assets/img/help1.png){loading=lazy}

**Figure 1: The Help menu is located at the top-right corner of the PARAM Rudra dashboard.**

From the expanded Help menu, select Ticketing Portal. This opens the Support Center in a new page.

![help](assets/img/help2.png){loading=lazy}

**Figure 2: Select “Ticketing Portal” from the Help drop-down menu.**

!!! Tip
    You can bookmark the Support Center URL for quicker access next time.

### 3. Support Center Home Page

Once redirected, you will land on the Support Center home page. This page gives you an overview of the ticketing system and two primary actions: opening a new ticket and checking the status of an existing one.

![help](assets/img/help3.png){loading=lazy}

**Figure 3: The Support Center home page, showing the Open a New Ticket and Check Ticket Status options.**

Because Single Sign-On is enabled, your name already appears at the top-right of the page — there is no separate login step.

### 4. Opening a New Support Ticket

Follow the steps below to raise a new support ticket.

**Step 1: Start a new ticket**

On the Support Center home page (or the top navigation bar), click Open a New Ticket.

**Step 2: Select the correct Help Topic**

Choose the topic that best matches your issue from the drop-down list:

1.   Application Support — for issues related to any software, application, or job script, such as compilation errors, library issues, or job failures.
2.   System Support — for issues related to login, accessibility, or resource/quota management on the system.

![help](assets/img/help4.png){loading=lazy}

**Figure 4: Selecting a Help Topic when opening a new ticket.**


!!! Note
    Choosing the correct Help Topic routes your ticket to the right support team and helps resolve it faster.

**Step 3: Fill in the Ticket Details**

Your Email and Client (name) fields are pre-filled automatically from your logged-in account. Complete the remaining fields:

- Issue Summary — a short, clear title for your problem (e.g., “Unable to submit SLURM job”).
- Description box — explain the issue in detail: what you were trying to do, what happened instead, and any error messages you saw.
- Attachments (optional) — drag and drop files, or use “choose them”, to attach screenshots, logs, or scripts that help explain the issue.

![help](assets/img/help5.png){loading=lazy}

**Figure 5: Completing the Issue Summary and Description before submitting a ticket.**

!!! Tip
    Include exact error messages, job IDs, and the date/time the issue occurred — this is the single biggest factor in getting a fast, accurate resolution.


**Step 4: Submit the ticket**

Review the information you have entered, then click Create Ticket to submit your request.

!!! IMPORTANT
    Use Reset to clear the form, or Cancel to discard the ticket. Once submitted, a ticket cannot be deleted — only updated through replies.


### 5. Ticket Confirmation

After clicking Create Ticket, you are taken to the ticket detail page. This confirms that your request has been logged and shows a unique ticket number (for example, #727792) that you can reference in all future correspondence.

![help](assets/img/help6.png){loading=lazy}

**Figure 6: A newly created ticket, showing its ticket number, status, and details.**

From this page you can review:

- Basic Ticket Information — status, department, and creation date/time.
- User Information — the name, email, and phone number on record.
- The original issue description you submitted.
- A Post a Reply box, used to add further information or respond to the support team.
 
### 6. Tracking Your Ticket Status

You can check on any of your tickets at any time:

1.   Go to the Support Center and click the Tickets tab in the top navigation bar (it shows a running count of your tickets, e.g., Tickets (1)).
2.   Select the ticket you want to view from the list.
3.   Review its current status (Open, In Progress, Resolved, or Closed) and any replies from the support team.

To provide more information or respond to a support agent, type your message in the Post a Reply box on the ticket page and click Post Reply.


### 7. Best Practices for Faster Resolution

- Choose the correct Help Topic (Application Support vs. System Support) so the ticket reaches the right team immediately.
- Write a specific Issue Summary rather than a generic one (e.g., “SLURM job 48213 stuck in pending”, not “Job not working”).
- Include exact error text, job/script names, and timestamps in the description.
- Attach relevant screenshots or log files wherever possible.
- Reply on the same ticket rather than creating a duplicate ticket for the same issue.
- Check the documentation linked on the PARAM Rudra Message of the Day (e.g., SLURM and storage policies) before raising a ticket, in case the answer is already available.

### 8. Need Further Help?

If you are unable to access the Support Center, or need urgent assistance outside the ticketing system, contact the PARAM Rudra team at <rudrasupport@cdac.in>

## system Usage Policy

Please read and follow them:

!!! Data retention
    
    Regularly back up your data in the /home and /scratch directories.
    Files in /scratch that are not accessed for one week are permanently deleted.


!!! Login node rules
    Do not run jobs on the login node — doing so results in termination without prior notice.
    Use the srun command on login06, login07, or login08 for interactive/test runs.
    Internet access is available only on login01, login02, and login03.



# Closing Your Account on PARAM Rudra

When once you have completed your research work and you no longer need to use PARAM Rudra, you may please close your account on PARAM Rudra. Please raise a ticket by following the URL <https://support.paramrudra.cdacb.in> or you can reach us via e-mail at <rudrasupport@cdac.in> The system administrator will guide you about the “Closure Procedure”. You will need clearance from your project-coordinator/ Supervisor/ Head of the Department about you having surrendered this resource for getting “no dues” certificate from the institute. 



## Frequently asked questions

??? question "My job was killed and I saw a warning about the login node. Why?"
    Compute-heavy processes are not allowed on login nodes and are terminated
    without notice. Submit through SLURM (`sbatch`/`salloc`/`srun`). See
    [Policies](policies.md) and the [Batch System](batch.md).

??? question "My files in /scratch disappeared."
    `/scratch` is purged: files **not accessed in 1 week** are permanently
    deleted, with no recovery. Move important data off the system promptly. See
    [Data Management](data.md).

??? question "Why is my job stuck in PENDING?"
    Run `squeue -j <jobid> -o "%.18i %.9P %.8T %.10M %R"` and read the **Reason**:
    `Priority`/`Resources` means you're waiting your turn; `PartitionTimeLimit`
    means your walltime exceeds the partition max; `AssocMaxJobsLimit` means too
    many of your jobs are queued; `ReqNodeNotAvail` means requested nodes are
    down/drained. See [Monitoring](batch.md#monitoring-and-control).

??? question "`sbatch` rejects my job about the account."
    Every job needs a valid `#SBATCH -A <account>`. List yours with
    `sacctmgr show assoc user=$USER format=account,partition -p`. See
    [Your account](batch.md#your-account--a-is-mandatory).
  

??? question "How do I know how many cores / how much memory a node has?"
    Confirm on an allocated node (not the login node): `lscpu`, `nproc`,
    `free -h`, `numactl --hardware`. See
    [Per-node hardware](configuration.md#node-types-and-per-node-hardware).

??? question "Which GPU do the nodes have, and what `sm_XX` should I compile for?"
    Inside a GPU job run
    `nvidia-smi --query-gpu=name,compute_cap --format=csv` and use the reported
    compute capability as `nvcc -arch=sm_XX`. See [GPU Computing](gpu.md).

??? question "`module load X` says not found."
    Search first: `module avail 2>&1 | grep -i x` or (Lmod)
    `module spider x`. Module output goes to **stderr**, so remember the `2>&1`.
    See [Software Modules](modules.md).

??? question "`scp` fails but `ssh` works (or vice-versa)."
    Port flags differ: `ssh`/`rsync -e ssh` use lowercase `-p 4422`; `scp`/`sftp`
    use uppercase `-P 4422`. See [Getting Access](access.md#copying-files-in-and-out).

??? question "My CUDA program says 'no kernel image is available for execution'."
    The binary was built for a different GPU architecture than the one it ran on.
    Rebuild with the correct `-arch=sm_XX` for the GPU reported by `nvidia-smi`,
    and make sure your framework's CUDA version matches the driver. See
    [Building CUDA](building.md#cuda-gpu-builds).

??? question "Conda is slow / filled my home quota."
    Don't install into `base`; create named envs and consider placing large ones
    under `/scratch`. Check usage with `du -sh ~/.conda`. See
    [Environment](environment.md#conda-and-python).

## Contributing to this manual

This guide is open and community-maintained. Spot something outdated or wrong?
See the **[Contributing](contributing.md)** page for stepwise instructions.

- Quickest way: click the ✏️ **edit** link (top right of any page) → propose a
  change → it becomes a reviewed pull request.
- Or open an issue in the
  [project's GitHub repository](https://github.com/samcom12/paramrudra-user-manual).

!!! warning "Never include secrets in a contribution"
    Do not paste passwords, SSH private keys, or access tokens into issues, pull
    requests, or documentation. If a credential is ever exposed, **rotate it
    immediately**.
