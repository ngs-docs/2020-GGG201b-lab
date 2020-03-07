---
tags: ggg, ggg2020, ggg201b
---

# GGG 201b, jan 2020 - lab 3 - NOTES

[toc]

## Friday Lab Outline - 1/24

You can do collaborative note taking [here](https://hackmd.io/cZ9ehhV7TQONFu5EYx2JgA) if you like!

Outline:
* even more on variant calling
* even more on snakemake
* discuss "real" variant calling on big genomes (GATK, etc.)

## Learning goals

* Start using farm, github, and conda.
* Learn about default rules.
* Learn about wildcards.

## Day 3: More variant calling with more snakemake

### Log into farm

Using the username/password I provided, log into farm.

You should end up at a prompt that looks like this:

```
(base) USERNAME@farm:~$ 
```

the (base) means you've installed conda, USERNAME is your username, 'farm' is the name of the computer, and `~` is your location - ~ means your home directory. The `$ ` indicates you're at the command line prompt.

### Clone a repository from github

Let's start by taking the binder repository from last week and making a local copy in your account:

```
git clone https://github.com/ngs-docs/2020-ggg-201b-variant-calling variants/ -b week3
```

This takes the data at https://github.com/ngs-docs/2020-ggg-201b-variant-calling, in the week3 branch, and makes a local copy in the directory `./variants/`. You can do `ls` to look at this directory:

```
ls ~/variants/
```

You should see that there's a `Snakefile` in there. That's what we've been working with in previous weeks.

### Installing necessary software with 'conda'

Before doing anything else, we need to install some software. Conveniently that's something that conda can do for us all in one fell swoop -- run

```
conda env create -f variants/binder/environment.yml
```
which will take ~3 minutes.

This will install [the packages listed in this file](https://github.com/ngs-docs/2020-ggg-201b-variant-calling/blob/master/binder/environment.yml).

If you want to know more about conda, please see [this week's GGG 298 tutorial](https://hackmd.io/@ctb/BkbkefV-U).

## Back to snakemake and actual variant calling.

(Every time you log in you'll need to activate a conda environment and change to the right directory. For this week, we want to be in the `vc` environment and the `variants/` directory.)

Activate the conda `vc` environment, which was created by the previous conda command:

```
conda activate vc
```

and change to the `variants/` directory:

```
cd ~/variants/
```

Your prompt should now look like this: `(vc) USERNAME@farm:~/variants$ ` reflecting the new environment (vc) and the location (~/variants/).

### Note new data copy rule in Snakefile

Because we're all working off of `farm`, we have a shared filesystem! So now instead of downloading the data we can just link it in from one copy, using `ln -s`.

([You can see all of the differences in the Snakefile between last week and this week here](https://github.com/ngs-docs/2020-ggg-201b-variant-calling/compare/week3)).

### Running lots of commands all at once

Last week, we fixed the rules `index_genome_bwa` and `map_reads`; I added the right input and output to the two remaining rules as well. So now you should be able to run everything up to the rule `make_vcf` by just specifying `make_vcf` - try it!

```
snakemake -p make_vcf
```

### Re-running everything

You can tell snakemake to delete everything it knows how to make for a particular rule (including all preceding rules) by running
```
snakemake --delete-all-output make_vcf
```

(This can also be a good way to check to make sure you have all the output: information right, because you'll have files left over if you forgot to put them in output: :)

### Using filenames instead of rule names

You don't actually need to use the rule names (this will be important later on!). You can just tell snakemake what file you want produced, and run that.

So:
```
snakemake -p variants.vcf
```
will also work to run the rule `samtools_sort`, but you don't have to remember the rule name (which can be arbitrary...)

### Running the whole workflow!

And now you should be able to run `snakemake -p make_vcf` and have it all work!

But it's kind of annoying that you have to specify `make_vcf`, isn't it? Can we make that default as well?

### Create a good "default" rule

By default, if you don't specify a rule snakemake runs the first rule in the file.  (This is actually the only case where the order of rules in the Snakefile matters!)

So it's conventional to define a rule named `all` at the top; try it! Add with `nano -ET4 Snakefile`,

```
rule all:
    input:
        "variants.vcf"
```
(Reminder: use CTRL-X, y, ENTER to exit the nano editor and save things)

Question: why specify the _input_ for this rule, and no output or shell command!?

Answer: there's no real answer. It's just a cute trick that matches the way snakemake thinks; snakemake knows the first thing it needs to do for a rule is find the inputs, so this `all:` rule forces it to build the inputs. Then there's nothing else for snakemake to do so it claims success!

This turns out to be an important distinction, though. As you'll see with wildcards, below, we'll end up having two kinds of rules in the Snakefile: what I'll call "concrete" rules, and what I'll call "abstract" rules. Abstract rules are generic recipes - "here's how you cook spaghetti." Concrete rules are specific directives: "make me this much spaghetti, please."

### Looking at VCF files

A reminder that we are actually doing bioinformatics in all of this :).

Let's look at the output:

```
cat variants.vcf
```

### Looking at alignments

and we can dig into any given alignment by running:
```
samtools tview -p ecoli:4202391 SRR2584857.sorted.bam ecoli-rel606.fa
```

## "Real" variant calling, digression

I feel compelled to mention that this is not the variant calling workflow you would necessarily use. 

Many people do indeed use samtools, but if you may also want to look into [GATK](https://gatk.broadinstitute.org/hc/en-us) if you have a big or complex genome.

You should also look into the various parameters used for mapping, mpileup, and so on.

I'm happy to chat about this, but I'm not really an expert; I do have experts in my lab, though, and there are many on campus!

### Digression in a digression: MAD

You might be interested in [Meet and Analyze Data](https://mad.oxli.org). More on that in a bit...

## Next workflow steps: more snakemake

### Using wildcards: `{sample}`

A big problem right now is that all our rules are tied to a specific filename. Let's fix that:

In the rule map_reads, change things to:

```
rule map_reads:
    input:
        "ecoli-rel606.fa.amb",
        "{sample}.fastq.gz"
    output:
        "{sample}.sam"
    shell:
        "bwa mem -t 4 ecoli-rel606.fa {wildcards.sample}.fastq.gz > {wildcards.\
sample}.sam"
```

### Changing outputs, too

The one remaining problem with the outputs is that the final rule specifes a single `variants.vcf` when what we really want is `{wildcards.sample}-variants.vcf` -- one for each input sample.

Fix it to read like this:

```

rule make_vcf:
    input: "{sample}-variants.raw.bcf"
    output: "{sample}-variants.vcf"
    shell: "bcftools view {wildcards.sample}-variants.raw.bcf > {wildcards.samp\
le}-variants.vcf"
```

## Wildcards, in summary

* In the 'input' and 'output' tags, put wildcard terms in curly brackets, e.g. `{sample}` or `{reads}`.
* In the shell command, use matching wildcard terms in like so: `{wildcards.sample}` or `{wildcards.reads}`
* wildcards operate entirely within a single rule, not across rules.
* you will need (somewhere) to provide them snakemake with a concrete rule that asks for the wildcard rules to be applied, e.g. if you have a rule that says "produce a `.sam` file from a `.fastq.gz` file" somewhere you need to ask for a specific `.sam` file.


### Q: What if I get a workflow error?

If you get the error


>WorkflowError:
>Target rules may not contain wildcards. Please specify concrete files or a rule without wildcards.
>
then what's happening is you're trying to directly run a rule with a wildcard in it (i.e. an abstract rule), and snakemake can't figure out what the wildcard should be; instead, ask snakemake to build a specific file.

For example, with the rule immediately above that adds wildcards to `map_reads`,
```
snakemake map_reads
```
will complain about the target rule containing wildcards. You should instead run
```
snakemake SRR2584857_1.sam
```
which snakemake can use to figure out what the wildcards should be.

An alternative to specifying the file on the command line is to put it in the default rule, e.g. `rule all:` (see [the section on default rules](https://hackmd.io/cGYzxz07SseGxH0y2gjYJw?view#Create-a-good-%E2%80%9Cdefault%E2%80%9D-rule)) and then you can run `snakemake`.

### A quick checklist:

* Are you asking snakemake to create a _specific_ file?
    * either by executing `snakemake <filename>`
    * or by specifying a rule that has an input or an output without wildcards,
    * or by providing a default rule?
* for any rule that you expect to be executed automatically (because some other rule needs its output), have you specified `output:`?