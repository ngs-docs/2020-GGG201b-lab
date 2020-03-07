---
tags: ggg, ggg2020, ggg298
---

[toc]

# GGG 201b, jan 2020 - lab 2 - NOTES

## Friday Lab Outline - 1/17

You can do collaborative note taking [here](https://hackmd.io/cZ9ehhV7TQONFu5EYx2JgA) if you like!

Outline:
* farm e-mail accounts & login check
* mini-homework for next class (see e-mail!)
* more variant calling with more snakemake!!

## Day 2: More variant calling with more snakemake

learning goals:
- understand basic assumptions of shotgun sequencing
- discuss how shotgun sequencing and variant calling interact
- learn how to connect commands in snakemake

### Shotgun sequencing

* assumption of uniform coverage
* where these assumptions are violated
* revisiting variant calling workflow

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

### Recap week 1 --

The `Snakefile` contains rules that we can run by name, e.g.
```
snakemake -p download_genome
```
This runs the shell command to download a genome, which
is
```
wget https://osf.io/4rdza/download -O SRR2584857_1.fastq.gz
```
and the `-p` says to print out the command(s) being run, which are normally omitted by snakemake.

But what if we want to run all the commands at once?
It gets annoying to run each command individually:

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
when really all we want do is reach the end point, which is `make_vcf`, which produces a variant call file.

### Upgrading our Snakefile by adding 'output:'

Run 
```
nano -ET4 Snakefile
```
(reminder, CTRL-X, yes, to save!)

and add
```
    output: "SRR2584857_1.fastq.gz"
```
to the `download_data` rule before the `shell:` line.

And try running the rule: `snakemake -p download_data`

Run it twice. Hey look, it doesn't do anything the second time! Why??

Try removing the file: `rm SRR2584857_1.fastq.gz`. Now run the rule again. It's aliiiiiive!

What's happening here is that snakemake is "smart" enough to know that if the output file exists, it doesn't need to be recreated. So once you tell it what the output of a rule is, it gets smarter about running it.

### Upgrading our Snakefile by adding `input:`.

To the `download_genome` rule, add:
```
    output: "ecoli-rel606.fa.gz"
```

and to the `uncompress_genome` rule, add:

```
    input: "ecoli-rel606.fa.gz"
```

What does this do?

This tells snakemake that `uncompress_genome` depends on having the input file `ecoli-rel606.fa.gz` in this directory, and that `download_genome` can produce it.

What's extra cool is that snakemake will now automatically run the rule that outputs this file (which is `download_genome`) before running `uncompress_genome`!

Try it:

```
snakemake -p download_genome
```
and you'll see that it runs two things: first the download_genome rule, then the uncompress_genome rule.

What is the output of the rule `uncompress_genome` and how would you find that out in general?

### In-class exercise A

Add "output:" with the correct filename to the rule `uncompress_genome`.

Hint: look at what changes in the directory when you run the command. You can use the Jupyter console window, or you can do `ls -l --sort=time` after running the rule.

### Formatting snakefiles

A few quick notes:
* input and output (and other things) can be in any order, as long as they are before shell.
* you can either put things all on one line, or form a block by indenting.
* rule names can be any valid variable, which basically means letters and underscores; you can use numbers after a first character.
* you can make lists for multiple input or output files by separating filenames with a comma

### In-class exercise B

How would you fix the rules `index_genome_bwa` and `map_reads` to have the appropriate input: and output:?

Hint: look at the shell command for those rules.

### Running lots of commands all at once

If you've fixed the rules `index_genome_bwa` and `map_reads`, you should be able to run everything up to the rule `samtools_sort` by just specifying `samtools_sort` - try it!

```
snakemake -p samtools_sort
```

(This can also be a good way to check to make sure you have all the output: information right, because you'll have files left over if you forgot to put them in output: :)

### Re-running everything

You can tell snakemake to delete everything it knows how to make for a particular rule (including all preceding rules) by running
```
snakemake --delete-all-output samtools_sort
```

### Using filenames instead of rule names

You don't actually need to use the rule names (this will be important later on!). You can just tell snakemake what file you want produced, and run that.

So:
```
snakemake -p SRR2584857.sorted.bam
```
will also work to run the rule `samtools_sort`, but you don't have to remember the rule name (which can be arbitrary...)

### In-class exercise C

Add appropriate input: and output: information to rule `samtools_index_sorted`.

### Running the whole workflow!

And now you should be able to run `snakemake -p make_vcf`.

### Bonus: create a default rule

By default, if you don't specify a rule snakemake runs the first rule in the file.  (This is actually the only case where the order of rules in the Snakefile matters!)

So it's conventional to define a rule named `all` at the top; try it! Add with `nano -ET4 Snakefile`,

```
rule all:
    input:
        "variants.vcf"
```

Question: why specify the _input_ for this rule, and no output or shell command!?

(Yeah, it's just a cute trick that matches the way computers think.)

### End of class recap

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands
* You can "decorate" the rules to tell snakemake how they depend on each other.
* This decoration comes in the form of "input:" and "output:" lists of one vor more files, quoted, separated by commas.
* and this is how you connect rules: by saying which rules take which files as inputs and/or produce what outputs.
* Snakemake cares about tabs :)

...and why is this all useful, anyway? We'll explore that in a bit more detail next Friday!
