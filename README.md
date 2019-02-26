# CRISPRitz

CRISPRitz is a software package containing 5 different tools dedicated to perform predictive analysis and result assessement on CRISPR/Cas experiments.

![fig_pipeline_breve-1](https://user-images.githubusercontent.com/32717860/53018798-93e99700-3453-11e9-85a9-89eba18b18bb.png)

With this aim in mind we created this package, containing the following software tools:

- add-variants: Tool to encode genomic variants using the IUPAC code. The ouput is a set of files representing the genome enriched with variants in IUPAC notation form.
- index-genome: Tool to find the candidate targets in a genome starting from a PAM. The ouput is a set of files, containing all the sequences of candidate targets extracted from the genome.
- search: Tool to perform off-target search on a genome (with or without variants) or genome index (with or without variants). The ouput is a set of files, one is a list of all targets and off-targets found, the others are profile files containing detailed information for each guide , like bp/mismatches and on/off-targets count.
- annotate-results: Tool to annotate results found during search with functional annotations (promoter, chromatin accessibility, insulator, etc). The output is a set of files, one is the list of on/off-targets with the annotation type, the others are files containing counts for each guide, the counts are the total on/off-targets found with the specific mismatch threshold and the specific annotation.
- generate-report: Tool to generate a graphical report with annotated and overall mismatch and bulge profile for a given guide. The output is a graphical representation of the input guide behaviour.

# USAGE MANUAL

**SHOW THE HELP MENU**
```
python3 crispritz.py
help:
        crispritz add-variants <vcfFilesDirectory> <genomeDirectory>
        crispritz index-genome <name_genome> <genomeDirectory> <pamFile>
        crispritz search <genomeDirectory> <pamFile> <guidesFile> <outputFile> {-index} (flag to search with index-genome, allow searching with bulges) -mm <mm_num> [-bRNA <bRNA_num> | -bDNA <bDNA_num>] [-th <num_thread>] {-r,-p,-t} (write only off-targets results,write only profiles, write both)
        crispritz annotate-results <guidesFile> <resultsFile> <outputFile> -exons <exonsbedFile> -introns <intronsbedFile> -ctcf <ctcfbedFile> -dnase <dnasebedFile> -promoters <promotersbedFile>
        crispritz generate-report <guide> -mm <mm_num or range mm_min-mm_max> -profile <guideProfile> -extprofile <guideExtendedProfile> -exons <exonsCountFile> -introns <intronsCountFile> -ctcf <CTCFCountFile> -dnase <DNAseCountFile> -promoters <promotersCountFile> [-gecko (to use gecko pre-computed profile)] [-sumone <summaryReferenceCountFile>][-sumtwo <summaryEnrichedCountFile>]
```

**CREATE A VARIANT GENOME (*add-variants*):**
```
python3 crispritz.py add-variants <vcfFilesDirectory> <genomeDirectory>

**INPUT**
- <vcfFilesDirectory>, the directory containing the VCF files (.vcf or .vcf.gz).
- <genomeDirectory>, the directory containing the genome FASTA files (.fa).

**OUTPUT**
- set of chromosomes with SNPs, copy of the original chromosomes with variants SNPs
- set of chromosomes with INDELs, copy of the original chromosomes with variants INDELs
```

**CREATE A GENOME INDEX (*index-genome*):**
```
python3 crispritz.py index-genome <name_genome> <genomeDirectory> <pamFile>

**INPUT**
- <name_genome>, name of the directory that will contain all the .bin files
- <genomeDirectory>, the directory containing the genome FASTA files (.fa).
- <pamFile>, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).

**OUTPUT**
- index genome directory, directory containing the .bin files (one or more per input chromosome). Each file contains all the candidate targets found on the chrosome for the selected input PAM.
```

**SEARCH ON A GENOME INDEX WITH MISMATCHES AND BULGES (*search*):**
```
python3 crispritz search <genomeDirectory> <pamFile> <guidesFile> <outputFile> {-index} -mm <mm_num> [-bRNA <bRNA_num> | -bDNA <bDNA_num>] [-th <num_thread> (default: 1)] {-r,-p,-t}

**INPUT**
- <genomeDirectory>, the directory containing the genome FASTA files (.fa).
- <pamFile>, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).
- <guidesFile>, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- <outputFile>, first name of all the output files generated by the tool.
- index, flag set to perform search using the index genome (allow mismatches+bulges search).
- -mm \<mm_num>, threshold of accepted mismatches (i.e., the number of mismatches accepted to save the target in result files).
- -bRNA \<bRNA_num>, threshold of accepted RNA bulges (i.e., the number of RNA bulges accepted to save the target in result files).
- -bDNA \<bDNA_num>, threshold of accepted DNA bulges (i.e., the number of DNA bulges accepted to save the target in result files).
- -th <num_thread>, number of threads used by the tool (default uses all the available threads).
- -r,-p,-t, select the output files (-r: write only off-targets results, -p: write only profiles, -t: write both).

**OUTPUT**
- list of targets and off-targets, a text file containing all the targets and off-targets found on the genome with genomic coordinates.
- set of profile files, set of files containing detailed profile of every input guide, with the count of bp/mismatches and a grouped representation of on/off-targets found per every mismatch count and bulges count.
```

**SEARCH ON A GENOME WITHOUT INDEXING WITH MISMATCHES ONLY (*search*):**
```
python3 crispritz search <genomeDirectory> <pamFile> <guidesFile> <outputFile> -mm <mm_num> [-th <num_thread> (default: ALL)] {-r,-p,-t}

**INPUT**
- <genomeDirectory>, the directory containing the genome FASTA files (.fa).
- <pamFile>, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).
- <guidesFile>, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- <outputFile>, first name of all the output files generated by the tool.
- -mm \<mm_num>, threshold of accepted mismatches (i.e., the number of mismatches accepted to save the target in result files).
- -th <num_thread>, number of threads used by the tool (default uses all the available threads).
- -r,-p,-t, select the output files (-r: write only off-targets results, -p: write only profiles, -t: write both).

**OUTPUT**
- list of targets and off-targets, a text file containing all the targets and off-targets found on the genome with genomic coordinates.
- set of profile files, set of files containing detailed profile of every input guide, with the count of bp/mismatches and a grouped representation of on/off-targets found per every mismatch count and bulges count.
```
**EXAMPLE OF OUTPUT LIST OF TARGETS AND OFF-TARGETS**
![example_targets](https://user-images.githubusercontent.com/32717860/53101471-19d81180-352a-11e9-9ee3-69de580c5e3f.PNG)

**EXAMPLE OF OUTPUT PROFILE FILE**
![example_profile](https://user-images.githubusercontent.com/32717860/53101468-180e4e00-352a-11e9-9bfb-31428cee7475.PNG)

**EXAMPLE OF OUTPUT MOTIF MATRIX FILE**
![example_motif_matrix](https://user-images.githubusercontent.com/32717860/53101466-1775b780-352a-11e9-84e8-0013656ad163.PNG)



**ANNOTATE RESULTS OBTAINED IN A PREVIOUS SEARCH PHASE (*annotate-results*):**
```
python3 crispritz.py annotate-results <guidesFile> <resultsFile> <outputFile> -exons <exonsbedFile> -introns <intronsbedFile> -ctcf <ctcfbedFile> -dnase <dnasebedFile> -promoters <promotersbedFile>

**INPUT**
- <guidesFile>, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- <resultsFile>, path to the text file containing on/off-targets generated by previous search phase.
- <outputFile>, first name of all the output files generated by the tool.
- -exons <exonsbedFile>, path to the .BED file containing intervals of exon regions in the genome.
- -introns <intronsbedFile>, path to  the .BED file containing intervals of intron regions in the genome.
- -ctcf <ctcfbedFile>, path to the .BED file containing intervals of ctcf regions in the genome.
- -dnase <dnasebedFile>, path to the .BED file containing intervals of dnase regions in the genome.
- -promoters <promotersbedFile>, path to the .BED file containing intervals of promoter regions in the genome.

**OUTPUT**
- annotated list of targets and off-targets, text file containing the annotated rows extracted from the input results file (if a row is not annotated, it will not be written in the file)
- set of count files, set of files with counting information, one per genomic annotation, those files are formatted as a matrix, with guides as rows and mismatch as columns, every cell contain the number of annotated targets for the guide with that mismatch
```

**GENERATE REPORT FROM ANNOTATED RESULTS (*generate-report*):**
```
python3 crispritz.py generate-report <guide> -mm <mm_num or range mm_min-mm_max> -profile <guideProfile> -extprofile <guideExtendedProfile> -exons <exonsCountFile> -introns <intronsCountFile> -ctcf <CTCFCountFile> -dnase <DNAseCountFile> -promoters <promotersCountFile> [-gecko (to use gecko pre-computed profile)] [-sumone <summaryReferenceCountFile>][-sumtwo <summaryEnrichedCountFile>]

**INPUT**
- <guide>, a string representing a guide (e.g., GAGTCCGAGCAGAAGAAGAANNN). MUST BE PRESENT IN THE PROFILE AND EXTENDED PROFILE FILE.
- -mm <mm_num or range mm_min-mm_max>, an int number or an interval of int numbers, to select the mismatches to use to generate the graphical representation (e.g., - mm 4 or -mm 3-5).
- -profile \<guideProfile>, path to the profile file containing the profile of sgRNA sequence(s) generated by previous search phase.
- -extprofile \<guideExtendedProfile>, path to the extended profile file containing the motif matrix of sgRNA sequence(s) generated by previous search phase.
- -exons \<exonsCountFile>, path to the ExonsCount file containing count of on/off-targets annotated as exon for each mismatch threshold.
- -introns \<intronsCountFile>, path to the IntronsCount file containing count of on/off-targets annotated as introns for each mismatch threshold.
- -ctcf \<CTCFCountFile>, path to the CTCFCount file containing count of on/off-targets annotated as ctcf for each mismatch threshold.
- -dnase \<DNAseCountFile>, path to the DNAseCount file containing count of on/off-targets annotated as dnase for each mismatch threshold.
- -promoters \<promotersCountFile>, path to the PromotersCount file containing count of on/off-targets annotated as promoter for each mismatch threshold.
- -gecko, flag to use the pre-computed Gecko Library annotated results.
- -sumone \<summaryReferenceCountFile>, path to the SummaryCount file created using a reference genome. USED WITH SUMTWO TO CREATE A BARPLOT TO SHOW THE PERCENTAGE GAIN OF ON/OFF-TARGETS FOR EACH ANNOTATION TYPE. 
- -sumtwo \<summaryEnrichedCountFile>, path to the SummaryCount file created using a enriched genome (genome with variants). USED WITH SUMONE TO CREATE A BARPLOT TO SHOW THE PERCENTAGE GAIN OF ON/OFF-TARGETS FOR EACH ANNOTATION TYPE.


**OUTPUT**
- detailed graphical report for the input guide, an image to visualize the guide behaviour in terms of on/off-targets found in each genomic annotation and in general (total target count).
- barplot with percentage increasing from Variant/Reference Genome, a barplot to visualize the percentage gain in terms of found targets, grouped by every genomic annotation used in the annotation phase [optional, generated only if sumone and sumtwo are present in input]
```
**EXAMPLE OF REPORT GENERATED WITH A GUIDE**
![fig_medium_guide-1](https://user-images.githubusercontent.com/32717860/53101072-5b1bf180-3529-11e9-8c9a-cb5895f2c6c0.png)

**EXAMPLE OF PERCENTAGE GAIN OBTAINED USING ENRICHED GENOME**
![fig_enrichment-1](https://user-images.githubusercontent.com/32717860/53101078-5ce5b500-3529-11e9-97c1-448f2de8c4be.png)


# USAGE EXAMPLE

**BEFORE START TESTING, YOU NEED TO DOWNLAOD SOME FILE, USE THE SCRIPT PROVIDED TO DO SO**
```
bash download_test_files.sh

The script will download chr22 from hg19 reference genome, using the ucsc genome repository, 
it will also download the chr22 vcf file from the 1000 genome project repository
```

**CREATE A VARIANT GENOME (*add-variants*):**
```
python3 crispritz.py add-variants chr22_vcf/ chr22_hg19/

**INPUT**
- chr22_vcf/, the directory containing the VCF files (.vcf or .vcf.gz).
- chr22_hg19/, the directory containing the genome FASTA files (.fa).

**OUTPUT**
- chr22.enriched.fa, copy of the original chromosomes with variants SNPs
- chr22.indels.fa, copy of the original chromosomes with variants INDELs
```

**CREATE A GENOME INDEX (*index-genome*):**
```
python3 crispritz.py index-genome CHR22_HG19 chr22_hg19/ pam/pamNGG.txt

**INPUT**
- CHR22_HG19, name of the directory that will contain all the .bin files (.vcf or .vcf.gz).
- chr22_hg19/, the directory containing the genome FASTA files (.fa).
pam/pamNGG.txt, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).

**OUTPUT**
- NGG_CHR22_HG19, directory containing the .bin files (one per input chromosome). Each file contains all the candidate targets found on the chrosome for the selected input PAM.
```

**SEARCH ON A GENOME INDEX WITH MISMATCHES AND BULGES (*search*):**
```
python3 crispritz.py search genome_library/NGG_CHR22_HG19/ pam/pamNGG.txt emx1_guide/EMX1.txt emx1.chr22 -index -mm 4 -bDNA 1 -bRNA 1 -th 2 -r

**INPUT**
- genome_library/NGG_CHR22_HG19/, the directory containing the genome FASTA files (.fa).
pam/pamNGG.txt, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).
- emx1_guide/EMX1.txt, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- emx1.chr22, first name of all the output files generated by the tool.
- index, flag set to perform search using the index genome (allow mismatches+bulges search).
- -mm 4, threshold of accepted mismatches (i.e., the number of mismatches accepted to save the target in result files).
- -bRNA 1, threshold of accepted RNA bulges (i.e., the number of RNA bulges accepted to save the target in result files).
- -bDNA 1, threshold of accepted DNA bulges (i.e., the number of DNA bulges accepted to save the target in result files).
- -th 2, number of threads used by the tool (default uses all the available threads).
- -r, select the output files (-r: write only off-targets results).

**OUTPUT**
- emx1.chr22.targets.txt, a text file containing all the targets and off-targets found on the genome with genomic coordinates.
- emx1.chr22.profile.xls and emx1.chr22.extendend_profile.xls, set of files containing detailed profile of every input guide, with the count of bp/mismatches and a grouped representation of on/off-targets found per every mismatch count and bulges count.
```

**SEARCH ON A GENOME WITHOUT INDEXING WITH MISMATCHES ONLY (*search*):**
```
python3 crispritz.py search chr22_hg19/ pam/pamNGG.txt emx1_guide/EMX1.txt emx1.chr22 -mm 4 -t

**INPUT**
- chr22_hg19/, the directory containing the genome FASTA files (.fa).
pam/pamNGG.txt, path to a text file containing the PAM (e.g., NNNNNNNNNNNNNNNNNNNNNGG 3).
- emx1_guide/EMX1.txt, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- emx1.chr22, first name of all the output files generated by the tool.
- -mm 4, threshold of accepted mismatches (i.e., the number of mismatches accepted to save the target in result files).
- -th <num_thread>, number of threads used by the tool (default uses all the available threads). 
- -t, select the output files (-r: write only off-targets results, -p: write only profiles, -t: write both).

**OUTPUT**
- emx1.chr22.targets.txt, a text file containing all the targets and off-targets found on the genome with genomic coordinates.
- emx1.chr22.profile.xls and emx1.chr22.extended_profile.xls, set of files containing detailed profile of every input guide, with the count of bp/mismatches and a grouped representation of on/off-targets found per every mismatch count and bulges count.
```

**ANNOTATE RESULTS OBTAINED IN A PREVIOUS SEARCH PHASE (*annotate-results*):**
```
python3 crispritz.py annotate-results emx1_guide/EMX1.txt emx1.chr22.targets.txt emx1.chr22.annotated -exons chroms_bed/hg19_exon.bed -introns chroms_bed/hg19_intron.bed -promoters chroms_bed/hg19_promoter.bed -dnase chroms_bed/hg19_dnase.bed -ctcf chroms_bed/hg19_ctcf.bed

**INPUT**
- emx1_guide/EMX1.txt, path to a text file containing the sgRNA sequence(s). THE LENGTH OF GUIDE MUST BE EQUAL TO THE LENGTH OF PAM SEQUENCE.
- emx1.chr22.targets.txt, path to the text file containing on/off-targets generated by previous search phase.
- emx1.chr22.annotated, first name of all the output files generated by the tool.
- -exons chroms_bed/hg19_exon.bed, path to the .BED file containing intervals of exon regions in the genome.
- -introns chroms_bed/hg19_intron.bed, path to  the .BED file containing intervals of intron regions in the genome.
- -ctcf chroms_bed/hg19_ctcf.bed, path to the .BED file containing intervals of ctcf regions in the genome.
- -dnase chroms_bed/hg19_dnase.bed, path to the .BED file containing intervals of dnase regions in the genome.
- -promoters chroms_bed/hg19_promoter.bed, path to the .BED file containing intervals of promoter regions in the genome.

**OUTPUT**
- emx1.chr22.annotated.txt, text file containing the annotated rows extracted from the input results file (if a row is not annotated, it will not be written in the file)
- emx1.chr22.annotated.*Count.txt, set of files with counting information, one per genomic annotation, those files are formatted as a matrix, with guides as rows and mismatch as columns, every cell contain the number of annotated targets for the guide with that mismatch
```

**GENERATE REPORT FROM ANNOTATED RESULTS (*generate-report*):**
```
python3 crispritz.py generate-report GAGTCCGAGCAGAAGAAGAANNN -mm 3-4 -profile emx1.chr22.profile.xls -extprofile emx1.chr22.extended_profile.xls -exons emx1.chr22.annotated.ExonsCount.txt -introns emx1.chr22.annotated.IntronsCount.txt -dnase emx1.chr22.annotated.DNAseCount.txt -ctcf emx1.chr22.annotated.CTCFCount.txt -promoters emx1.chr22.annotated.PromotersCount.txt -gecko

**INPUT**
- GAGTCCGAGCAGAAGAAGAANNN, a string representing a guide (e.g., GAGTCCGAGCAGAAGAAGAANNN). MUST BE PRESENT IN THE PROFILE AND EXTENDED PROFILE FILE.
- -mm 3-5, an int number or an interval of int numbers, to select the mismatches to use to generate the graphical representation (e.g., - mm 4 or -mm 3-5).
- -profile emx1.chr22.profile.xls, path to the profile file containing the profile of sgRNA sequence(s) generated by previous search phase.
- -extprofile emx1.chr22.extended_profile.xls, path to the extended profile file containing the motif matrix of sgRNA sequence(s) generated by previous search phase.
- -exons emx1.chr22.annotated.ExonsCount.txt, path to the ExonsCount file containing count of on/off-targets annotated as exon for each mismatch threshold.
- -introns emx1.chr22.annotated.IntronsCount.txt, path to the IntronsCount file containing count of on/off-targets annotated as introns for each mismatch threshold.
- -ctcf emx1.chr22.annotated.CTCFCount.txt, path to the CTCFCount file containing count of on/off-targets annotated as ctcf for each mismatch threshold.
- -dnase emx1.chr22.annotated.DNAseCount.txt, path to the DNAseCount file containing count of on/off-targets annotated as dnase for each mismatch threshold.
- -promoters emx1.chr22.annotated.PromotersCount.txt, path to the PromotersCount file containing count of on/off-targets annotated as promoter for each mismatch threshold.
- -gecko, flag to use the pre-computed Gecko Library annotated results.
- -sumone emx1.chr22.annotated.SummaryCount.txt, path to the SummaryCount file created using a reference genome. USED WITH SUMTWO TO CREATE A BARPLOT TO SHOW THE PERCENTAGE GAIN OF ON/OFF-TARGETS FOR EACH ANNOTATION TYPE. 
- -sumtwo emx1.chr22.enriched.SummaryCount.txt, path to the SummaryCount file created using a enriched genome (genome with variants). USED WITH SUMONE TO CREATE A BARPLOT TO SHOW THE PERCENTAGE GAIN OF ON/OFF-TARGETS FOR EACH ANNOTATION TYPE.


**OUTPUT**
- detailed graphical report for the input guide, an image to visualize the guide behaviour in terms of on/off-targets found in each genomic annotation and in general (total target count).
- barplot with percentage increasing from Variant/Reference Genome, a barplot to visualize the percentage gain in terms of found targets, grouped by every genomic annotation used in the annotation phase [optional, generated only if sumone and sumtwo are present in input]
```

SOFTWARE REQUIREMENTS:
- PYTHON 3.*
- GCC 5.4 or 5.5
- bcftools 1.2 or later
- intervaltree-3.0.2 or later
