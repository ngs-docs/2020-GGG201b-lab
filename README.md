# 2020-GGG201b-lab

Materials from Intro to Bioinformatics lab, Winter offering at UC Davis (Jan-Mar 2020)

<p xmlns:dct="http://purl.org/dc/terms/" xmlns:vcard="http://www.w3.org/2001/vcard-rdf/3.0#">
  <a rel="license"
     href="http://creativecommons.org/publicdomain/zero/1.0/">
    <img src="http://i.creativecommons.org/p/zero/1.0/88x31.png" style="border-style: none;" alt="CC0" />
  </a>
  <br />
  To the extent possible under law,
  <a rel="dct:publisher"
     href="http://twitter.com/ctitusbrown">
    <span property="dct:title">Titus Brown</span></a>
  has waived all copyright and related or neighboring rights to
  <span property="dct:title">GGG 201(b), Winter 2020 at UC Davis</span>.
This work is published from:
<span property="vcard:Country" datatype="dct:ISO3166"
      content="US" about="http://twitter.com/ctitusbrown">
  United States</span>.
</p>


## Code of Conduct

Please abide by [my lab's Code of Conduct](http://ivory.idyll.org/lab/coc.html) in this course.

In particular, this is not an intellectual contest, and please realize that we all have plenty of things to learn.

## Lab sessions

What: hands-on computational work
When: Friday 9-10:50am
Where: Shields Library dungeon

## Homework

There will be three homeworks, submitted via GitHub Classroom. Details TBA.

Homeworks can be done collaboratively, but you need to hand it in individually.

## Grading and collaborative work

I grade each homework P/F. The division of grading between labs and the rest of the course is in the whole course syllabus.

## Office hours

Office hours to meet with Titus will be from 3-5pm on Wednesdays in CCAH 251; please use [this online signup sheet](https://calendly.com/ctitusbrown/office-hours). Don't worry too much about the specific time, this is just to signal to me that you want to talk to me (and puts it on my calendar!). (If no one signs up by 1pm of that Wednesday, I will feel free not to show up!)

Note that Titus is busy on 1/15 between 3-5pm, and out of town on 2/26.

## Instructors

C. Titus Brown (IOR) (<ctbrown@ucdavis.edu>)

## Lab description

In this lab, we will build and examine three different automated workflows, for three common bioinformatics tasks: variant calling, genome assembly, and RNAseq differential expression. (These will not be cutting edge workflows and should not be directly used for your own work, but they will be complete and functional.)

The overall learning goals for the lab are to:

1. familiarize you with the basic operational concepts involved in variant calling, genome assembly, and RNAseq differential expression.
2. introduce the use of workflow management tools as a core aspect of biological data analysis.
3. describe scientific issues surrounding data analysis techniques and processes, including statistical issues, reproducibility, provenance, and publication.

In terms of technology, we'll be using the snakemake workflow system, running on the farm cluster. We'll touch briefly on git/GitHub and conda, but those topics will be gone into in much more detail in [GGG 298](https://github.com/ngs-docs/2020-GGG298/).

## Schedule of lab topics

1. 1/10 - [Intro; variant calling 1](lab-1.md)
2. 1/17 - [Variant calling 2 - now with more snakemake!](lab-2.md)
3. 1/24 - [Variant calling 3 - conda, github, and snakemake](lab-3.md); [HW 1 assigned](lab-hw-1.md).
4. 1/31 - [HW 1: Variant calling, due](lab-hw-1) --- TODAY'S NOTES: [Genome assembly 1](lab-4.md)
5. 2/04 - BONUS LECTURE on assembly! [Outline and collaborative notes](NOTES-de-novo-assembly-lecture.md)
6. 2/07 - [Genome assembly 2](lab-5.md)
7. 2/14 -  [Genome assembly 3](lab-6.md)
8. 2/18 - BONUS LECTURE on transcriptomics! [Outline](NOTES-transcription-lecture.md)
9. 2/21 - [Variant calling and assembly workflows discussion](lab-7.md)
10. 2/28 - [HW 2: Assembly, due](lab-hw-2.md) - [RNAseq diff exp 1](https://github.com/ngs-docs/2020-ggg-201b-rnaseq)
11. 3/06 - [RNAseq diff exp 2](lab-9.md)
12. 3/13 - RNAseq diff exp 3

