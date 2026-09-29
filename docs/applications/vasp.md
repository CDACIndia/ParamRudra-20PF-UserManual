# VASP (Vienna Ab initio Simulation Package)

VASP is a widely used first-principles simulation software for electronic-structure calculations and materials modeling. It is commonly used for density functional theory (DFT), molecular dynamics, and calculation of structural, electronic, and magnetic properties of materials.

Official Website: <https://vasp.at/>

VASP Forum: <https://vasp.at/forum/>

```
#!/bin/bash
#SBATCH -N 4
#SBATCH --job-name=VASP
#SBATCH --time=72:00:00
#SBATCH --ntasks-per-node=48
#SBATCH --output=vasp.%J.out
#SBATCH --error=vasp.%J.err
#SBATCH --partition=debug
#SBATCH --mem=180G
#SBATCH --exclusive

source /home/apps/intel/oneapi/setvars.sh

ulimit -s unlimited
export OMP_NUM_THREADS=1

export PATH=$PATH:/home/nsmext/$USER/vasp/vasp-{version}/bin

cd $SLURM_SUBMIT_DIR

env > environment.log
time mpiexec.hydra -np $SLURM_NTASKS vasp_ncl
```