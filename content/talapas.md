---
Title: Guide to Talapas
---

# How to use `R` in talapas

You can find the Talapas knowledge base [here](https://hpcrcf.atlassian.net/wiki/spaces/TCP/overview). 

## Logging in to Talapas

Usde `ssh` to login through the terminal:

```{bash}
ssh username@talapas-login.uoregon.edu
```

Use `scp` to copy files to/from your local machine. Adding the `-r` flag lets you copy whole directories and all of the files within that directory. Enclosing filenames in \{\} allows you to list multiple files separated by commas. Some examples of its usage:

```{bash}
scp ~/myfile.tsv username@talapas-login.uoregon.edu:./projects/my_project
scp username@talapas-login.uoregon.edu:./projects/my_project/myfile.tsv ~/
scp -r ~/my_directory username@talapas-login.uoregon.edu:./projects/my_project
```

To login to a virtual desktop environment go to [talapas-login.uoregon.edu](talapas-login.uoregon.edu) with your browser.

## Submitting a job to slurm

When you login to talapas, you will be on one of the login nodes. To run analyses, you will have to access a compute node by submitting a job. From the terminal, there are two ways of submitting a job. You can either start an interactive session in your terminal using `srun` or submit a batch script using `sbatch`, which will run automatically in the background. In either case, you will have to wait in the queue until a node opens up for the amount of CPU, memory, and time that you need.

An example `srun` command:

```{bash}
srun --account=crobe --pty --partition=short --mem=1024M --time=240 bash
```

you must have `--account=crobe` and `--pty` and `bash` in there. The other commands are optional, but let you spectify memory, time, CPUs, etc. Also, these flags (e.g., `--time=240`) are the same as what you would put into a batch script. `--partition=short` is the default type of partition you will use. `--mem=1024M` gives you a gig of RAM/memory and `--time=240` gives you 4 hours of time on the node.

An example batch script:

```{bash}
#!/bin/bash
#SBATCH --partition=short       ### queue to submit to
#SBATCH --job-name=varcomp      ### job name
#SBATCH --output=varcomp.out    ### file in which to store job stdout
#SBATCH --error=varcomp.err     ### file in which to store job stderr
#SBATCH --time=30               ### wall-clock time limit, in minutes
#SBATCH --nodes=1               ### number of nodes to request
#SBATCH --ntasks-per-node=1     ### number of tasks per node
#SBATCH --cpus-per-task=28      ### number of cores/CPUs
#SBATCH -A crobe                ### account
module load R/3.6.1 # optional if you want to load a specific version
cd projects/Royal_Society/R
Rscript var_comp.R
```

A batch script is essentially just a normal `bash` script with some extra parameters at the top, which slurm reads. You should specify `--partition=short`, a `--job-name` and where to put `--output` and `--error`. `--time` sets the time in minutes. `--nodes` and `--ntasks-per-node` will pretty much always by equal to `1` for `R` jobs. You can set how many cores/CPUs you want with `--cpus-per-task` and the amount of memory you want with `--mem` where it takes a number and a unit. For example, `--mem=64G` will gives you 64 gigabytes of RAM on that node. I think the default is something like 4 GB per CPU. At the bottom of the script, you should enter normal terminal commands as you would in a `bash` script or as you would enter them directly at the command line. This script can be in your home directory or in the project directory.

Run a batch script by typing `sbatch` and then the name of the batch script (the filename extension doesn't matter so I usually just use `.batch`). The script will run in the directory you call it from. So if you're in your home directory and you want to run commands in another directory, you have have to add some `cd` commands to navigate to where you want the commands to run. Or you can `cd` to the project directory and run the batch script from there. Alternatively, you can just add filepaths to the commands:

```{bash}
sbatch my_script.batch
```

To check on your jobs you can type in:

```{bash}
squeue -u username
```

(Replacing `username` with your username.) This will tell you whether they're still in the queue, how long they've been running, etc.

You can cancel a job by getting the job ID from `squeue` and then entering:

```{bash}
scancel job-id
```

(Replacing `job-id` with the actual number.) Any output that your commands print to the terminal will be saved in the `output` file that you specify. Any errors specifically from slurm, for example if your job crashes for some reason, will be printed to the `error` file that you specify.


## Using `R`


## Check if `R` is installed

First, login to talapas using `ssh`. By default, when you login to talapas, `R` is already loaded with a recent installed version (at least on my account). Check by entering:

```{bash}
R --version
```

at the command line. If this returns an error or if you want a specific version of R you can see the installed versions with:

```{bash}
module spider R
```

and load the latest version with:

```{bash}
module load R
```

or a specific version with:

```{bash}
module load R/3.6.1
```

This is important to check first, because if it's not automatically loaded, you will have to include `module load R` at the top of your batch file when you go to submit jobs. 

(It's probably *safest* to always load a specific version of `R` at the top of your batch file, but I don't do this. `r emo::ji("shrug")`)

## Running `R` at the command line

Run `R` directly by typing 

```{bash}
R
```

at the command line, which will open an `R` console. This is a good way of testing things and installing packages. You should manually install all of the packages you need this way (with the appropriate `R` module loaded) before submitting a batch script. When you run `install.package()` from talapas it will ask you to select a mirror from which to download the files. I usually choose the OSU one.

## Running an `R` script from the command line

Use the function `Rscript` to run a script from the terminal:

```{bash}
Rscript my_script.R
```

By default, the output is not saved to a file, but is just printed to the terminal. So any output you need should be manually saved within your script.

(if you're running scripts manually at the command line without a batch job (i.e., without using `sbatch`) then remember to login to a compute node using `srun` instead of running your script on the login node.)

## Running an `R` script from within a batch file using slurm

If you want to submit your `R` script as a job to talapas, just create a batch script in which you `cd` into the folder with your `R` script and use the `Rscript` command like you would at the command line.

Your batch script will look something like this:

```{bash}
#!/bin/bash
#SBATCH --partition=short       ### queue to submit to
#SBATCH --job-name=varcomp      ### job name
#SBATCH --output=varcomp.out    ### file in which to store job stdout
#SBATCH --error=varcomp.err     ### file in which to store job stderr
#SBATCH --time=30               ### wall-clock time limit, in minutes
#SBATCH --nodes=1               ### number of nodes to request
#SBATCH --ntasks-per-node=1     ### number of tasks per node
#SBATCH --cpus-per-task=28      ### number of cores you want to use
#SBATCH -A crobe                ### account
module load R/3.6.1 # optional if you want to load a specific version
cd projects/Royal_Society/R
Rscript var_comp.R
```

And you will run it at the command line like this (except replace `varcomp.batch` with whatever the name of your batch file is):

```{bash}
sbatch varcomp.batch
```

(It can be helpful to use `require('package_name')` instead of `library('package_name')` so that you receive output in the event that a package fails to load.)

(If you've ever used `R CMD BATCH` to run `R` scripts at the command line, `Rscript` works a little differently. It prints output to the terminal (a.k.a., `stdout`) and does *not* save an `.Rout` file. Therefore, any output or errors will print to your `sbatch.out` file defined in your batch script.) 

## Using `rstudio` on talapas

If you want to use `rstudio` on talapas you can login to an Open OnDemand virtual desktop by opening your web browser and navigating to [talapas-login.uoregon.edu](talapas-login.uoregon.edu). Enter your uoregon login info and then click on "Interactive Apps" at the top of the screen and then "Talapas Desktop" from the dropdown menu that appears. This will take you to a screen where you enter your normal `srun` or `sbatch` info for slurm including your account name (crobe), the length of your job, how many cpus you need, etc. Once your session begins, click on "Launch noVNC in New Tab" to open the desktop.

Once you have the talapas desktop open, go to the top of the screen and click on the terminal button. At the terminal type in

```{bash}
module load rstudio
```

and if you want a specific version of `R` you can type in

```{bash}
module load R/3.6.1
```

or whatever version you want. Finally, type in

```{bash}
rstudio
```

to start the `rstudio` application. Your working directory will be your normal talapas home directory and you can use `rstudio` like you would on your local machine but with the power  `r emo::ji("biceps")` of talapas.

## Using `qiime2` with `modules` and `miniconda`

If you want to use `qiime2` on talapas, you should put the following commands into your batch script:

```{bash}
module load miniconda
conda activate qiime2-2019.10
```

where you should replace `qiime2-2019.10` with the name of the `conda` environment that has the version of `qiime2` that you need. You can see which `qiime2` environments are installed with:

```{bash}
conda env list
```

## How to install a specific version of `qiime2` on Talapas using `miniconda`

When you do this, you should go to the [qiime2 docs](https://docs.qiime2.org/) under "Installing QIIME 2" and run the commands from there to get the latest version. This will create a miniconda environment specific to *your* talapas user account. Below, I have copied how to do this for the [current version (version 2020.2)](https://docs.qiime2.org/2020.2/install/native/) as an example.

First, if you haven't already, activate the `miniconda` module:

```{bash}
module load miniconda
```

Then you can install `qiime2` in a new environment. Just update the url and environment name with whatever specific version you want to install:

```{bash}
wget https://data.qiime2.org/distro/core/qiime2-2020.2-py36-linux-conda.yml
conda env create -n qiime2-2020.2 --file qiime2-2020.2-py36-linux-conda.yml
# OPTIONAL CLEANUP
rm qiime2-2020.2-py36-linux-conda.yml
```

Then any time you what to use that version simply run:

```{bash}
source activate qiime2-2020.2
```

If you forget what it's called, it will be at the top of this left:

```{bash}
conda env list
```


