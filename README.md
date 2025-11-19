# SLURM Job Templates Repository for Tribhuvan University Super Computing Center

This repository provides standardized, reproducible, and well-documented SLURM job templates for a variety of scientific software packages. It is designed for HPC users who need reliable job scripts, helper tools, and examples for running simulations, analyses, and computational workflows.

---

## **1. Overview**
This repository centralizes SLURM job scripts and support files used for scientific computing workloads. Each software package has its own directory containing:
- SLURM job templates
- Wrapper scripts
- Example input files
- Documentation specific to that software

The goal is to ensure reproducibility, portability across HPC systems, and simplify onboarding for new users.

---

## **2. Repository Structure**
```
README.md
LICENSE
CONTRIBUTING.md
CI/
  checks.sh
  lint.yml

templates/
  common_header.sh
  slurm_header.sbatch

softwares/
  fplo/
    README.md
    run_qe.sh
    jobs/
      fplo_short.sbatch
      fplo_long.sbatch
    inputs/
      example.in
    outputs/

  gromacs/
    README.md
    jobs/
      gmx_md.sbatch
    container/

examples/
  benchmark_matrix.csv
  submit_all.sh
```

---

## **3. Naming Conventions**
- Job scripts: `software_task_resources.sbatch`
  - Example: `qe_relax_4h_1node.sbatch`
- Wrapper scripts: `run_software.sh`
- All templates include version tags in comments.

---

## **4. Usage Instructions**
### **4.1 Submitting a Job**
```
cd softwares/fplo/jobs
sbatch fplo_short.sbatch
```

### **4.2 Monitoring Jobs**
```
squeue -u $USER
sacct -j <JOBID>
```

---

## **5. Standard SLURM Template Specification**
Each `.sbatch` file should include the following structure:

### **Header**
```
#!/bin/bash
#SBATCH --job-name=<JOB_NAME>
#SBATCH --account=<ACCOUNT>
#SBATCH --partition=<PARTITION>
#SBATCH --nodes=<NODES>
#SBATCH --ntasks-per-node=<NTASKS_PER_NODE>
#SBATCH --cpus-per-task=<CPUS_PER_TASK>
#SBATCH --time=<TIME>
#SBATCH --mem=<MEMORY>
##SBATCH --gres=gpu:<GPUS> Since we have single gpu on the system on each node. It doesnot require now. 
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err
```

### **Variables Section**
```
ACCOUNT="project_x"
PARTITION="compute"
MODULE="fplo/22"
INPUT_FILE="inputs/example.in"
OUTPUT_DIR="$SLURM_SUBMIT_DIR/outputs"
```

### **Execution Section**
```
module purge
module load $MODULE

mkdir -p $OUTPUT_DIR
srun pw.x -in $INPUT_FILE > $OUTPUT_DIR/pw.out
```

---

## **6. Per-Software Documentation**
Each software directory includes:
- Typical HPC resource requirements
- Recommended MPI/OMP settings
- Pre/post-processing workflow
- Known issues and best practices

Example (Quantum ESPRESSO):
- CPU-only: Match tasks per node with physical CPU cores.
- Hybrid: Use MPI ranks × OpenMP threads.
- Suggested command: `srun pw.x -in input.in`

---

## **7. Modules and Containers Support**
Templates support:
- Environment Modules / Lmod
- Apptainer/Singularity

### **Container Example**
```
singularity exec qe.sif pw.x -in $INPUT_FILE > output.out
```

---

## **8. Input/Output Organization**
- `inputs/`: Minimal example input files
- `outputs/`: Output generated after job completion
- For large datasets, store externally and reference them

---

## **9. Testing & CI**
Included tests:
- Syntax validation for `.sbatch` files
- ShellCheck linting for wrapper scripts
- Placeholder checks (no unresolved <VARIABLE>)

---

## **10. Contribution Guidelines**
To contribute:
- Add software under `softwares/<name>/`
- Provide minimal + advanced templates
- Include documentation and version tags
- Follow formatting conventions

---

## **11. License and Citation**
- Default license: MIT
- Cite software authors properly in each submodule

---

## **12. Troubleshooting**
**PENDING job**:
- Account/partition mismatch
- Requested nodes unavailable

**MPI errors**:
- Use correct `srun` or `mpirun` based on cluster policy

**Slow I/O**:
- Use `$SCRATCH` rather than home directories
- **We have scratch directory, under maintenance, So you cannot use until further notice.**

---

## **13. Contact**
Maintain the list of maintainers here:
- Primary Maintainer: Aatiz Ghimire
- Email: hpc@tu.edu.np

---

This repository aims to become a standard reference for reproducible HPC job submissions across scientific disciplines.
