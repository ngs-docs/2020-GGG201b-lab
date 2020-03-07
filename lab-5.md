---
tags: ggg, ggg2020, ggg201b
---
# GGG 201b, feb 2020 - lab 5 - Assembly 2

## Getting set up for the assembly workflow

(Ask people to do this soon.)

Log into farm; then clone [the assembly repository](https://github.com/ngs-docs/2020-ggg-201b-assembly),

```
cd ~/
git clone https://github.com/ngs-docs/2020-ggg-201b-assembly.git assembly-repo/
```

change into that directory,

```
cd ~/assembly-repo/
```

and also install the necessary software in a new environment:

```
conda create -y -n assembly -c conda-forge -c bioconda prokka megahit snakemake-minimal
```

## How assemblers work

## An assembly workflow!

Actually, we'll come back to this :). Let's get some results first!

## Running assembly on a farm node with sbatch

Most HPCs or clusters at most universities distribute jobs across their cluster with a queuing system that allocations heterogeneous resources based on demand. This is a fancy way of saying that the queueing system tries to figure out how to efficiently use the available computers.

To do this, the cluster needs to know what resources your program needs. The standard way to provide this is via a queue submission script, a little shell script that (a) runs what you want to run while (b) providing the information at the top.

On farm the queueing system we use is Slurm, and the way you provide your queue submission is to run `sbatch <filename>` where `<filename>` contains all the information sbatch needs to run your program.

I should say that this is a quite different way to run programs from the way we've been doing it. A few differences:

* the script runs when Slurm decides to run it, based on the resources requested and the resources available. This can be now or in 12 hours!
* the script runs on a different computer than the head node, which means it has access to different (usually bigger!) resources (memory and disk and CPUs)
* the computer the script runs on may not have access to the Internet, though, so commands like `conda install` may not work properly.
* the script runs in "batch" mode, so you don't get the output to your terminal. It goes to a separate file.
* the script doesn't necessarily run in your home directory, so you need to make sure you go to the right directory
* the script runs in new environment, so you need to be sure to set your software up right (luckily conda makes this easy!)

In exchange for all of this complexity, you can set up dozens or hundreds or even thousands of jobs in advance, and then just ...walk away while slurm deals with it!

(In GGG 298, we'll even learn how to have snakemake distribute jobs for you, using slurm!)

### Queue submission script example

Check out the file [`farm-queue.sh`](https://github.com/ngs-docs/2020-ggg-201b-assembly/blob/master/farm-queue.sh):

```
less farm-queue.sh
```
(use 'q' to quit out of less)

There are three parts to this file.

The first part is a series of commented out lines starting with `#SBATCH`. These are instructions to Slurm about what resources you think your program needs.

The lines following are the actual shell commands to go and run your stuff. Here you'll see we're activating a conda environment, changing to a directory, and 

### Testing queue submission scripts

It's always a good idea to test your script before running it for realz.

First, let's make sure snakemake isn't actually going to run anything - the command should be `snakemake -p -n`, where `-n` tells snakemake to just print out what it _would_ run were it going to run anything.

Now run it locally, with `bash`, just to test the syntax etc.

```
bash farm-queue.sh
```

it should succeed and printout a bunch of stuff, right up until you get this error,`farm-queue.sh: line 31: SLURM_JOB_ID: unbound variable`, which happens because we're not running it via slurm!

Now, use `nano farm-queue.sh` and remove the `-n` from the snakemake line, use <kbd>CTRL-X</kbd> <kbd>y</kbd> to save it, and now run:

```
sbatch farm-queue.sh
```

You will see something like, `Submitted batch job 18052163`. This number is your job number. Once it starts running, all of the output will be in `slurm-JOB.out` and the error messages (if any) will be `slurm-JOB.err`.

NOTE: you do _not_ want to run `bash farm-queue.sh` again - if you do now, it will run your assembly workflow on the head node, where it might crash or (worse) cause others' programs to crash.

### Some slurm commands and interactions

* sbatch - submit shell script to queueing system
* squeue - list everything in the queue. run `squeue | grep ggg` to see all the things submitted.
* scancel - `scancel JOBID` kills the running job.

Log files are written progressively to `slurm-JOB.out`. Run `tail -f slurm-JOB.out` (replace JOB with your job ID!) to see what your program is doing; use <kbd>CTRL-C</kbd> to get out of tail.

## Snakemake workflow revisited

OK! Let's look at the Snakefile we're running!

It has two main steps. The first step is to run the [megahit assembler](https://github.com/voutcn/megahit/). The second step is to run the [prokka](https://github.com/tseemann/prokka) prokaryotic genome annotation pipeline. Before we get into the details of what those do, let's talk about the Snakefile.

* rule `link_data`, what is that `{input}` and `{output}` stuff? These are variables that are replaced with the entire list of inputs and outputs, respectively!
* rule `link_data`, what is this output mismash? This [constrains wildcards](https://snakemake.readthedocs.io/en/stable/tutorial/additional_features.html#constraining-wildcards) to match a particular pattern
* rule `assemble_data`, in the `input:` block, what is this `r1` and `r2` stuff? If you have multiple inputs, you can name them here and then use them as `{input.name}` in the shell command.
* rule `assemble_data`, what's this `threads:` block? It tells snakemake to use up to that many cores, where the number of cores snakemake is ALLOWED to use for this rule is given by the `-j` parameter, and also to Slurm in the sbatch script with `-c 8`. (Yes, complicated.) This info is then passed into the shell command with `{threads}`, which will be between 1 and 8 in this case.
* rule `assemble_data`, what's this `params:` block? This is a new block where you can put parameters that you might want to adjust in various ways. 
* rule `assemble_data`, what's this `directory(...)` thing? It tells snakemake that the output is a whole directory.

## Useful information for farm

There are a few bits and bobs of useful information on campus about using farm. Here's ours:

https://github.com/dib-lab/farm-notes