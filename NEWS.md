# inferRecom 0.99.4

## Breaking changes

* `xoPhase()` now takes `REF` from PLINK allele 2 (BIM column 6) and `ALT`
  from allele 1 (BIM column 5), the convention of plink2 and of PLINK 1.9
  with `--keep-allele-order`. Before, `REF` and `ALT` were the other way
  round, so for data converted from a VCF with plink2 every `REF` was the
  alternate allele and every `GT` code was inverted. The phased alleles
  themselves are unchanged.

## New features

* `xoPhase()` phases SNPs where both parents are heterozygous, which were
  left unphased. Homozygous children carry their only allele; each parent is
  phased from which of its haplotypes the homozygous children inherited at
  the nearest phased SNPs on both sides (left unphased if that evidence is
  missing or conflicting); heterozygous children are then phased from the
  haplotypes they inherited from the phased parents. On the example data this
  phases 1619 more genotypes. On simulated families with known haplotypes,
  all newly phased alleles were correct.

## Performance

* `xoPhase()` is much faster on chromosomes with many SNPs. It looked up every
  SNP in the BIM file and the genotype matrix by name, once per SNP, so the
  run time grew with the square of the number of SNPs. The rows are now found
  once for all SNPs. On a chromosome with 66,130 SNPs (chr21 of a
  whole-genome family data set) the run time fell from 186 s to 5 s, and the
  phased genotypes are unchanged.

## Other changes

* `hzRun()` calls the per-sample ROH detection directly, without an internal
  wrapper function.

## Documentation

* The package Description and the vignette describe the phasing of SNPs
  beyond the informative SNPs.

# inferRecom 0.99.3

## Breaking changes

* `xoPhase()` always returns a `VCF` object (VariantAnnotation), with phased
  `GT` genotypes for every family member and sample information from the FAM
  file in `colData()`. The `outputFormat` and `vcfOutput` arguments are
  removed; use `VariantAnnotation::writeVcf()` to write VCF files. Families
  that cannot be phased are omitted with a message.
* `hzRun()`, `xoDetect()` and `xoPhase()` use UCSC-style chromosome names
  (e.g. `chr4`), so their results can be overlapped and displayed together.
* `caseControl = TRUE` uses standard PLINK phenotype coding: 2 = affected
  (case); 1 = unaffected or missing (control). Previously 1 was a case.
* VariantAnnotation replaces SummarizedExperiment in Depends, and R >= 4.6.0
  is required.

## Bug fixes

* `xoDetect()`: `snpFilter` had no effect. A crossover reversed in the same
  child within fewer than `snpFilter` informative SNPs is now removed
  together with its reversal.
* `xoDetect()`: PLINK paths given with a `.bed`, `.bim` or `.fam` extension
  are now accepted.
* `hzRun()` and `xoDetect()` with `caseControl = TRUE` return a
  `GRangesList`, as documented, instead of a list.
* `xoPhase()`:
    * used the crossover data from `xoDetect()` only under the wrong column
      names, so a parent could be phased from a child with crossovers;
    * no longer discards informative SNPs where a parent is homozygous for
      allele 1;
    * phases SNPs where the parents are homozygous for different alleles;
    * at SNPs left out of the informative set because of a missing or
      inconsistent genotype, infers children's alleles from their genotypes,
      and the heterozygous parent's phase from the haplotypes the children
      inherited on both sides, instead of copying alleles from a neighbouring
      SNP. Children with inconsistent genotypes are left unphased.

## Other changes

* All functions check that the PLINK `.bed`, `.bim` and `.fam` files exist,
  and `xoDetect()` now validates all of its arguments. `NA` is rejected for
  logical and numeric arguments.
* Internal code reorganised: shared helpers, no `for` loops, functions of 50
  lines or fewer, and formatting with `biocthis::bioc_style()`.

## Documentation

* Vignette: new introduction and references; the example data are described
  as simulated with sim1000G from HapMap CEU genotypes; code is marked up and
  `sessionInfo()` evaluated; the control track in the case/control plot
  shows the control intervals.
* `inst/scripts` documents how the example data were produced.
* Data documentation has `\value` sections and corrected sources.

## Data

* `simCEU.fam` uses standard PLINK phenotype coding (cases 2, others 1).
* `xoPat.rds` and `xoMat.rds` are regenerated with current class
  definitions; their crossovers are unchanged.

# inferRecom 0.99.0

## New Features

* Initial Bioconductor submission
* `xoDetect()` - Detect meiotic crossovers in family data
* `hzRun()` - Identify runs of homozygosity
* `xoPhase()` - Phase haplotypes using crossover information

## Data

* Example dataset `simCEU` with PLINK files and genetic maps
