# DeltaAI Charging Estimator

A web-based calculator for estimating Service Unit (SU) charges on [NCSA DeltaAI](https://docs.ncsa.illinois.edu/systems/deltaai/en/latest/), hosted via GitHub Pages.

**[Live Site](https://biboyqg.github.io/deltaai-charging-estimator/)**

## How It Works

DeltaAI bills jobs in **Service Units (SU)**, where **1 SU = 1 GH200 superchip used for 1 hour**.

Each DeltaAI node contains 4 NVIDIA GH200 Grace Hopper superchips. Each GH200 provides:

| Resource | Per GH200             |
| -------- | --------------------- |
| GPU      | 1 x H100 (96 GB HBM3) |
| CPU      | 72 Grace ARM cores    |
| Memory   | 120 GB LPDDR5         |

The number of GH200s billed is determined by whichever resource dimension requires the most GH200 equivalents:

```
GH200s per node = max(GPUs requested, ceil(CPUs / 72), ceil(Memory_GB / 120))

Charge (SU) = GH200s_per_node x nodes x wall_hours x partition_factor
```

### Partition charge factors

| Partition          | Factor | Max Wall Time |
| ------------------ | ------ | ------------- |
| `ghx4` (default)   | 1.0x   | 48 hours      |
| `ghx4-interactive` | 2.0x   | 2 hours       |

## Usage

Enter your SLURM job parameters:

- **Nodes** — number of nodes (1–152)
- **GPUs per node** — H100 GPUs per node (0–4)
- **Tasks per node** — `--ntasks-per-node`
- **CPUs per task** — `--cpus-per-task`
- **Memory per node** — in GB (up to 480 GB)
- **Wall time** — in hours or minutes
- **Partition** — `ghx4` or `ghx4-interactive`

The estimator will show:

- Total estimated SU charge
- Billing breakdown by resource dimension (GPU, CPU, memory)
- Which dimension is driving the charge
- Ready-to-copy `#SBATCH` directives for your job script

## Deploying to GitHub Pages

1. Push this repository to GitHub.
2. Go to **Settings > Pages**.
3. Under **Source**, select the branch (e.g., `main`) and root (`/`) directory.
4. Save. The site will be available at `https://<org>.github.io/<repo>/`.

## References

- [DeltaAI Job Accounting](https://docs.ncsa.illinois.edu/systems/deltaai/en/latest/user-guide/job-accounting.html)
- [DeltaAI Running Jobs](https://docs.ncsa.illinois.edu/systems/deltaai/en/latest/user-guide/running-jobs.html)
- [DeltaAI System Architecture](https://docs.ncsa.illinois.edu/systems/deltaai/en/latest/user-guide/architecture.html)

## Disclaimer

This tool provides **estimates only**. Actual charges may differ based on SLURM scheduling, resource allocation behavior, and system configuration. Always verify with the `jobcharge` command on DeltaAI for actual usage.
