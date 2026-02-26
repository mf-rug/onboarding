# Installing and using Boltz on the Habrok cluster

## Using Habrok in the command line

Habrok is the supercomputing cluster of the RUG. Instead of running heavy programs on your own laptop, you connect to Habrok and ask it to run jobs using strong CPUs or GPUs. To use Habrok, you first need an account and access permissions. Instructions for requesting access and connecting can be found [here](https://wiki.hpc.rug.nl/habrok/connecting_to_the_system/connecting). 

The command line (or terminal) is a text-based way to interact with a computer.
Instead of clicking folders and buttons, you type commands to move between folders, run programs, and submit jobs to the cluster.

If you have never used the command line before, there are some useful tutorials you can do: 
- [Shell novices:](https://swcarpentry.github.io/shell-novice/) A beginner-friendly introduction to the command line.
- [Habrok tutorial:](https://wiki.hpc.rug.nl/habrok/additional_information/course_material/exercises_solutions) Exercises specific for Habrok cluster


## Installing Boltz (and Boltzgen)

### Step 1 - Log into Habrok cluster

Use the terminal to log into the habrok cluster with:

```bash
ssh [p-number]@[login-node]
```
For example:  
```bash
ssh p287956@login1.hb.hpc.rug.nl
```
When doing it for the first time, it will ask you to confirm that this address is to be trusted. Type ‘yes’ to continue. You then login with your university password and 2-factor authentication. All actions you do now happen on the cluster, no longer on your own computer. 

For most cases, a normal login node is fine (login1.hb.hpc.rug.nl). if you plan on installing programs that use GPU (like boltz), it is better to login to a GPU node (gpu1.hb.hpc.rug.nl) to make sure there are no errors when installing GPU-driver dependent packages. You will always have access to the same files regardless of which node you use to login. For an overview of the available login nodes, check [here](https://wiki.hpc.rug.nl/habrok/connecting_to_the_system/connecting).

Habrok is split up into different partitions, i.e different parts of the server where your data is stored. By default, you will be in your home directory, which has less space but is more frequently backed up. You also have the /scratch/ partition, which has significantly more space, so you want to do most calculations in /scratch/. Read more about the partitions [here](https://wiki.hpc.rug.nl/habrok/data_management/storage_areas). You can access your scratch folder using:

```bash
cd /scratch/[p-number]/
```

### Step 2 - Load recent Python version

To check the installed version of Python, run:
```bash
python3 --version
```
Usually habrok defaults to an older python version (~python 3.6). For Boltz, we need a newer version (at least 3.10). Instead of installing manually, we can load in a module from Habrok. Modules are different packages/software already tested and pre-installed on the habrok cluster. If a module is available, this is usually the safest way to use it. You can find more about it [here](https://wiki.hpc.rug.nl/habrok/software_environment/module_system).

We can use `module` to load a newer version of python3:
```bash
module load Python/3.11.5-GCCcore-13.2.0
```

### Step 3 - Create a virtual environment

A virtual environment is a separate workspace for a specific project. It keeps the packages you install for one project isolated from the packages used by other projects. This helps avoid situations where different programs need different, incompatible versions of the same package. It is best to use separate environments for separate projects instead of installing everything globally on your system.

There are two common ways to manage environments:
- **Virtual environments (venv):** These are built into Python and are the simplest option. They create an isolated folder that contains the packages for your project. They still use your main Python installation, but keep project-specific packages separate from the rest of your system.
- **Conda environments:** Conda is a separate tool (not included with Python) that creates fully isolated environments, including their own Python installation and packages. This can be useful for complex software, but it also takes up more disk space and creates many additional files.

For this reason, is generally best to use virtual environments unless your program only works with Conda. We will use venv to make a virtual environment for Boltz.

In your scratch folder, do:
```bash
Python3 -m venv [name environment]
```
For example:
```bash
Python3 -m venv boltz-env
```

To activate the environment, use:
```bash
Source [name environment]/bin/activate
```

You should now see the name of your virtual environment in brackets next to the command terminal prompt. To deactivate it, use
```bash
deactivate
```

### Step 4 - Install Boltz/Boltzgen

When the virtual environment is activated, we can install Boltz inside it using a pip install, as specified on the [GitHub page of Boltz](https://github.com/jwohlwend/boltz/tree/main). 

```bash
pip install boltz[cuda] -U
```

The same process is done for installing Boltzgen. Make sure a recent version of python is loaded, create a new virtual environment (something like boltzgen-env), and follow the instructions on the [GitHub page of Boltzgen](https://github.com/HannesStark/boltzgen).

```bash
pip install boltzgen
```

## Using Boltz

Boltz is a protein structure prediction tool. We will run Boltz on the Habrok cluster by submitting a job through the command line. 

Documentation:
- Usage guide: [link](https://github.com/jwohlwend/boltz/blob/main/docs/prediction.md)
- Example input files: [link](https://github.com/jwohlwend/boltz/tree/main/examples)  

### Step 1 – Create a working folder in `/scratch`

Go to your scratch directory and create folders for your project, logs, and results:

```bash
cd /scratch/pxxxxxx
mkdir Boltz
cd Boltz
mkdir logs results
```

### Step 2 - Create or copy your input file (FASTA or YAML)

You now need an input file in your working folder (`scratch/pxxxxxx/Boltz`) that tells Boltz what to predict. You can copy an existing file into your folder using `rsync`, or create a new file using `nano`.

Boltz accepts two types of input files:
- A simple `.fasta` file (for sequences)
- A more detailed `.yaml` file (recommended, allows ligands and more settings)

For ligands, you can provide either:
- A CCD code (ligand name used in PDB files, e.g. FAD, MG), search codes [here](https://www.ebi.ac.uk/pdbe-srv/pdbechem/)
- A SMILES string (chemical representation of the molecule)

Example of .yaml file:
```bash
sequences:
  - protein:
      id: [A]
      sequence: MKVPEAAISRLITYLRILEELEAQGVHRTSSEQLGELAQVTAFQVRKDLSYFGSYGTRGVGYTVPVLKREL
  - ligand:
      id: [NADH]
      ccd: NAI
```

### Step 3 - Create job submission script 

The job script requests resources from Habrok and needs to be created in you working folder (`scratch/pxxxxxx/Boltz`). Create a .sh file, for example:

```bash
nano run_boltz.sh
```

Example of submission script:
```bash
#!/bin/bash
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --gpus-per-node=1
#SBATCH --job-name=boltz
#SBATCH --partition=gpu
#SBATCH --output=logs/%x_%j.out
#SBATCH --error=logs/%x_%j.err

module load Python/3.11.5-GCCcore-13.2.0
source /scratch/p324178/boltz-env/bin/activate

boltz predict [filename].yaml \
--cache /scratch/p324178/boltz/.boltz/ \
--out_dir results/ \
--use_msa_server \
--accelerator gpu \
--devices 1
```
Save changes with `Cmd + O` and close with `Cmd + X`.

### Step 4 - Submit job

To start the prediction, submit the job with:

```bash
sbatch run_boltz.sh
```

This sends your job to the queue. It will run when resources (GPU/CPU) become available.

You can check the status of your job with:

```bash
squeue -u $USER
```

If your job disappears from the list, it usually means it has finished (or failed).

### Step 5 - Check run and download results 

After the job finishes you can open the results/ folder to see the predicted structures. If the folder is empty, check the logs/ folder for error messages

To download the results to your local computer, use rsync from a terminal on your own machine (not while logged into the cluster):

```bash
rsync -r [p-number]@[login-node]:/path/to/results /local/destination
```

Example:

```bash
rsync -r p324178@login1.hb.hpc.rug.nl:/scratch/p324178/Boltz/results .
```

This will copy the entire results folder from the cluster to your local computer. You can then check your predicted structures :)



