---
tags: ggg, ggg2020, ggg201b
---

# Transcriptomics lecture - GGG 201(b), 2/18

eukaryotic transcription, polII - transcription initiation, nRNA, splicing, export to cytoplasm, translation
* first point of control is transcription, in most organisms
* second is splicing
* third is rna degradation (managed by micrornas, and/or proteins)
* fourth is translation rate which I think is mostly ribosome controlled
* fifth is degradation / post-transcriptional modification

in development and differentiation, tissue specific gene expression is one of the main ways cell identity seems to be determined. this may also be true of cell _activity_. so gene regulation becomes important, and gene regulatory networks become important.
* a regulates or represses b, usually in combination with (many) other genes
* signaling often comes down to dna binding proteins / transcription factors responding to (e.g. hormones, notch + delta => suppressor of hairless, wnt + beta-catenin)

measuring RNA expression:
* idea is that if you sequence deeply, you can get "digital" expression - basis of RNAseq, ChIP-seq
* isoform reconstruction is more challenging and require longer-range methods (paired ends, long reads)
* _lots_ of noise in RNAseq...
* if no isoforms (bacteria, archaea) => map reads to genes, you're good
* however, if you have isoforms, it becomes more challenging.
* programs that are used: salmon/kallisto for mapping+quantification, THEN a differential expression analysis with deseq2, edger, or limma/voom.
* how you do study design and batch effects (next week)

cis-regulation (cis as in "near", e.g. cis-lunar, as opposed to trans, "far", e.g. trans-lunar)
* proteins targeted to genes by dna/protein interactions (t.f.s) and cofactors
* usually a complex of multiple co-factors
* one big determining factor is dna-protein binding
* these enhancer complexes reach out to the basal promoter and initiate transcription
* the DNA components (enhancers) can be anywhere in gene region, up to MB away;
* usually within intergenic or intron (as opposed to bacteria)

how to find cis-regulatory elements:
* bioinformatically, looking for binding sites doesn't really work; highly degenerate (any given protein matches to many DNA sequences)
* can look for conservation between species and then test them (I did this for a while)
* can also use experimental approaches, in particular ChIP-seq or look for particularly chromatin accessible regions
* "ATAC-seq" and other approaches look at chromatin accessibility
* in vivo approaches can work by tethering transposases to particular proteins
* most of these _predict_ potential binding  but often validation must still be done in vivo.