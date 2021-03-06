ArrayMaker
==========

Version 1.1

Creates transposed PED files of SNP genotypes at chosen loci from BAM files of mammalian whole genome sequence data

This program generates SNP calls from whole-genome alignments (BAM format) and outputs them in a transposed PED (tped) format. It was designed to create tpeds for genotype calls at any list of markers in BED format (currently only SNPs) for any species with a reference genome sequence. The program assumes the autosomes are designated in consecutive numeric order, as in human etc. Provided with the program are example BED files for the Equine SNP50 and SNP70K arrays and the Canine HD array (canFam2 and canFam3 coordinates). Calls can be made in the top (http://res.illumina.com/documents/products/technotes/technote_topbot.pdf) or forward call orientation. 

The program runs on a Linux operating system and requires SAMtools version 1.18 or later. SAMtools can be freely downloaded from SourceForge or GitHub. The SAMtools-indexed reference sequence to which the samples were aligned must also be available. 

Arguments are parsed to the program on the command line using the format argument=value. Arguments with no default that were failed to be read from command line will be prompted. 

Arguments:

*	chrs: number of autosomes in the species of interest. Required for renaming of the sex chromosomes to numerals to be consistent with array-generated calls on sex chromosomes (consistency is essential if array and ArrayMaker calls are to be merged). Chromosome X will be indicated in the tped file as (last autosome + 1) and Chromosome Y as (last autosome + 2).  
*	strand: top or forward. Case insensitive.
*	mincov: minimum number of good quality reads covering the SNP site to call a SNP. For low-pass sequencing, 4 gives generally reliable calls. 
*	maxcov: maximum number of good quality reads covering the SNP site to call a SNP. Setting to approximately double the average mapped coverage helps to prevent calls in duplicated sequence. 
*	str: stringency of calls. Default stringency applies all recommended parameters for SAMtools mpileup plus E and C 50. Low disables BAQ calculation, sets the prior probability of any genotype to 1, and allows reads included in improperly mapped pairs to contribute to calls. Low is useful for increasing the call rate in low-pass sequencing. Indel calling is skipped. See SAMtools manual reference pages for more details on these parameters. Default value: high. 
*	bam: a list of sorted BAM files from which to call SNPs. Full path to file should be included. One BAM per line, one BAM per sample. The order of the samples in this list of BAM files will be the order of the sample columns in the output tped. 
*	bed: BED formatted list of SNPs. Any list of markers can be used if formatted as column 1 = chromosome/sequence name, column 2 = SNP position, column 3 = genotype in format [A/G]. The third column is optional, but is essential if top calling orientation is desired, and also to enable the called genotypes to be checked for concordance with expected alleles. If a bed file is provided without the fourth column/genotype information, please select forward calling and disable genotype checking (nocheck=true). Provided with program are array BED files for the Equine SNP50K, Equine SNP70K, Canine HD array in coordinates canFam2 and canFam3.
*	out: output file name stub. This name will be used for the VCF and tped files generated.
*	ref: reference sequence to which the samples in bamList were aligned. Please make sure the SAMtools faidx index is also available. 
*	sam: path to SAMtools on this machine Please be sure to indicate full path terminated by samtools command, ie /home/programs/samtools-0.1.18/samtools.
*	bcf: path to bcftools on this machine. Bcftools is found within the SAMtools directory. eg /home/place/samtools-0.1.18/bcftools/bcftools.
*	tfam: if true, a dummy ntfam is created using file names in bam list as family and individual IDs, while gender, affection status and parental IDs are 0. Tfam file is given the prefix designated in *out* and suffix .tfam. 
*	prefix: prefix of chromosomes/sequence names in alignment. If samples were aligned to a reference genome fasta which includes a chromosomal prefix, ie >chr1 or >chrom1 instead of >1, indicate chr or chrom as required. Default is no prefix.
*	add: if a chromosomal prefix is required and the marker list has no prefix (for example the supplied array BED files), setting add to 'true' creates a marker list with the appropriate prefix. Leave as false (default) if the alignment has no prefix, or if the BED file already has the required prefix.
*	nocheck: Do not check that the genotypes observed match the alleles expected from the input list of SNPs. It is recommended to check genotypes for array manifests (default is to check) but this requires the array BED file to have the expected base information. If using this program to produce calls at a list of sites for which the expected alleles are unknown, user can provide these sites in plain BED (chr, pos -1, pos) format. Note that if nocheck is set ('true' or 'yes'), only forward calling can be performed. Due to the nature of multi-sample pileup, no triallelic sites will be present in the tped but if the data is to be merged with another dataset (for example merging this output tped with some array data in PLINK), absence of checking against expected alleles may result in triallelic sites in the merged file. If checking is to be performed (default), the alternate homozygotes or heterozygotes displaying an unexpected allele will be downgraded to missing genotype status. The genotyping rate will thus be higher with nocheck.
*	vcf: make a new pileup and output in VCF format. Default value is true. If set to false, the program will bypass pileup and move straight to the tped generation stage. This is useful if the intended VCF has already been generated (the most timely part of the program, from around 30 minutes to a few hours depending on sample numbers, coverage and machine specs) and the tped is to be rerun with different parameters, for example changing the minimum and maximum per base coverage for calling or changing whether or not genotypes are to be checked. A VCF produced in 'high' stringency can be re-run with low calling stringency and vice-versa, but note that there may be slight differences in the output compared to when the same stringency is applied to both VCF generation and genotype calling. The program will expect the VCF to have the same filename stub as indicated in 'out' and to be suffixed with .vcf.

There are 6 possible versions of the genotype calls produced:
*	High calling stringency, check against expected alleles, forward calling strand 
*	High calling stringency, check against expected alleles, top calling strand
*	High calling stringency, no check against expected alleles, forward calling strand 
*	Low calling stringency, check against expected alleles, forward calling strand 
*	Low calling stringency, check against expected alleles, top calling strand
*	Low calling stringency, no check against expected alleles, forward calling strand
Default is high stringency and checked genotypes. There is no default value for calling strand. 
