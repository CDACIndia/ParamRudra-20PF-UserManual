
# Table of Contents

1.  [1. Overview](#orgb30171a)
2.  [2. References](#org35cebe2)
3.  [3. PARAM Rudra Environment](#org4e051ff)
4.  [4. Knit Installation on PARAM Rudra](#org7ae2976)
5.  [5. Experiment Directory](#orgb4b856a)
6.  [6. Directory Structure](#org766a084)
7.  [7. Knit Experiment Structure](#org3caabe1)
8.  [8. Bootstrap](#org30122c3)
9.  [9. Machine Profile](#org793486a)
10. [10. Slurm Configuration](#org38cd2dd)
11. [11. Important Slurm Options](#org59a7e72)
12. [12. Hello World Through Slurm](#org4885742)
13. [13. Checking Jobs](#orgd300bc5)
14. [14. Knit Job Records](#org53c76cf)
15. [15. Important Path Rule](#org6026f96)
16. [16. LAMMPS Experiment](#orga043c5e)
17. [17. LAMMPS Multi-Node Submission](#org00920fe)
18. [18. GROMACS Experiment](#org5d73bf0)
19. [19. GROMACS Input Generation](#orgf7f2afe)
20. [20. GROMACS MPI Execution](#org48c1bfd)
21. [21. GROMACS Multi-Node Submission](#org1927583)
22. [22. Experiment Timing](#org1b15bd7)
23. [23. Recommended Experiment Workflow](#orgdc61ec8)
24. [24. Basic Command Reference](#org885ccfa)
25. [25. Reusing the Experiment Template](#orgc77ca1e)
26. [26. Knit — Important Commands for PARAM Rudra](#org6e4169c)
    1.  [Help and Information](#org23c7300)
    2.  [Bootstrap](#org92c62a0)
    3.  [Submit Jobs](#org4461546)
    4.  [Save the Knit Job ID](#orgf5c1adc)
    5.  [Job Status](#org90f6ccb)
    6.  [Wait for a Job](#org4ab0903)
    7.  [Show Job Information](#org840dd7a)
    8.  [Read Job Output](#orge5a833e)
    9.  [Complete Job Debug Workflow](#orgb092dac)
    10. [Knit Job Directory](#org11fbbe8)
    11. [Inspect Generated Slurm Script](#org20950a5)
    12. [Slurm Commands](#org544a72f)
    13. [Slurm Variables Inside a Job](#org70e9a4f)
    14. [Knit Variables Inside a Job](#orgeaac538)
    15. [Query Knit Information](#orgf26812a)
    16. [Query Knit Database](#org189d0da)
    17. [MPI Experiments](#org0bcf147)
    18. [MPI Job Inspection](#org28ebca5)
    19. [Logging and Debugging](#org2300bd5)
    20. [Quick Reference](#orgd2c5588)
27. [27. How to write experiment script](#org3053657)
    1.  [1. Experiment Script Structure](#orga52edae)
    2.  [2. Load Knit](#org26da16c)
    3.  [3. Use Absolute Paths](#org3106f81)
    4.  [4. Load Application Environment](#org474de28)
    5.  [5. Define the Application](#orgc4261c2)
    6.  [6. Define a Knit Job](#org75c4dd3)
    7.  [7. Access Slurm Information](#org7748460)
    8.  [8. Running MPI Applications](#orgf930e53)
    9.  [9. GROMACS Example](#org4ffe48b)
    10. [10. Check Command Success](#org8574582)
    11. [11. Measure Execution Time](#orgdb41f06)
    12. [12. Finish the Experiment Script](#org8a1ef9c)
    13. [13. Recommended Experiment Layout](#org4bde840)
    14. [14. Important Rules](#orgb257d77)
28. [28. Scripts](#org7577557)
    1.  [hello.sh](#orga7d7671)
    2.  [lammps\_cpu\_exp.sh](#org734d1ba)
    3.  [gromacs\_cpu\_exp.sh](#orgde5b438)



<a id="orgb30171a"></a>

# 1. Overview

This case study demonstrates the use of **Knit** for running reproducible HPC
experiments on the **PARAM Rudra 20 PF** supercomputer.

Knit is a Bash framework for writing reproducible and portable HPC experiments.
It provides:

-   Experiment scripts written in Bash
-   Experiment bootstrap and initialization
-   Machine profiles
-   Slurm job submission
-   MPI execution
-   Job and output logging
-   Experiment provenance
-   Separate records for jobs and executions

The experiments demonstrated in this case study are:

1.  Hello World executed through Slurm
2.  LAMMPS MPI experiment
3.  GROMACS MPI experiment


<a id="org35cebe2"></a>

# 2. References

Knit documentation and source:

-   Knit project: <https://knit.sh>
-   Knit documentation: <https://knit.sh/docs/>
-   Knit GitHub repository: <https://github.com/knit-sh/knit>

For the complete Knit documentation, refer to the official Knit documentation
and repository.


<a id="org4e051ff"></a>

# 3. PARAM Rudra Environment

The experiments were performed on PARAM Rudra using the Slurm scheduler.

Basic environment:

-   Cluster: PARAM Rudra 20 PF
-   Scheduler: Slurm
-   MPI: Intel MPI
-   MPI launcher: mpirun
-   Compute nodes: 48 CPU cores per node
-   Login node: login03
-   Example compute node: cbhm0027
-   Default test partition: debug

The Slurm environment is available automatically inside submitted jobs.

Useful variables include:

    SLURM_JOB_ID
    SLURM_JOB_NAME
    SLURM_JOB_PARTITION
    SLURM_JOB_NUM_NODES
    SLURM_JOB_NODELIST
    SLURM_NTASKS
    SLURM_NTASKS_PER_NODE
    SLURM_CPUS_ON_NODE

Allocated nodes can be displayed using:

    scontrol show hostnames "${SLURM_JOB_NODELIST}"


<a id="org7ae2976"></a>

# 4. Knit Installation on PARAM Rudra

Knit has already been installed centrally on PARAM Rudra and is available
through an environment module.

There is no need to download or build Knit manually.

Load the Knit module:

    module load knit/v0.1.0

Check the environment:

    echo $KNIT_HOME

The module provides the centrally installed Knit environment.

The Knit installation is located under the central software installation and
should normally be accessed through the module rather than using a hard-coded
installation path.


<a id="orgb4b856a"></a>

# 5. Experiment Directory

A reusable Knit experiment directory has been prepared at:

    /home/apps/knit

This directory can be copied into a user&rsquo;s home or project directory and then
modified according to the experiment.

Example:

    cp -r /home/apps/knit ~/knit
    cd ~/knit

After copying the directory, update experiment-specific paths such as:

-   Executable paths
-   Input files
-   Software environment scripts
-   Application directories
-   MPI configuration

Do not modify the centrally installed Knit module.


<a id="org766a084"></a>

# 6. Directory Structure

A typical experiment directory contains:

    knit/
    ├── lammps_cpu_exp.sh
    ├── gromacs_cpu_exp.sh
    ├── profiles/
    ├── setups/
    ├── jobs/
    ├── loadGromacs.sh
    ├── loadLammps.sh
    └── application input files

The exact files can be changed depending on the application being tested.


<a id="org3caabe1"></a>

# 7. Knit Experiment Structure

A basic Knit experiment follows this structure:

    #!/bin/bash
    
    source /home/apps/software/knit/knit.sh
    
    knit_set_program_description \
        "Description of the experiment."
    
    @job "job-name" "Description of the job."
    
    job_function()
    {
        # experiment commands
    }
    
    @done
    
    knit "$@"

The important parts are:

-   Source Knit
-   Define the program description
-   Register an experiment/job
-   Implement the experiment function
-   Finish the registration with `@done`
-   Pass command-line arguments to `knit`


<a id="org30122c3"></a>

# 8. Bootstrap

Bootstrap initializes a Knit experiment.

From the experiment directory:

    ./experiment.sh bootstrap \
        --profile ./profiles/cdac/rudra_20pf.json \
        --scheduler slurm \
        --launcher slurm

The profile defines the PARAM Rudra 20 PF environment, including the
Slurm scheduler, launcher and hardware configuration.

To check the configured profile:

    ./experiment.sh profile show

After bootstrap, the experiment can be used for jobs, setups and executions.

Check the available commands:

    ./experiment.sh --help

Individual command help:

    ./experiment.sh submit --help
    ./experiment.sh bootstrap --help
    ./experiment.sh profile --help


<a id="org793486a"></a>

# 9. Machine Profile

A Knit profile describes the machine environment used by the experiment.

The profile can contain information about:

-   Scheduler
-   Scheduler command
-   Default queue
-   Queue limits
-   MPI launcher
-   CPU count
-   GPU count
-   Modules
-   External software

For Rudra, the important scheduler configuration is Slurm.

A profile allows Knit to know that jobs should be submitted through Slurm
instead of being executed as ordinary local processes.

Profiles can be inspected using:

    ./experiment.sh profile list
    ./experiment.sh profile show <profile-name>

If a custom Rudra profile is used during bootstrap:

    ./experiment.sh bootstrap --profile <rudra-profile>


<a id="org38cd2dd"></a>

# 10. Slurm Configuration

The Rudra experiment uses Slurm for batch submission.

The important Slurm parameters used in the experiments are:

    --queue
    --nodes
    --walltime
    --name
    --job-name

For example:

    ./lammps_cpu_exp.sh submit \
        --name lammps-multinode \
        --job-name knit-lammps-multi \
        --queue debug \
        --nodes 4 \
        --walltime 01:00:00 \
        -- lammps-multinode

Here:

-   `--name` is the Knit experiment/job name.
-   `--job-name` is the Slurm job name.
-   `--queue` selects the Slurm partition.
-   `--nodes` requests the number of compute nodes.
-   `--walltime` requests the maximum execution time.
-   The final argument after `--` is the Knit job command.


<a id="org59a7e72"></a>

# 11. Important Slurm Options

Additional Slurm resources can be requested when supported by the Knit
scheduler interface and Rudra configuration.

Examples include:

    --nodes
    --ntasks
    --ntasks-per-node
    --cpus-per-task
    --mem
    --partition
    --account
    --time

For example, a memory request can be represented by:

    --mem 180G

The exact resource options available to Knit depend on the scheduler/profile
configuration being used.


<a id="org4885742"></a>

# 12. Hello World Through Slurm

The next step is to execute the experiment on a compute node through Slurm.

Example:

    ./hello.sh submit \
        --name hello-slurm \
        --job-name knit-hello \
        --queue debug \
        --nodes 1 \
        --walltime 00:10:00 \
        -- hello

This submits the experiment to Slurm instead of executing it directly on the
login node.


<a id="orgd300bc5"></a>

# 13. Checking Jobs

After submission, inspect the Slurm job:

    squeue

For a specific job:

    squeue -j <SLURM_JOB_ID>

Detailed information:

    scontrol show job <SLURM_JOB_ID>

Cancel a job if required:

    scancel <SLURM_JOB_ID>


<a id="org53c76cf"></a>

# 14. Knit Job Records

Knit creates a separate directory for each recorded job.

Typical structure:

    jobs/
    └── <job-id>/
        ├── .stdout
        ├── .stderr
        └── ...

List recorded jobs:

    ls jobs/

View standard output:

    cat jobs/<job-id>/.stdout

View standard error:

    cat jobs/<job-id>/.stderr

The job directory is important because Knit changes the working directory while
executing an experiment.

Therefore application files should not normally be referenced using relative
paths based on the original shell directory.


<a id="org6026f96"></a>

# 15. Important Path Rule

Knit changes the working directory to the individual job directory:

    jobs/<job-id>/

Therefore this is unsafe:

    INPUT="$PWD/in.lj.txt"

Instead define the real experiment directory explicitly:

    EXPERIMENT_DIR="/path/to/experiment"
    INPUT="${EXPERIMENT_DIR}/in.lj.txt"

For applications with multiple input files, change to the application directory
before running the application:

    cd "${EXPERIMENT_DIR}" || return 1

This was required for the LAMMPS and GROMACS experiments.


<a id="orga043c5e"></a>

# 16. LAMMPS Experiment

The LAMMPS experiment uses the installed LAMMPS MPI executable.

The experiment defines:

    EXPERIMENT_DIR="/path/to/lammps/experiment"
    
    LAMMPS_EXECUTABLE="/home/apps/spack/opt/spack/linux-cascadelake/lammps-20250722.3-4dwl4bkyshwzbrou5ylumnf5mmu7dl3t/bin/lmp"

The LAMMPS environment is loaded separately:

    source "/home/apps/knit/loadLammps.sh"

The experiment changes into the experiment directory before execution:

    cd "${EXPERIMENT_DIR}" || return 1

LAMMPS is then launched using MPI:

    mpirun \
        -np "${SLURM_NTASKS}" \
        "${LAMMPS_EXECUTABLE}" \
        -in in.lj.txt


<a id="org00920fe"></a>

# 17. LAMMPS Multi-Node Submission

Example:

    ./lammps_cpu_exp.sh submit \
        --name lammps-multinode \
        --job-name knit-lammps-multi \
        --queue debug \
        --nodes 4 \
        --walltime 01:00:00 \
        -- lammps-multinode

The requested number of MPI ranks is obtained from:

    SLURM_NTASKS

The allocated nodes can be displayed using:

    scontrol show hostnames "${SLURM_JOB_NODELIST}"


<a id="org5d73bf0"></a>

# 18. GROMACS Experiment

The GROMACS experiment uses the MPI version of GROMACS.

The experiment defines:

    EXPERIMENT_DIR="/path/to/water-cut1.0_GMX50_bare/3072"
    
    GMX_EXECUTABLE="/home/apps/spack/opt/spack/linux-cascadelake/gromacs-2026.1-pvlg3o7p2vjfv6v6rfwnk6pvbsnsr2n4/bin/gmx_mpi"

The GROMACS environment is loaded using:

    source "/home/apps/knit/loadGromacs.sh"

The experiment then changes to the benchmark directory:

    cd "${EXPERIMENT_DIR}" || return 1


<a id="orgf7f2afe"></a>

# 19. GROMACS Input Generation

The GROMACS input file is generated using:

    "${GMX_EXECUTABLE}" grompp \
        -f pme.mdp \
        -c conf.gro \
        -p topol.top \
        -o water_pme.tpr

The required files are:

    pme.mdp
    conf.gro
    topol.top


<a id="org48c1bfd"></a>

# 20. GROMACS MPI Execution

The simulation is executed using:

    mpirun \
        -np "${SLURM_NTASKS}" \
        "${GMX_EXECUTABLE}" \
        mdrun \
        -nsteps 5000 \
        -s water_pme.tpr

For the single-node test, the experiment can use 48 MPI ranks:

    mpirun \
        -np 48 \
        "${GMX_EXECUTABLE}" \
        mdrun \
        -nsteps 5000 \
        -s water_pme.tpr


<a id="org1927583"></a>

# 21. GROMACS Multi-Node Submission

Example:

    ./gromacs_cpu_exp.sh submit \
        --name gromacs-multinode \
        --job-name knit-gromacs-multi \
        --queue debug \
        --nodes 4 \
        --walltime 01:00:00 \
        -- gromacs-multinode

The multi-node experiment uses the Slurm allocation:

    mpirun \
        -np "${SLURM_NTASKS}" \
        "${GMX_EXECUTABLE}" \
        mdrun \
        -nsteps 5000 \
        -s water_pme.tpr


<a id="org1b15bd7"></a>

# 22. Experiment Timing

The experiments record simple wall-clock timing using Bash:

    START_TIME=$(date +%s)
    
    # application
    
    END_TIME=$(date +%s)
    WALL_TIME=$((END_TIME - START_TIME))
    
    echo "Wall time : ${WALL_TIME} seconds"

This provides an application-level execution time in the job output.


<a id="orgdc61ec8"></a>

# 23. Recommended Experiment Workflow

The complete workflow used on Rudra is:

    Load Knit module
          |
          v
    Copy experiment directory
          |
          v
    Bootstrap
          |
          v
    Configure/select Rudra profile
          |
          v
    Submit Hello World through Slurm
          |
          v
    Run LAMMPS
          |
          v
    Run GROMACS
          |
          v
    Inspect jobs and output


<a id="org885ccfa"></a>

# 24. Basic Command Reference

    # Load Knit
    module load knit/v0.1.0
    
    # Check Knit
    echo $KNIT_HOME
    
    # Bootstrap
    ./experiment.sh bootstrap
    
    # General help
    ./experiment.sh --help
    
    # Command help
    ./experiment.sh submit --help
    
    # Submit a job
    ./experiment.sh submit --name <name> \
        --job-name <slurm-name> \
        --queue debug \
        --nodes 1 \
        --walltime 01:00:00 \
        -- <experiment-command>
    
    # Check Slurm jobs
    squeue
    
    # Detailed job information
    scontrol show job <job-id>
    
    # Show allocated nodes
    scontrol show hostnames <node-list>
    
    # Cancel a job
    scancel <job-id>
    
    # View Knit stdout
    cat jobs/<job-id>/.stdout
    
    # View Knit stderr
    cat jobs/<job-id>/.stderr
    
    # List recorded jobs
    ls jobs/


<a id="orgc77ca1e"></a>

# 25. Reusing the Experiment Template

The prepared experiment directory under:

    /home/apps/knit

can be copied and adapted for new HPC applications.

For a new application, the main items that normally need to be changed are:

1.  `EXPERIMENT_DIR`
2.  Application executable path
3.  Application environment/module script
4.  Input files
5.  MPI command
6.  Number of MPI ranks
7.  Slurm submission parameters
8.  Experiment/job name

The Knit framework itself does not need to be modified.


<a id="org6e4169c"></a>

# 26. Knit — Important Commands for PARAM Rudra


<a id="org23c7300"></a>

## Help and Information

    ./experiment.sh --help
    ./experiment.sh submit --help
    ./experiment.sh run --help
    
    ./experiment.sh describe
    ./experiment.sh describe --format json
    ./experiment.sh describe --format markdown


<a id="org92c62a0"></a>

## Bootstrap

    ./experiment.sh bootstrap
    
    ./experiment.sh bootstrap --default-queue debug
    
    ./experiment.sh bootstrap --default-walltime 01:00:00
    
    ./experiment.sh bootstrap --account cdac


<a id="org4461546"></a>

## Submit Jobs

1.  Basic submission

        ./experiment.sh submit -- pi-test

2.  Submit and wait

        ./experiment.sh submit --wait -- pi-test

3.  Select Slurm partition

        ./experiment.sh submit \
            --queue debug \
            -- pi-test

4.  Request multiple nodes

        ./experiment.sh submit \
            --nodes 2 \
            --queue debug \
            -- pi-multinode

5.  Set walltime

        ./experiment.sh submit \
            --walltime 01:00:00 \
            -- pi-test

6.  Complete submission

        ./experiment.sh submit \
            --name pi-single \
            --job-name knit-pi-single \
            --queue debug \
            --nodes 1 \
            --walltime 00:05:00 \
            --wait \
            -- pi-test


<a id="orgf5c1adc"></a>

## Save the Knit Job ID

    id=$(./experiment.sh submit -- pi-test)
    
    echo "$id"

Check the job:

    ./experiment.sh job status --id "$id"


<a id="org90f6ccb"></a>

## Job Status

    ./experiment.sh job status --id "$id"


<a id="org4ab0903"></a>

## Wait for a Job

    ./experiment.sh job wait --id "$id"


<a id="org840dd7a"></a>

## Show Job Information

    ./experiment.sh job show --id "$id"
    
    ./experiment.sh job show --id "$id" --json


<a id="orge5a833e"></a>

## Read Job Output

1.  Standard output

        ./experiment.sh job show:stdout --id "$id"

2.  Standard error

        ./experiment.sh job show:stderr --id "$id"

3.  Generated Slurm script

        ./experiment.sh job show:script --id "$id"


<a id="orgb092dac"></a>

## Complete Job Debug Workflow

    id=$(./experiment.sh submit \
        --name debug-run \
        --job-name knit-debug \
        --queue debug \
        --nodes 1 \
        --walltime 00:05:00 \
        -- pi-test)
    
    echo "Knit Job ID: $id"
    
    ./experiment.sh job status --id "$id"
    
    ./experiment.sh job wait --id "$id"
    
    ./experiment.sh job status --id "$id"
    
    ./experiment.sh job show --id "$id"
    
    ./experiment.sh job show:stdout --id "$id"
    
    ./experiment.sh job show:stderr --id "$id"
    
    ./experiment.sh job show:script --id "$id"


<a id="org11fbbe8"></a>

## Knit Job Directory

List all jobs:

    ls -lah jobs/

Enter a job directory:

    cd jobs/<JOB-ID>

Inspect its files:

    ls -lah
    
    cat .submit
    cat .job.id
    cat .job.sh
    cat .stdout
    cat .stderr


<a id="org20950a5"></a>

## Inspect Generated Slurm Script

    cat .job.sh

Show only Slurm directives:

    grep '^#SBATCH' .job.sh

Find the command executed by Knit:

    grep 'experiment' .job.sh

Show the Slurm job ID:

    cat .job.id


<a id="org544a72f"></a>

## Slurm Commands

1.  Show my jobs

        squeue -u "$USER"

2.  Show all jobs

        squeue

3.  Detailed job information

        scontrol show job <SLURM_JOB_ID>

4.  Show allocated hostnames

        scontrol show hostnames <SLURM_NODELIST>

5.  Job accounting

        sacct -j <SLURM_JOB_ID>

6.  Detailed accounting

        sacct -j <SLURM_JOB_ID> \
            --format=JobID,JobName,State,Elapsed,AllocNodes,AllocCPUS,NodeList


<a id="org70e9a4f"></a>

## Slurm Variables Inside a Job

    echo "$SLURM_JOB_ID"
    echo "$SLURM_JOB_NAME"
    echo "$SLURM_JOB_PARTITION"
    echo "$SLURM_JOB_NUM_NODES"
    echo "$SLURM_JOB_NODELIST"
    echo "$SLURM_NTASKS"
    echo "$SLURM_CPUS_ON_NODE"
    echo "$SLURM_NTASKS_PER_NODE"
    echo "$SLURM_NNODES"

Show allocated nodes:

    scontrol show hostnames "$SLURM_JOB_NODELIST"


<a id="orgeaac538"></a>

## Knit Variables Inside a Job

    echo "$KNIT_JOB_PREFIX"
    echo "$KNIT_SOURCE_ID"
    echo "$KNIT_SOURCE_COMMAND"
    echo "$KNIT_SETUP_PREFIX"
    echo "$_KNIT_PREFIX"


<a id="orgf26812a"></a>

## Query Knit Information

    ./experiment.sh query catalog
    
    ./experiment.sh query catalog --ref jobs
    
    ./experiment.sh query catalog --ref jobs.state


<a id="org189d0da"></a>

## Query Knit Database

    ./experiment.sh query sql \
        --exec "SELECT * FROM jobs"
    
    ./experiment.sh query sql \
        --exec "SELECT id,state FROM jobs"


<a id="org0bcf147"></a>

## MPI Experiments

1.  Single-node MPI

        ./experiment.sh submit \
            --name pi-single \
            --job-name knit-pi-single \
            --queue debug \
            --nodes 1 \
            --walltime 00:05:00 \
            --wait \
            -- pi-test

2.  Multi-node MPI

        ./experiment.sh submit \
            --name pi-multinode \
            --job-name knit-pi-multinode \
            --queue debug \
            --nodes 2 \
            --walltime 01:00:00 \
            --wait \
            -- pi-multinode


<a id="org28ebca5"></a>

## MPI Job Inspection

    id=$(./experiment.sh submit \
        --name pi-multinode \
        --job-name knit-pi-multinode \
        --queue debug \
        --nodes 2 \
        --walltime 01:00:00 \
        -- pi-multinode)
    
    echo "Knit Job ID: $id"
    
    ./experiment.sh job status --id "$id"
    
    ./experiment.sh job wait --id "$id"
    
    ./experiment.sh job status --id "$id"
    
    ./experiment.sh job show:stdout --id "$id"
    
    ./experiment.sh job show:stderr --id "$id"
    
    ./experiment.sh job show:script --id "$id"


<a id="org2300bd5"></a>

## Logging and Debugging

    KNIT_LOG_LEVEL=trace ./experiment.sh pi-test
    
    KNIT_LOG_LEVEL=debug ./experiment.sh pi-test
    
    KNIT_LOG_LEVEL=info ./experiment.sh pi-test
    
    KNIT_LOG_LEVEL=warning ./experiment.sh pi-test
    
    KNIT_LOG_LEVEL=error ./experiment.sh pi-test


<a id="orgd2c5588"></a>

## Quick Reference

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left">Command</th>
<th scope="col" class="org-left">Purpose</th>
</tr>
</thead>
<tbody>
<tr>
<td class="org-left"><code>./experiment.sh --help</code></td>
<td class="org-left">Show help</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh describe</code></td>
<td class="org-left">Show experiment commands</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh bootstrap</code></td>
<td class="org-left">Initialize Knit</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh submit -- pi-test</code></td>
<td class="org-left">Submit a job</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh submit --wait -- pi-test</code></td>
<td class="org-left">Submit and wait</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job status --id ID</code></td>
<td class="org-left">Check job status</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job wait --id ID</code></td>
<td class="org-left">Wait for job</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job show --id ID</code></td>
<td class="org-left">Show job information</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job show:stdout --id ID</code></td>
<td class="org-left">Read stdout</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job show:stderr --id ID</code></td>
<td class="org-left">Read stderr</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh job show:script --id ID</code></td>
<td class="org-left">Read generated Slurm script</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh query catalog</code></td>
<td class="org-left">Inspect Knit database</td>
</tr>

<tr>
<td class="org-left"><code>./experiment.sh query sql --exec "..."</code></td>
<td class="org-left">Query Knit database</td>
</tr>

<tr>
<td class="org-left"><code>squeue -u "$USER"</code></td>
<td class="org-left">Check Slurm jobs</td>
</tr>

<tr>
<td class="org-left"><code>scontrol show job ID</code></td>
<td class="org-left">Inspect Slurm job</td>
</tr>

<tr>
<td class="org-left"><code>sacct -j ID</code></td>
<td class="org-left">Check completed job</td>
</tr>

<tr>
<td class="org-left"><code>ls jobs/</code></td>
<td class="org-left">List Knit job records</td>
</tr>
</tbody>
</table>


<a id="org3053657"></a>

# 27. How to write experiment script


<a id="orga52edae"></a>

## 1. Experiment Script Structure

A Knit experiment script generally contains:

1.  Load Knit
2.  Define experiment/application paths
3.  Load required software environment
4.  Define executable and input paths
5.  Define common helper functions
6.  Create a Knit job
7.  Add the application commands
8.  Call the Knit CLI

The basic structure is:

    #!/bin/bash
    
    source /home/apps/softwares/knit/knit.sh
    
    knit_set_program_description \
        "Description of the experiment."
    
    # Configuration
    EXPERIMENT_DIR="/absolute/path/to/experiment"
    MPI_EXECUTABLE="/absolute/path/to/application"
    
    # Environment
    source "/absolute/path/to/loadEnv.sh"
    
    # Job
    @job "experiment-name" "Experiment description."
    
    experiment_function()
    {
        # Commands go here
    }
    
    @done
    
    knit "$@"


<a id="org26da16c"></a>

## 2. Load Knit

Always load Knit at the beginning:

    source /home/apps/softwares/knit/knit.sh

Set a description for the experiment:

    knit_set_program_description \
        "PARAM Rudra MPI experiment."


<a id="org3106f81"></a>

## 3. Use Absolute Paths

Knit changes the working directory to the generated job directory:

    jobs/<job-id>/

Therefore, do NOT use:

    $PWD
    ./codes/application
    ./input

Instead use the actual experiment directory:

    EXPERIMENT_DIR="/home/nsmapplication/cdacapp01/rAbhishek/test/my-experiment"

Then explicitly enter it before running the application:

    cd "${EXPERIMENT_DIR}" || return 1


<a id="org474de28"></a>

## 4. Load Application Environment

If the application requires a custom environment, load it using an absolute path.

Example:

    source "/home/nsmapplication/cdacapp01/rAbhishek/test/loadGromacs.sh"

For example, the environment script may configure:

-   GROMACS
-   Intel MPI
-   CUDA
-   Spack
-   Required libraries


<a id="orgc4261c2"></a>

## 5. Define the Application

Define the actual executable using an absolute path:

    MPI_EXECUTABLE="/home/apps/spack/opt/spack/linux-cascadelake/gromacs-2026.1-*/bin/gmx_mpi"

Check that it exists:

    if [[ ! -f "${MPI_EXECUTABLE}" ]]; then
        echo "ERROR: executable does not exist"
        return 1
    fi
    
    if [[ ! -x "${MPI_EXECUTABLE}" ]]; then
        echo "ERROR: executable is not executable"
        return 1
    fi


<a id="org75c4dd3"></a>

## 6. Define a Knit Job

Create a job using:

    @job "job-name" "Job description."
    
    job_function()
    {
        # experiment commands
    }
    
    @done

Example:

    @job "gromacs-test" "Run GROMACS on one Rudra node."
    
    gromacs_test()
    {
        echo "Running GROMACS"
    }
    
    @done


<a id="org7748460"></a>

## 7. Access Slurm Information

Inside the job, Slurm variables are automatically available.

Useful variables:

    echo "Job ID     : ${SLURM_JOB_ID}"
    echo "Job Name   : ${SLURM_JOB_NAME}"
    echo "Partition  : ${SLURM_JOB_PARTITION}"
    echo "Nodes      : ${SLURM_JOB_NUM_NODES}"
    echo "NodeList   : ${SLURM_JOB_NODELIST}"
    echo "Tasks      : ${SLURM_NTASKS}"
    echo "Tasks/node : ${SLURM_NTASKS_PER_NODE}"
    echo "CPUs/node  : ${SLURM_CPUS_ON_NODE}"

To display allocated nodes:

    scontrol show hostnames "${SLURM_JOB_NODELIST}"


<a id="orgf930e53"></a>

## 8. Running MPI Applications

For a fixed number of MPI ranks:

    mpirun \
        -np 48 \
        "${MPI_EXECUTABLE}" \
        application_arguments

For multi-node execution using all allocated Slurm tasks:

    mpirun \
        -np "${SLURM_NTASKS}" \
        "${MPI_EXECUTABLE}" \
        application_arguments

For Intel MPI on Rudra, Slurm bootstrap can be explicitly specified when required:

    mpirun \
        -bootstrap slurm \
        -np "${SLURM_NTASKS}" \
        "${MPI_EXECUTABLE}"


<a id="org4ffe48b"></a>

## 9. GROMACS Example

For the GROMACS experiment, first enter the benchmark directory:

    cd "${EXPERIMENT_DIR}" || return 1

Generate the GROMACS input:

    "${MPI_EXECUTABLE}" grompp \
        -f pme.mdp \
        -c conf.gro \
        -p topol.top \
        -o water_pme.tpr

Then run the MPI simulation:

    mpirun \
        -np 48 \
        "${MPI_EXECUTABLE}" \
        mdrun \
        -nsteps 5000 \
        -s water_pme.tpr


<a id="org8574582"></a>

## 10. Check Command Success

Store the return code:

    MPI_RC=$?
    
    if [[ ${MPI_RC} -ne 0 ]]; then
        echo "MPI application failed."
        return ${MPI_RC}
    fi


<a id="orgdb41f06"></a>

## 11. Measure Execution Time

A simple wall-time measurement:

    START_TIME=$(date +%s)
    
    mpirun \
        -np 48 \
        "${MPI_EXECUTABLE}" \
        mdrun \
        -nsteps 5000 \
        -s water_pme.tpr
    
    MPI_RC=$?
    
    END_TIME=$(date +%s)
    WALL_TIME=$((END_TIME - START_TIME))
    
    echo "Exit code : ${MPI_RC}"
    echo "Wall time : ${WALL_TIME} seconds"


<a id="org8a1ef9c"></a>

## 12. Finish the Experiment Script

The final line must invoke the Knit CLI:

    knit "$@"

This allows commands such as:

    ./experiment_gromacs.sh list

and:

    ./experiment_gromacs.sh submit \
        --name gromacs-test \
        --job-name knit-gromacs \
        --queue debug \
        --nodes 1 \
        --walltime 01:00:00 \
        -- gromacs-test


<a id="org4bde840"></a>

## 13. Recommended Experiment Layout

A simple project layout:

    test/
    ├── experiment_gromacs.sh
    ├── knit/
    ├── knit.sh
    ├── loadGromacs.sh
    ├── jobs/
    ├── setups/
    ├── profiles/
    └── water-cut1.0_GMX50_bare/
        └── 3072/
            ├── pme.mdp
            ├── conf.gro
            ├── topol.top
            └── ...


<a id="orgb257d77"></a>

## 14. Important Rules

-   Always source Knit.
-   Use absolute paths for application/input data.
-   Remember that Knit starts jobs inside `jobs/<job-id>/`.
-   `cd` into the real experiment directory before using relative input files.
-   Load the required application environment inside the experiment.
-   Use `mpirun` for MPI applications when using the tested Rudra configuration.
-   Use `SLURM_NTASKS` for the number of MPI ranks in scalable multi-node jobs.
-   Return the application&rsquo;s exit code.
-   Finish the script with `knit "$@"`.

\`\`\`


<a id="org7577557"></a>

# 28. Scripts


<a id="orga7d7671"></a>

## hello.sh

    #!/bin/bash
    
    source /home/apps/softwares/knit/knit.sh
    
    knit_set_program_description \
        "PARAM Rudra 20 PF HPC experiment."
    
    @job "hello" "Run a basic Slurm test on PARAM Rudra."
    hello() {
        echo "========================================"
        echo "       KNIT / PARAM RUDRA TEST"
        echo "========================================"
    
        echo "Hostname : $(hostname)"
        echo "Date     : $(date)"
    
        echo
        echo "SLURM"
        echo "----------------------------------------"
        echo "Job ID       : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name     : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition    : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes        : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList     : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks        : ${SLURM_NTASKS:-not-set}"
        echo "CPUs/node    : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    }
    
    @done
    
    knit "$@"


<a id="org734d1ba"></a>

## lammps\_cpu\_exp.sh

    #!/bin/bash
    
    source /home/apps/softwares/knit/knit.sh
    
    knit_set_program_description \
        "PARAM Rudra 20 PF HPC experiment."
    
    @job "hello" "Run a basic Slurm test on PARAM Rudra."
    hello() {
        echo "========================================"
        echo "       KNIT / PARAM RUDRA TEST"
        echo "========================================"
    
        echo "Hostname : $(hostname)"
        echo "Date     : $(date)"
    
        echo
        echo "SLURM"
        echo "----------------------------------------"
        echo "Job ID       : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name     : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition    : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes        : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList     : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks        : ${SLURM_NTASKS:-not-set}"
        echo "CPUs/node    : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    }
    
    @done
    
    knit "$@"
    
    [cdacapp01@login01 knit]$ cat lammps_cpu_exp.sh 
    #!/bin/bash
    
    source /home/apps/softwares/knit/knit.sh
    
    knit_set_program_description \
        "PARAM Rudra 20 PF LAMMPS MPI experiment using OpenMPI."
    
    
    # ============================================================
    # CONFIGURATION
    # ============================================================
    
    # IMPORTANT:
    #
    # Knit changes the working directory to:
    #
    #   jobs/<job-id>/
    #
    # Therefore NEVER construct paths using $PWD.
    #
    # Always use the real experiment directory.
    
    EXPERIMENT_DIR="/home/apps/knit/"
    
    # Load LAMMPS / MPI environment
    source "/home/apps/knit/loadLammps.sh"
    
    # LAMMPS MPI executable
    LAMMPS_EXECUTABLE="/home/apps/spack/opt/spack/linux-cascadelake/lammps-20250722.3-4dwl4bkyshwzbrou5ylumnf5mmu7dl3t/bin/lmp"
    
    
    # ============================================================
    # COMMON MPI ENVIRONMENT
    # ============================================================
    
    setup_mpi_environment()
    {
        echo
        echo "MPI ENVIRONMENT"
        echo "----------------------------------------"
    
        echo "MPI launcher:"
        which mpirun || true
    
        echo
        echo "MPI version:"
        mpirun --version 2>&1 | head -n 5 || true
    
        echo
        echo "MPI environment:"
        env | grep -E \
            '^(OMPI|PMI|PMIX|SLURM)' \
            | sort \
            || true
    
        echo
        echo "LAMMPS:"
        "${LAMMPS_EXECUTABLE}" -h 2>&1 | head -n 10 || true
    }
    
    
    # ============================================================
    # EXECUTABLE CHECK
    # ============================================================
    
    check_executable()
    {
        echo
        echo "EXECUTABLE CHECK"
        echo "----------------------------------------"
    
        echo "LAMMPS executable:"
        echo "${LAMMPS_EXECUTABLE}"
    
        if [[ ! -f "${LAMMPS_EXECUTABLE}" ]]; then
            echo
            echo "ERROR: LAMMPS executable does not exist:"
            echo "${LAMMPS_EXECUTABLE}"
            return 1
        fi
    
        if [[ ! -x "${LAMMPS_EXECUTABLE}" ]]; then
            echo
            echo "ERROR: LAMMPS executable is not executable:"
            echo "${LAMMPS_EXECUTABLE}"
            return 1
        fi
    
        ls -lh "${LAMMPS_EXECUTABLE}"
    
        echo
        file "${LAMMPS_EXECUTABLE}"
    
        return 0
    }
    
    
    # ============================================================
    # SINGLE NODE LAMMPS
    # ============================================================
    
    @job "lammps-test" "Run LAMMPS using mpirun on one Rudra node."
    
    lammps_test()
    {
        echo "========================================"
        echo "       KNIT LAMMPS - SINGLE NODE"
        echo "========================================"
    
        echo
        echo "SLURM ALLOCATION"
        echo "----------------------------------------"
    
        echo "Job ID        : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name      : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition     : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList      : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks         : ${SLURM_NTASKS:-not-set}"
        echo "Tasks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
        echo "CPUs/node     : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    
        check_executable || return 1
    
        setup_mpi_environment
    
        echo
        echo "LAMMPS EXPERIMENT DIRECTORY"
        echo "----------------------------------------"
    
        echo "${EXPERIMENT_DIR}"
    
        if [[ ! -d "${EXPERIMENT_DIR}" ]]; then
            echo "ERROR: Experiment directory does not exist:"
            echo "${EXPERIMENT_DIR}"
            return 1
        fi
    
        cd "${EXPERIMENT_DIR}" || return 1
    
        echo
        echo "INPUT FILE"
        echo "----------------------------------------"
    
        if [[ ! -f "in.lj.txt" ]]; then
            echo "ERROR: LAMMPS input file does not exist:"
            echo "${EXPERIMENT_DIR}/in.lj.txt"
            return 1
        fi
    
        ls -lh in.lj.txt
    
        echo
        echo "========================================"
        echo "RUNNING LAMMPS"
        echo "========================================"
    
        echo "MPI ranks     : 8"
        echo "Input file    : in.lj.txt"
    
        START_TIME=$(date +%s)
    
        mpirun \
            -np 8 \
            "${LAMMPS_EXECUTABLE}" \
            -in in.lj.txt
    
        MPI_RC=$?
    
        END_TIME=$(date +%s)
        WALL_TIME=$((END_TIME - START_TIME))
    
        echo
        echo "========================================"
        echo "LAMMPS RUN FINISHED"
        echo "========================================"
    
        echo "Exit code     : ${MPI_RC}"
        echo "Wall time     : ${WALL_TIME} seconds"
    
        return "${MPI_RC}"
    }
    
    @done
    
    
    # ============================================================
    # MULTI-NODE LAMMPS
    # ============================================================
    
    @job "lammps-multinode" "Run LAMMPS across multiple Rudra nodes using OpenMPI mpirun."
    
    lammps_multinode()
    {
        echo "========================================"
        echo "       KNIT LAMMPS - MULTI NODE"
        echo "========================================"
    
        echo
        echo "SLURM ALLOCATION"
        echo "----------------------------------------"
    
        echo "Job ID        : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name      : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition     : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList      : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks         : ${SLURM_NTASKS:-not-set}"
        echo "Tasks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
        echo "CPUs/node     : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    
        check_executable || return 1
    
        setup_mpi_environment
    
        echo
        echo "LAMMPS EXPERIMENT DIRECTORY"
        echo "----------------------------------------"
    
        echo "${EXPERIMENT_DIR}"
    
        cd "${EXPERIMENT_DIR}" || return 1
    
        echo
        echo "INPUT FILE"
        echo "----------------------------------------"
    
        if [[ ! -f "in.lj.txt" ]]; then
            echo "ERROR: LAMMPS input file does not exist:"
            echo "${EXPERIMENT_DIR}/in.lj.txt"
            return 1
        fi
    
        ls -lh in.lj.txt
    
        echo
        echo "LAMMPS APPLICATION"
        echo "----------------------------------------"
    
        echo "Application   : LAMMPS"
        echo "Executable    : ${LAMMPS_EXECUTABLE}"
        echo "MPI launcher  : mpirun"
        echo "MPI ranks     : ${SLURM_NTASKS}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES}"
        echo "Ranks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
        echo "Input file    : in.lj.txt"
    
        echo
        echo "MPI HOST LIST"
        echo "----------------------------------------"
    
        HOSTS=$(scontrol show hostnames "${SLURM_JOB_NODELIST}")
    
        echo "${HOSTS}"
    
        echo
        echo "MPI RANK DISTRIBUTION"
        echo "----------------------------------------"
    
        echo "Expected:"
        echo "${SLURM_NTASKS} MPI ranks"
        echo "${SLURM_NTASKS_PER_NODE:-unknown} ranks per node"
    
        echo
        echo "========================================"
        echo "RUNNING MULTI-NODE LAMMPS"
        echo "========================================"
    
        START_TIME=$(date +%s)
    
        mpirun \
            -np "${SLURM_NTASKS}" \
            "${LAMMPS_EXECUTABLE}" \
            -in in.lj.txt
    
        MPI_RC=$?
    
        END_TIME=$(date +%s)
        WALL_TIME=$((END_TIME - START_TIME))
    
        echo
        echo "========================================"
        echo "MULTI-NODE LAMMPS FINISHED"
        echo "========================================"
    
        echo "Exit code     : ${MPI_RC}"
        echo "Wall time     : ${WALL_TIME} seconds"
    
        return "${MPI_RC}"
    }
    
    @done
    
    
    # ============================================================
    # KNIT CLI
    # ============================================================
    
    knit "$@"


<a id="orgde5b438"></a>

## gromacs\_cpu\_exp.sh

    #!/bin/bash
    
    source /home/apps/softwares/knit/knit.sh
    
    knit_set_program_description \
        "PARAM Rudra 20 PF GROMACS MPI experiment using Intel MPI mpirun."
    
    
    # ============================================================
    # CONFIGURATION
    # ============================================================
    
    # Real experiment directory.
    #
    # Knit changes the working directory to:
    #
    #   jobs/<job-id>/
    #
    # Therefore use an absolute path for the application directory.
    
    EXPERIMENT_DIR="/home/apps/knit/water-cut1.0_GMX50_bare/3072"
    
    # Load GROMACS environment
    source "/home/apps/knit/loadGromacs.sh"
    
    # GROMACS MPI executable
    GMX_EXECUTABLE="/home/apps/spack/opt/spack/linux-cascadelake/gromacs-2026.1-pvlg3o7p2vjfv6v6rfwnk6pvbsnsr2n4/bin/gmx_mpi"
    
    
    # ============================================================
    # MPI ENVIRONMENT
    # ============================================================
    
    setup_mpi_environment()
    {
        echo
        echo "MPI ENVIRONMENT"
        echo "----------------------------------------"
    
        echo "MPI launcher:"
        which mpirun || true
    
        echo
        echo "MPI version:"
        mpirun --version 2>&1 | head -n 5 || true
    
        echo
        echo "Intel MPI bootstrap:"
        export I_MPI_HYDRA_BOOTSTRAP=slurm
        echo "I_MPI_HYDRA_BOOTSTRAP=${I_MPI_HYDRA_BOOTSTRAP}"
    
        echo
        echo "Intel MPI fabric:"
        export I_MPI_FABRICS=shm:ofi
        echo "I_MPI_FABRICS=${I_MPI_FABRICS}"
    
        echo
        echo "GROMACS:"
        "${GMX_EXECUTABLE}" --version
    }
    
    
    # ============================================================
    # EXECUTABLE CHECK
    # ============================================================
    
    check_executable()
    {
        echo
        echo "EXECUTABLE CHECK"
        echo "----------------------------------------"
    
        echo "GROMACS executable:"
        echo "${GMX_EXECUTABLE}"
    
        if [[ ! -f "${GMX_EXECUTABLE}" ]]; then
            echo
            echo "ERROR: GROMACS executable does not exist:"
            echo "${GMX_EXECUTABLE}"
            return 1
        fi
    
        if [[ ! -x "${GMX_EXECUTABLE}" ]]; then
            echo
            echo "ERROR: GROMACS executable is not executable:"
            echo "${GMX_EXECUTABLE}"
            return 1
        fi
    
        ls -lh "${GMX_EXECUTABLE}"
    
        echo
        file "${GMX_EXECUTABLE}"
    
        return 0
    }
    
    
    # ============================================================
    # SINGLE NODE GROMACS
    # ============================================================
    
    @job "gromacs-test" "Run GROMACS PME benchmark on one Rudra node."
    
    gromacs_test()
    {
        echo "========================================"
        echo "       KNIT GROMACS - SINGLE NODE"
        echo "========================================"
    
        echo
        echo "SLURM ALLOCATION"
        echo "----------------------------------------"
    
        echo "Job ID        : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name      : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition     : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList      : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks         : ${SLURM_NTASKS:-not-set}"
        echo "Tasks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
        echo "CPUs/node     : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    
        check_executable || return 1
    
        setup_mpi_environment
    
        echo
        echo "GROMACS EXPERIMENT DIRECTORY"
        echo "----------------------------------------"
    
        echo "${EXPERIMENT_DIR}"
    
        if [[ ! -d "${EXPERIMENT_DIR}" ]]; then
            echo "ERROR: Experiment directory does not exist:"
            echo "${EXPERIMENT_DIR}"
            return 1
        fi
    
        cd "${EXPERIMENT_DIR}" || return 1
    
        echo
        echo "INPUT FILES"
        echo "----------------------------------------"
    
        ls -lh pme.mdp conf.gro topol.top
    
        echo
        echo "========================================"
        echo "GENERATING GROMACS INPUT"
        echo "========================================"
    
        "${GMX_EXECUTABLE}" grompp \
            -f pme.mdp \
            -c conf.gro \
            -p topol.top \
            -o water_pme.tpr
    
        GROMPP_RC=$?
    
        if [[ ${GROMPP_RC} -ne 0 ]]; then
            echo
            echo "ERROR: grompp failed."
            return ${GROMPP_RC}
        fi
    
        echo
        echo "========================================"
        echo "RUNNING GROMACS"
        echo "========================================"
    
        START_TIME=$(date +%s)
    
        mpirun \
            -np 48 \
            "${GMX_EXECUTABLE}" \
            mdrun \
            -nsteps 5000 \
            -s water_pme.tpr
    
        MPI_RC=$?
    
        END_TIME=$(date +%s)
        WALL_TIME=$((END_TIME - START_TIME))
    
        echo
        echo "========================================"
        echo "GROMACS RUN FINISHED"
        echo "========================================"
    
        echo "Exit code     : ${MPI_RC}"
        echo "Wall time     : ${WALL_TIME} seconds"
    
        return "${MPI_RC}"
    }
    
    @done
    
    
    # ============================================================
    # MULTI-NODE GROMACS
    # ============================================================
    
    @job "gromacs-multinode" "Run GROMACS PME benchmark across multiple Rudra nodes."
    
    gromacs_multinode()
    {
        echo "========================================"
        echo "       KNIT GROMACS - MULTI NODE"
        echo "========================================"
    
        echo
        echo "SLURM ALLOCATION"
        echo "----------------------------------------"
    
        echo "Job ID        : ${SLURM_JOB_ID:-not-set}"
        echo "Job Name      : ${SLURM_JOB_NAME:-not-set}"
        echo "Partition     : ${SLURM_JOB_PARTITION:-not-set}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES:-not-set}"
        echo "NodeList      : ${SLURM_JOB_NODELIST:-not-set}"
        echo "Tasks         : ${SLURM_NTASKS:-not-set}"
        echo "Tasks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
        echo "CPUs/node     : ${SLURM_CPUS_ON_NODE:-not-set}"
    
        echo
        echo "ALLOCATED HOSTS"
        echo "----------------------------------------"
    
        scontrol show hostnames "${SLURM_JOB_NODELIST}"
    
        check_executable || return 1
    
        setup_mpi_environment
    
        echo
        echo "GROMACS EXPERIMENT DIRECTORY"
        echo "----------------------------------------"
    
        echo "${EXPERIMENT_DIR}"
    
        cd "${EXPERIMENT_DIR}" || return 1
    
        echo
        echo "INPUT FILES"
        echo "----------------------------------------"
    
        ls -lh pme.mdp conf.gro topol.top
    
        echo
        echo "========================================"
        echo "GENERATING GROMACS INPUT"
        echo "========================================"
    
        "${GMX_EXECUTABLE}" grompp \
            -f pme.mdp \
            -c conf.gro \
            -p topol.top \
            -o water_pme.tpr
    
        GROMPP_RC=$?
    
        if [[ ${GROMPP_RC} -ne 0 ]]; then
            echo
            echo "ERROR: grompp failed."
            return ${GROMPP_RC}
        fi
    
        echo
        echo "========================================"
        echo "RUNNING MULTI-NODE GROMACS"
        echo "========================================"
    
        echo "MPI ranks     : ${SLURM_NTASKS}"
        echo "Nodes         : ${SLURM_JOB_NUM_NODES}"
        echo "Ranks/node    : ${SLURM_NTASKS_PER_NODE:-not-set}"
    
        START_TIME=$(date +%s)
    
        mpirun \
            -np "${SLURM_NTASKS}" \
            "${GMX_EXECUTABLE}" \
            mdrun \
            -nsteps 5000 \
            -s water_pme.tpr
    
        MPI_RC=$?
    
        END_TIME=$(date +%s)
        WALL_TIME=$((END_TIME - START_TIME))
    
        echo
        echo "========================================"
        echo "MULTI-NODE GROMACS FINISHED"
        echo "========================================"
    
        echo "Exit code     : ${MPI_RC}"
        echo "Wall time     : ${WALL_TIME} seconds"
    
        return "${MPI_RC}"
    }
    
    @done
    
    
    # ============================================================
    # KNIT CLI
    # ============================================================
    
    knit "$@"

