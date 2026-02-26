# Snellius Cluster Quick Reference

Snellius is operated by SURF and accessed via `int4.local.snellius.surf.nl`.

Official docs: <https://edu.nl/cq7yn>

## Filesystems

| Path | Purpose | Quota (mfurst) | Notes |
|---|---|---|---|
| `/gpfs/home5/mfurst` (`$HOME`) | Home directory | 200 GiB | Backed up, small quota |
| `/gpfs/scratch1/` | Scratch / temp storage | 8 TiB | **Not backed up**, auto-purged |
| `/projects/prjs1925` | Shared project space | Shared by group | Persistent, for project data |

Check your quota anytime with:
```bash
myquota
```

Check your account and budget with:
```bash
accinfo
```

## Partitions & Nodes

| Partition | CPUs/node | RAM/node | GPUs/node | Max walltime | Notes |
|---|---|---|---|---|---|
| `rome` (default) | 128 | 224 GiB | — | 5 days | AMD Rome, 522 nodes |
| `genoa` | 192 | 336 GiB | — | 5 days | AMD Genoa, 737 nodes |
| `fat_rome` | 128 | 960 GiB | — | 5 days | High-memory Rome |
| `fat_genoa` | 192 | 1.4 TiB | — | 5 days | High-memory Genoa |
| `gpu_a100` | 72 | 480 GiB | 4x A100 | 5 days | 63 nodes |
| `gpu_h100` | 72 | 720 GiB | 4x H100 | 5 days | 88 nodes |
| `gpu_mig` | 72 | 480 GiB | 8x A100 MIG (3g.20gb) | 5 days | 4 nodes |
| `gpu_vis` | 72 | 480 GiB | 4x A100 | 1 day | Same hardware as gpu_a100, for visualization |
| `himem_4tb` | 128 | 3.75 TiB | — | 5 days | 2 nodes |
| `himem_8tb` | 128 | 7.5 TiB | — | 5 days | 2 nodes |
| `staging` / `cbuild` | 32 | 224 GiB | — | 5 days | For data staging and building software |

Check partition status:
```bash
sinfo -s
```

## SBATCH Essentials

GPU jobs are **shared by default** (up to 4 jobs per node). Key SBATCH directives for a typical single-GPU job:

```bash
#SBATCH --partition=gpu_a100       # or gpu_h100
#SBATCH --gpus-per-node=a100:1     # or h100:1
#SBATCH --nodes=1
#SBATCH --cpus-per-task=18         # 72 cores / 4 GPUs = 18 per GPU
#SBATCH --mem=120G                 # ~480 GiB / 4 = 120G per GPU
#SBATCH --time=02:00:00
```

Add `--exclusive` if you need the whole node.

GPU GRES format is `<type>:<count>`, e.g. `a100:1`, `h100:2`.

## Module System

Uses Lmod. Modules are organized by **year-based software stacks**. You must load
the year module first before loading software from that stack:

```bash
module load 2024 Python/3.12.3-GCCcore-13.3.0   # correct
module load Python/3.12.3-GCCcore-13.3.0         # ERROR — year not loaded
```

Use `module spider <name>` to find which year stack a module belongs to.

Available Python versions:
| Module | Year stack |
|---|---|
| `Python/3.11.3-GCCcore-12.3.0` | 2023 |
| `Python/3.12.3-GCCcore-13.3.0` | 2024 |
| `Python/3.13.1-GCCcore-14.2.0` | 2024 |

```bash
module spider <name>      # search for a module (shows required year stack)
module load <year> <mod>  # load a module (year first!)
module list               # show loaded modules
module purge              # unload all modules
```

## Useful Commands

```bash
accinfo                   # account info, budgets, project spaces
myquota                   # filesystem quota usage
sinfo -s                  # partition overview
squeue -u $USER           # your running/pending jobs
scancel <jobid>           # cancel a job
sbatch job.sh             # submit a job
sbatch --test-only job.sh # dry-run: validates job without submitting
scontrol show job <jobid> # detailed info about a running job
sacct -j <jobid>          # accounting info for a completed job
```
