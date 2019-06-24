## TIGAR
"TIGAR" standing for Transcriptome-Intergrated Genetic Association Resource, which is developed using Python and BASH scripts. TIGAR can fit both Elastic-Net and nonparametric Beyesian model (Dirichlet Process Regression, i.e. DPR) for gene expression imputation, impute genetically regulated gene expression (GReX) from genotype data, and conduct transcriptome-wide association studies (TWAS) using both individual-level and summary-level GWAS data for univariate and multivariate phenotypes.

### Software

- Please add the executable file `./Model_Train/DPR` to your linux `${PATH}` directory. Assuming `~/bin/` is a directory added to your `${PATH}` environmental variable, you can accomodate the following example command

```
cp ./Model_Train/DPR ~/bin/
```

- BGZIP, TABIX, Python 3.5 and the following python libraries are required for running TIGAR
1. BGZIP: http://www.htslib.org/doc/bgzip.html 
2. TABIX: http://www.htslib.org/doc/tabix.html 
3. python 3.5 
   - dfply
   - io
   - subprocess
   - multiprocess

### Input file format
Example data provided here are generated artificially. All input files are tab delimited text files.


#### 1. Gene Expression File (Gene_Exp_combination.txt)
| CHROM | GeneStart | GeneEnd | TargetID/GeneID | GeneName | sample1 | sample...|
|:-----:|:---------:|:-------:|:---------------:|:--------:|:-------:|:--------:|
|   1   |    100    |   200   |     ENSG0000    |     X    |   0.2   |     ...  |


#### 2. Genotype File
1) vcf file
- http://www.internationalgenome.org/wiki/Analysis/Variant%20Call%20Format/vcf-variant-call-format-version-40/

| CHROM | POS |  ID | REF | ALT | QUAL | FILTER | INFO | FORMAT |  sample1 | sample...|
|:-----:|:---:|:---:|:---:|:---:|:----:|:------:|:----:|:------:|:--------:|:--------:|
|   1   | 100 | rs1 |  C  |  T  |   .  |  PASS  |   .  |  GT:DS | 0/0:0.01 |    ...   |

2) dosages file

| CHROM | POS |  ID | REF | ALT | sample1 | sample...|
|:-----:|:---:|:---:|:---:|:---:|:-------:|:--------:|
|   1   | 100 | rs1 |  C  |  T  |   0.01  |    ...   |

#### 3. PED File
- http://zzz.bwh.harvard.edu/plink/data.shtml#ped

| FAM_ID | IND_ID | FAT_ID | MOT_ID | SEX | PHENO | COV |
|:------:|:------:|:------:|:------:|:---:|:-----:|:---:|
|   11A  |   11A  |    X   |    X   |  1  |  0.2  | 0.3 |

#### 4.Zscore File

| CHROM | POS | REF | ALT | Zscore |
|:-----:|:---:|:---:|:---:|:------:|
|   1   | 100 |  C  |  T  |  0.01  |

#### 5. Genome block annotation file
- The block annotation file is a tab delimited text file with head row of `CHROM Start End File`, denoting the chromosome number, starting position, ending position, and corresponding reference VCF file name under specified `--geno_path`. Reference VCF files shall be of one per chromosome, or one for the whole genome-wide variants. Example block annotation file for European samples is provided `./TIGAR/example_data/block_annotation_EUR.txt`. 

| CHROM | Start | End | File |
|:-----:|:---------:|:-------:|:---------------:|
|   1   |    100    | 20000   |  CHR1.vcf.gz    |

- Block annotation files of other ethnicities can be adopted from the genome segmentation generated by `LDetect`, https://bitbucket.org/nygcresearch/ldetect-data/src/master/.


### Example Usage 
- More details are available in the TIGAR_Manual.pdf
- Train gene expression imputation model

Train DPR imputation model
```
./Model_Train/TIGAR_Model_Train.sh --model DPR \
--Gene_Exp ${Gene_Exp_path} --train_sample ${train_sample_path} \
--chr 1 --train_dir ${train_dir} \
--geno_train vcf --FT DS \
--out ${out_prefix}
```

Train Elastic-Net imputation model
```
./Model_Train/TIGAR_Model_Train.sh --model elastic_net \
--Gene_Exp ${Gene_Exp_path} --train_sample ${train_sample_path} \
--chr 1 --train_dir ${train_dir} \
--geno_train vcf --FT DS \
--out ${out_prefix}
```

- Predict GReX
```

```

- TWAS

Using individual-level GWAS data
```
./TWAS/TIGAR_TWAS.sh --asso 1 \
--Gene_EXP ${Gene_Exp_path} --PED ${PED} --Asso_Info ${asso_Info} \
--out ${out_prefix}
```

Using summary-level GWAS data
```
./TWAS/TIGAR_TWAS.sh --asso 2 \
--Gene_EXP ${Gene_Exp_path} --Zscore ${Zscore} --Weight ${Weight} \
--Covar ${Ref_Covariance_file} --chr 22 \
--out ${out_prefix}
```

Generate reference covariance files
```
./TWAS/covar_calculation.sh --block ${block_annotation} \
--geno_path ${geno_path} --geno vcf \
--chr 22 --Format GT \
--out ${out_prefix}
```

### Reference
- Elastic Net: https://github.com/hakyimlab/PrediXcan  
- DPR: https://github.com/biostatpzeng/DPR
