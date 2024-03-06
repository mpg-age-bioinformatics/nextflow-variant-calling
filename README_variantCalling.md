## Variant Calling analysis

**NOTE: RUNS GOOGLE DEEPVARIANT. THEIR MODELS WERE TRAINED ON HUMAN DATA; OTHER ORGANISMS CAN BE USED; IF RESULTS ARE UNEXPECTED PLEASE REFER TO THE DEEPVARIANT DOCUMENTATION. ONLY DETECTS VARIANTS > 10% AF**

### Requirements:

  - DNA-Seq from mutated samples (WGS and WES tested, RNAseq potentially possible)
  - optional: matched unmutated WT sample(s)
  - In case of exome sequencing, a target exon bed file

### Pipeline:

  1. reads are aligned using `bwa-mem` and `samtools`
  2. SNPs are called using GOOGLE's `deepVariant` version 1.1.0 (only keep variants flagged as **PASS**)
  3. percentage of reads mapped to targetd exome is quantified using `featureCounts`
  4. if WT sample is available, remove mutations found in WT from all mutated samples
  5. run `snpEff` version 4.3.t to predict functional effect of mutation (keep only protein coding mutations of high or moderate effect)
  6. merge samples and reformat vcf format to an excel table

*depending on your organism and the size of the data the pipeline runs a couple hours*
  
### Outputs of the pipeline:

Results are locates in `snps` folders  

Explanation of the main table **[project].merged_results.xlsx**:  

| Column Name | Contents |
|---- |-----------|
| sampleID | sample ID the mutation if found in. If the mutation is found across multiple samples, the same position will appear in several rows with the associated samples |
| chrom | chromosomal location of mutation |
| pos | coordinate of mutation on chromosome |
| REF | reference allele |
| ALT | identified alternative allele |
| QUAL | `deepVariant` SNP quality score (see (here)[https://github.com/google/deepvariant/blob/14b690d970f6efb14e66bfead7ccb74103dbe469/docs/deepvariant-vcf-stats-report.md] for more information)| 
| sums | total number of reads at this position |
| smaller alternative al. | number of reads showing alternative allele |
| freq. (smaller alternative al.) (%) | ALT allele frequency in sample |
| ANN | snpEff annotation |
| sample name | user defined sample name |


### References

 - [bwa-mem](https://pubmed.ncbi.nlm.nih.gov/19451168/)
 - [samtools](https://pubmed.ncbi.nlm.nih.gov/19505943/)
 - [deepVariant](https://academic.oup.com/bioinformatics/article/36/24/5582/6064144)
 - [featureCounts](https://pubmed.ncbi.nlm.nih.gov/24227677/)
 - [snpEff](https://pubmed.ncbi.nlm.nih.gov/22728672/)

