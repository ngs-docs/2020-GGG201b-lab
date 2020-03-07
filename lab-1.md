---
tags: ggg, ggg2020, ggg201b
---

[toc]

# GGG 201b, jan 2020 - lab 1 - NOTES

## Friday Lab Outline - 1/10

* introductions
* [syllabus](https://hackmd.io/YaM6z84wQeK619cSeLJ2tg)
* goals of this course
    * how this course will differ from previous years
    * how this course will differ from GGG 298
    * how this course will differ from other courses
* [signing up for a farm account](https://github.com/dib-lab/dib_rotation/blob/master/01_farm_account.md)
    * [generating a key pair in windows](https://docs.computecanada.ca/wiki/Generating_SSH_keys_in_Windows/en) using mobaxterm
* auditing
* our first workflow: variant calling!!!!!!

## Day 1: Variant Calling

learning goals:
- introduce basic concepts of variant calling
- understand the basics of running shell commands via snakemake
- download and examine some data

### A very brief discussion of variant calling

Lots of whiteboarding goes here!

### Start a binder

https://github.com/ngs-docs/2020-ggg-201b-variant-calling

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ngs-docs/2020-ggg-201b-variant-calling/master)

wait 20 or 30 seconds...

### Open a terminal

...and set a prompt:
```
PS1='$ '
```


### Run some stuff!

There are lots of "rules" in the Snakefile [(view it on GitHub, too)](https://github.com/ngs-docs/2020-ggg-201b-variant-calling/blob/5fe29045b3741661b551682a326689cd66cd6f6a/Snakefile)! Try:

```
grep rule Snakefile
```

and

```
nano -ET4 Snakefile
```
to examine them. To exit type `Control + x`

* what is the structure of a rule?
* what does shell mean here?

----

Let's run one! Try the following command:
```
snakemake -p map_reads
```

This says, "run the shell command in the Snakefile under the 'map_reads' rule".

`-p` means "show the command that you're running.

It will fail! Why?

Because we don't have the prerequisite files! We need to download some stuff and prepare it.

Start with

```
snakemake -p download_data
```

ahh... cool green!

What does this command do?

Now run some more -- one at a time.

```
snakemake -p download_genome
snakemake -p uncompress_genome
snakemake -p index_genome_bwa
snakemake -p map_reads
snakemake -p index_genome_samtools
snakemake -p samtools_import
snakemake -p samtools_sort
snakemake -p samtools_index_sorted
snakemake -p samtools_mpileup
snakemake -p make_vcf
```

This runs a complete (if fairly simple :) variant calling workflow.

View the results with

```
samtools tview -p ecoli:4314717 --reference ecoli-rel606.fa SRR2584857.sorted.bam
```

A few things to discuss:

* these are just shell commands, with a bit of "decoration". You could run them yourself if you wanted!
* order of the rules in the Snakefile doesn't matter
* `snakemake -p` prints the command
* red if it fails (try breaking one command :)
* it's all case sensitive
* tabs and spacing matters...
* ...what are we actually doing here?

Some foreshadowing:

* wouldn't it be nice to not have to run each rule one by one?
* wouldn't it be nice if the command didn't rerun if it had already run

### Upgrading our Snakefile by adding 'output:'

Run 
```
nano -ET4 Snakefile
```
(reminder, CTRL-X, yes, to save!)

and add `output: "SRR2584857_1.fastq.gz"` to the `download_data` rule.

Now try running the rule: `snakemake -p download_data`

Run it again. Hey look, it doesn't do anything! Why??

Try removing the file: `rm SRR2584857_1.fastq.gz`. Now run the rule again.

### Upgrading our Snakefile by adding `input:`.

To the `map_reads` rule, add:

`input: "SRR2584857_1.fastq.gz", "ecoli-rel606.fa"`

What does this do?

### End of class recap

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands
* You can "decorate" the rules to tell snakemake how they depend on each other.


