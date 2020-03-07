---
tags: ggg, ggg2020, ggg298
---
# GGG 201(b), Lab Homework 1

Due at the beginning of class on Friday, 1/31.

## 1. Sign up for GitHub Classroom

Sign up for GitHub Classroom using https://classroom.github.com/a/VoyiLDCW. This will probably involve creating a (free) GitHub account.

## 2. Log into farm & clone your github repo for hw1

On farm, clone your assignment repository, which will be something like `https://github.com/dib-lab/2020-ggg-201b-lab-hw1-ctb`; do so like this,

```
git clone YOUR_REPO_URL 201b-lab-hw1
```

Change into that directory:
```
cd 201b-lab-hw1/
```

## 3. Activate the `vc` conda environment.

```
conda activate vc
```

## 4. Edit your Snakefile to do variant calling on three more sample data sets.


Add these three data sets to the Snakefile; they are all in `~ctbrown/data/ggg201b`.

```
SRR2584403_1.fastq.gz
SRR2584404_1.fastq.gz
SRR2584405_1.fastq.gz
```

Please make sure of the following:

* the VCF output should be sent to `SAMPLE-variants.vcf` for each of the four samples.
* Running `snakemake` by itself should go from raw data to final results for all samples (see 'default rule' instructions from [week 3](https://hackmd.io/cGYzxz07SseGxH0y2gjYJw?view#Create-a-good-%E2%80%9Cdefault%E2%80%9D-rule))
* All of the generated files (including intermediates) are in at least one 'output:' annotation, so that e.g. `snakemake --delete-all-output` removes all of the generated files in the directory. (See [this section of lab 3](https://hackmd.io/cGYzxz07SseGxH0y2gjYJw?view#Re-running-everything))

It turns out that the copy_data rule for all four files is a bit tricky; here's a freebie, courtesy of Ashleigh:

```

# copy data from /home/ctbrown/data/ggg201b
rule copyData:
    output:
        "SRR2584857_1.fastq.gz",
        "SRR2584403_1.fastq.gz",
        "SRR2584404_1.fastq.gz",
        "SRR2584405_1.fastq.gz"
    shell:
        "ln -s /home/ctbrown/data/ggg201b/*.fastq.gz ."
```

## 5. Commit and push your changes back to github.

At any time, do the following to save changes.

```
git commit -am "updated Snakefile"
git push origin master
```
(you can do this as many times as you want, and save intermediate changes, etc. etc.)

## 6. Relax in knowledge of a job well done.

If you run into any trouble with submission, that's ok - we'll debug it on Friday's class.

Reminder - you can ask questions and request help [on the MAD site](https://mad.oxli.org/c/GGG-lab-courses-for-winter-2020/9)!

