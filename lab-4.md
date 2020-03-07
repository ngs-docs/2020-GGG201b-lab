---
tags: ggg, ggg2020, ggg201b
---

# GGG 201b, jan 2020 - lab 4 - NOTES

[toc]

## Friday Lab Outline - 1/31

### Submit homework?

Please put up stickies if you are having trouble with the homework submission process (git commit, git push).

### Test homework!

Let's all test our [homework](https://hackmd.io/5XPKWkOVTWGR2f2zyURS_A?view)!

Zeroth, Log into farm etc. etc.

First, activate vc environment.
```
conda activate vc
```

Second, `git clone` into a _new_ directory and change into that:
```
git clone YOUR_REPO_URL 201b-lab-hw1-TEST
cd 201b-lab-hw1-TEST/
```

Third, run `snakemake` in your directory.
```
snakemake
```
Does it work?

If it does, you can remove the directory like so:
```
cd ~/
rm -fr 201b-lab-hw1-TEST/
```
but you don't need to.

### Variant calling steps, explained

1. index genome for mapping
2. map reads -> alignments in SAM
3. index genome for samtools
4. convert SAM to binary format BAM
5. sort the BAM by mapped position in genome rather than by order in reads file
6. index the BAM
7. do pileup -> binary format BCF
8. do conversion from binary format to text format

Q:
* compare outputs for samtools_index_sorted and samtools_mpileup. Did Titus mess up? (what does samtools mpileup _require_ for inputs?)


### Assembly exercise!

(CTB to post PDFs to canvas :)

Working individually or in groups, please reconstruct the original text for the handouts I'm giving you. You may use the computer as you wish, but I suggest you avoid any attempts at programming...


Questions!
* what advantages do you have in this exercise?
    * language
    * grammar
* what are the pluses and minuses of the different datatypes?
* does the organization of reads matter?
* if you use a reference, how do you know it's the right reference?
    * (what strategies might you use to validate your assembly?)
* what strategy would you use if I told you you could have as many undergrads as you wanted to do this?