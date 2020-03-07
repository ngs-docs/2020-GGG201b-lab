---
tags: ggg, ggg2020, ggg298
---
# GGG 201(b), Lab Homework 2

Due at the beginning of class on Friday, 2/28.

## 1. Accept homework #2

Click https://classroom.github.com/a/2nJpSJ74 to accept the homework for Lab Homework #2.

## 2. Log into farm & clone your github repo for hw1

On farm, clone your assignment repository, which will be something like `https://github.com/dib-lab/2020-ggg-201b-lab-hw2-ctb`; do so like this,

```
git clone YOUR_REPO_URL 201b-lab-hw2
```

Change into that directory:
```
cd 201b-lab-hw2/
```

## 3. Determine how many reads you need for a good assembly

There are 2,129,833 reads in SRR2584857_1.fastq.gz and SRR2584857_2.fastq.gz. Approximately how many do you actually need for a good assembly?

Determine this by editing the Snakefile in your hw2 directory to use a list of numbers together with `expand` to generate and assess assemblies for a range of subsamples. See [lab 6](https://hackmd.io/6pNk3hzjQkmzOAYfSNpvgQ?view) for some example code.

Remember to use slurm to submit the homework, and examine the slurm output file to see if it works. (You can use 'snakemake -n' to check the syntax of your snakemake file without running it.) 

**Note**: you'll need to fix the `farm-queue.sh` file in your homework directory to cd into the correct directory, which will be individual to your account.

The correct solution will:

* have a list of numbers at the top of the file, each of which represents the number of lines (4 lines per sequence, so each number should be a multiple of 4)
* use `expand` to generate many quast output directories with that list of numbers


The way to generate an assembly from 25k reads is to add `"rel606.100000.quast"` to the `all:` rule inputs.

I would suggest no more than 10 numbers (as that will run 10 different assemblies).

Once you have generated a number of quast assemblies, look at each quast `report.txt` file to see where the assembly collapses catastrophically into fragments. Bring that number with you to class on next Friday!

**What I would like you to bring to class on 3/6:** be prepared with a list of subsample sizes, and their associated **sum size of contigs > 1000 & number contigs > 1000**.

## 4. Commit and push your changes back to github.

At any time, do the following to save changes.

```
git commit -am "updated Snakefile"
git push origin master
```
(you can do this as many times as you want, and save intermediate changes, etc. etc.)

This is how you submit your homework :)

## 5. Relax in knowledge of a job well done.

Reminder - you can ask questions and request help [on the MAD site](https://mad.oxli.org/c/GGG-lab-courses-for-winter-2020/9)!

