---
tags: ggg, ggg2020, ggg201b
---
# GGG 201b, feb 2020 - lab 6 - Assembly 3

## Variant calling - homework #1, and updated snakefile

For HW #1, see [variant calling Snakefile](https://github.com/ngs-docs/2020-ggg-201b-variant-calling/blob/master/Snakefile)

### Parts of the solution

(Let's ignore the SAMPLES and expand stuff at first.)

A couple of notes:

* the rule 'all' needs to contain, as inputs, the four desired VCF files.
* the rule 'copy_data' should copy in all four files. Here we're doing something tricky to limit the sample wildcards to things that don't have '/' in them; this matches what people _expect_ wildcards to do. See [constrains wildcards](https://snakemake.readthedocs.io/en/stable/tutorial/additional_features.html#constraining-wildcards) for more information here. (What happens if we remove the constraint? WEIRD STUFF.)
* Also note in rule copy_data the use of `{input}` and `{output}`. These are replaced by the filename(s) in the input: and output: block; if there are multiple files in those blocks, they are space separated.
* Since I stupidly _failed_ to teach you the use of `{input}` and `{output}`, the main trick that you needed to use was to put `{wildcards.sample}` in the shell: commands as appropriate; a few pointers:
    * each wildcard is specific to each rule, so you can use whatever wildcard you want in each rule as long as it's used consistently. `sample`, `sample5`, whatever.
* note rule `map_reads` is kinda tricky. Why does the `input:` not match the command line? (Same with `samtools_index_sorted`). (Reminder: these commands don't have to make sense to _you_, they need to make sense to the _computer_. The goal here is for the computer to generate the .amb and .bai files that are needed for the commands :)

### On to `expand`

So, in the homework, you need to put:

```
rule all:
    input:
        "SRR2584403_1-variants.vcf",
        "SRR2584405_1-variants.vcf",
        "SRR2584404_1-variants.vcf",
        "SRR2584857_1-variants.vcf"
```

which looks ...pretty repetitive, and also prone to typos. It turns out this is a pretty common pattern: you have a list of different names, and you want to make the same output filenames for each name.

So, snakemake provides a way to do it with `expand`, like so:

```

# list out samples
SAMPLES=['SRR2584403_1',
	 'SRR2584405_1',
         'SRR2584404_1',
         'SRR2584857_1']

rule all:
    input:
        # create a new filename for every entry in SAMPLES,
        # replacing {name} with each entry.
        expand("{name}-variants.vcf", name=SAMPLES)
```

this is actually the _inverse_ of the wildcard matching. You're _producing_ filenames that snakemake can use to fill in the wildcard matching in the other rules.

You'll need to use this for your homework, so... any questions? :)

## Update your assembly workflow

Log into farm; then change into the assembly repo from [last week](https://hackmd.io/_oLNDy9hTBOCZ2wffXJodQ?view), discard changes to farm-queue.sh, and update it:

```
cd ~/
cd assembly-repo/
git checkout farm-queue.sh
git pull
```

Now try running it to make sure it works,

```
bash farm-queue.sh
```

If it _does_ look good, then you're ready to run it in the queue - edit the file and remove the `-n` from the snakemake command, and submit it to the queue with

```
sbatch farm-queue.sh
```

### Some slurm commands and interactions

* sbatch - submit shell script to queueing system
* squeue - list everything in the queue. run `squeue -u $USER` to see all the things submitted.
* scancel - `scancel JOBID` kills the running job. `scancel -u $USER` kills all your running jobs.

Log files are written progressively to `slurm-JOB.out`. Run `tail -f slurm-JOB.out` (replace JOB with your job ID!) to see what your program is doing; use <kbd>CTRL-C</kbd> to get out of tail.

## Snakemake workflow revisited

OK! Let's look at the Snakefile we're running!

It has three main steps. The first step is to run the [megahit assembler](https://github.com/voutcn/megahit/). The second step is to run the [prokka](https://github.com/tseemann/prokka) prokaryotic genome annotation pipeline. And the third is to run [quast](http://bioinf.spbau.ru/quast), which provides statistics on genome assemblies.

Before we get into the details of what those do, let's talk about the Snakefile.


* rule `assemble_data`, in the `input:` block, what is this `r1` and `r2` stuff? If you have multiple inputs, you can name them here and then use them as `{input.name}` in the shell command. I should have taught you this a few weeks ago because it's super cool!
* rule `assemble_data`, what's this `threads:` block? It tells snakemake to use up to that many cores, where the number of cores snakemake is ALLOWED to use for this rule is given by the `-j` parameter, and also to Slurm in the sbatch script with `-c 8`. (Yes, complicated.) This info is then passed into the shell command with `{threads}`, which will be between 1 and 8 in this case.
* rule `assemble_data`, what's this `params:` block? This is a new block where you can put parameters that you might want to adjust in various ways. 
* rule `assemble_data`, what's this `directory(...)` thing? It tells snakemake that the output is a whole directory.
* The conda directive! What's that??
* 
### What does quast do?

tl;dr reports on assemblies! Check out `rel606.quast/report.txt`.

The numbers you care most about are total length, which should be large:
```
Total length (>= 1000 bp)   4542425       
```
and number of long contigs, which should be small:
```
# contigs (>= 1000 bp)      92            
```
(92 is not small, but maybe it's the best we can do with this data...)

This indicates that we are getting a lot of the genome but in not super large fragments.

For your homework, your goal will be to look at a bunch of these quast reports and figure out WHERE the assembly breaks down when you subsample the data!

### Subsampling!

For your homework, I've included subsampling instructions. The key bit is here, in rule `subsample_reads`:

```
(gunzip -c {input} || true) | head -{wildcards.num_reads} | gzip > {output}
```
where we extract num_reads (really num_lines...) from the input read data set, and save it to the output.

How does this work in terms of the rule `all:`?
