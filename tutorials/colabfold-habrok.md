# using ColabFold on the Habrok cluster

## Using Habrok in the command line

Habrok is the supercomputing cluster of the RUG. Instead of running heavy programs on your own laptop, you connect to Habrok and ask it to run jobs using strong CPUs or GPUs. To use Habrok, you first need an account and access permissions. Instructions for requesting access and connecting can be found [here](https://wiki.hpc.rug.nl/habrok/connecting_to_the_system/connecting). 

The command line (or terminal) is a text-based way to interact with a computer.
Instead of clicking folders and buttons, you type commands to move between folders, run programs, and submit jobs to the cluster.

If you have never used the command line before, there are some useful tutorials you can do: 
- [Shell novices:](https://swcarpentry.github.io/shell-novice/) A beginner-friendly introduction to the command line.
- [Habrok tutorial:](https://wiki.hpc.rug.nl/habrok/additional_information/course_material/exercises_solutions) Exercises specific for Habrok cluster

I wrote down the most important commands in [Command Line Basics](command_line_basics.md).

## Loading the ColabFold module in Habrok

The Habrok cluster has a set of pre-installed software made available as loadable modules. Modules on Habrok have been verified to work on the cluster, and automatically load the correct software environment, including correct python versions, compilers, and so on. If a module of a sofware package is available, this is the preferred way of running it on the cluster. More information about Habrok modules can be found on the [Habrok wiki](https://wiki.hpc.rug.nl/habrok/software_environment/module_system). The available modules can be inspected using:
```
module avail [search term]
```
ColabFold can be loaded as a module using:
```
module load ColabFold
```
If this was successful, you should now have access to two new commands: `colabfold_search` allows you to calculate MSAs, and `colabfold_batch` actually calls the folding model. You can list the input flags for both using `-h`
```
colabfold_search -h
colabfold_batch -h
```

## Calculating MSAs
By default, ColabFold requires multiple sequence allignments (MSAs) as input. This evolutionary context allows the model to provide more accurate predictions. For _de novo_ proteins for which evolutionary information is not available or not desirable, ColabFold should be run in single sequence mode (see below). When running low to medium-throughput calculations, MSA calculation can be outsourced to the ColabFold server. This is done automatically by `colabfold_batch` if a fasta file is provided rather than a directory with `.a3m` MSA files. 

For higher throughput, the ColabFold server might be too slow, or a usage limit on the server might be hit. In this case, MSAs can also be calculated locally using the `colabfold_search` command. Calculating the MSAs requires high memory and CPU to calculate, while the folding model itself requires primarily GPU. For this reason, these calculations are run as separate jobs from the `colabfold_batch` command. A jobscript for `colabfold_search` will look something like:
```
#SBATCH --job-name=msa
#SBATCH --time=2:00:00
#SBATCH --nodes=1
#SBATCH --partition=regular
#SBATCH --cpus-per-task=10
#SBATCH --mem=100GB

module load ColabFold

inputfile='/path/to/input/fasta.fa'
outdir='/path/to/output/directory'

colabfold_search "$inputfile" $COLABFOLD_DATA_DIR "$outdir" --db-load-mode 0 --threads 20
```
Save these commands in a text file (For example called `jobscript-msa`). Then submit this jobscript to the cluster using:
```
sbatch jobscript-msa
```
Depending on how many sequences are in the input, the time, number of threads, or memory can be increased. If very high amounts of memory are needed, the high memory partition can be used. In this case, replace the partition with `#SBATCH --partition=himem`. All MSAs will be saved as `.a3m` files in the specified directory.

## Running ColabFold
To run ColabFold locally , the `colabfold_batch` command is used. This is also submitted as a jobscript, this time requesting GPU. in this case, memory is not important, since primarily the memory internal to the GPU is used. The available internal memory for each GPU is listed on the [habrok wiki](https://wiki.hpc.rug.nl/habrok/advanced_job_management/running_jobs_on_gpus). If your protein does not require a specific GPU with higher internal memory, the `--gpus-per-node` can simply be set to 1, which will select whichever GPU is most readily available. If a specific GPU is needed, this can be set by using for example `--gpus-per-node=a100:1`, which requests one A100 GPU. Your jobscript will then look something like:
```
#!/bin/bash
#SBATCH --time=2:00:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --gpus-per-node=1
#SBATCH --job-name=colab
#SBATCH --partition=gpu

module load ColabFold

inputdir='/path/to/input/msas/'
outdir='/path/to/output/directory'

colabfold_batch "$inputdir "$outdir" 
```
The exact input and flags for the colabfold_batch command can differ depending on the aim and throughput of your job. If no MSAs are provided, they will be automatically calculated by the ColabFold MSA server based on the sequences provided in the fasta. This is convenient for low to medium throughput, but becomes slow for high throughput calculations. In this case, your `colabfold_batch` command will look like:
```
colabfold_batch input_sequences.fa "$outdir"
```
If you have a _de novo_ protein for which evolutionary information is not available or not desirable, you can force it to run with just the sequence, bypassing MSA calculation entirely. In this case, your `colabfold_batch` command will look like:
```
colabfold_batch input_sequences.fa "$outdir" --msa-mode single_sequence
```
If MSAs have already been calculated using `colabfold_search` or during a previous run, the directory with MSAs can be provided directly, as shown in the jobscript example. Like before, save these commands in a text file (For example called `jobscript-colab`). Then submit this jobscript to the cluster using:
```
sbatch jobscript-colab
```

