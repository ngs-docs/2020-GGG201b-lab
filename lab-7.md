---
tags: ggg, ggg2020, ggg201b
---
# GGG 201b - lab 7 - Variant calling and assembly workflows discussion

*Feb 21, 2020*

[Variant calling workflow](https://github.com/ngs-docs/2020-ggg-201b-variant-calling/blob/master/Snakefile)

[Assembly workflow](https://github.com/ngs-docs/2020-ggg-201b-assembly/blob/master/Snakefile)

what does each workflow do?
how do we know they worked?
- computer saying "no errors happened!" is usually necessary but not sufficient
- need to examine outputs, information loss, and have evaluation metrics
- controls on whole process - how?

variant calling:
* look at mapped reads & some regions
* find low coverage regions
* count and/or look at unmapped reads
	* (technique for looking at medium data: take 10 randomly selected results, e.g. unmapped reads, and ask what they are!)

assembly:
* how does megahit work? what does it do?
* (what if megahit just output all the reads again?)
* try mapping reads
* look for known genes
* (black box view)
* how many k-mers don't show up in final? how abundant are they?
* assemblathon result; usually a tradeoff between various parameters

big data is kind of like wet bench experiments: you often can't look at it directly, so you need to develop indirect ways of doing quality checks.
- summary statistics for various stages
- information loss estimates
- visualization
- pos/neg controls
- can we do hold-out / train+test+validate? not so easily in some cases.

