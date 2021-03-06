
<!-- README.md is generated from README.Rmd. Please edit that file -->
Instructions of the usage of rBS2ndStructure
--------------------------------------------

This package provides thermal stable RNA 2ndary structures predicted on full length transcripts mm10 and hg19. The predicted structures are used to filter the bisulfite sequening sites that have potentially incomplete bisulfite conversion on them.

-   First, install the package in R

``` r
devtools::install_github("ZW-xjtlu/rBS2ndStructure")
library(rBS2ndStructure)
```

-   Then, you could use the following data containing the ranges of the structures provided by the package. Use `?` to check their documentations.

``` r
library(GenomicRanges)

?rBS2ndStructure::Struc_hg19

?rBS2ndStructure::Struc_mm10
```

-   You could directly use the provided GRanges object to filter your sites.

``` r
rBS_gr = GRanges(seqnames = rBS_df$`#SeqID`, strand = rBS_df$refStrand, ranges = IRanges(start = rBS_df$refPos, width = 1))
rBS_gr_filtered <- rBS_gr[!rBS_gr %over% Struc_hg19]
```

-   Alternatively, you could try to filter the sites overlapped with RNA secondary structures with the function `rBS_2ndStructure_Filter`, the function also helps you mask all the sites on introns and intergenic sequences.

``` r
rBS_gr = GRanges(seqnames = rBS_df$`#SeqID`, strand = rBS_df$refStrand, ranges = IRanges(start = rBS_df$refPos, width = 1))
rBS_gr$mcols = p.adjust(rBS_df$`p-value_mState`,method = "BH") < .05
rBS_gr_filtered <- rBS_2ndStructure_Filter(rBS_gr,"hg19")
```

-   Finally, you could try to predict RNA 2ndary structures of your own transcriptome with RNAfold of viennaRNA package installed on your system.

``` r
#Extract transcript sequences in fasta files from BSgenome and txdb

library(BSgenome.Mmusculus.UCSC.mm10)
library(TxDb.Mmusculus.UCSC.mm10.knownGene)
txdb <- TxDb.Mmusculus.UCSC.mm10.knownGene
tx_seq_extraction(MMusculus,txdb,getwd()) 

#Run RNAfold in multiple computational clusters 

RNAfold(1:30,"Small","Fsm") 
RNAfold(1:30,"Middle","Fmd") 
RNAfold(1:30,"Large","Flg") 

# Assemble the predicted transcripts

rfold_assembly_tx(txdb,RfdNames = c("Fsm","Fmd","Flg"))
```

-   Notice that you could run `RNAfold` on command line by yourself, the function `RNAfold` only works if you implement `qsub` command on your computer system.
