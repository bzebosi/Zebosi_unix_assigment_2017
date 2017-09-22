###UNIX ASSIGNMENT
##Data inspection

Combined commands to form a loop for data inspectionon 

```
$ for i in snp_position.txt fang_et_al_genotypes.txt ; do echo "number of lines at: $(wc -l $i)"; echo  "numbers of columns : $(awk -F "\t" '{print NF; exit}' $i) $i"; echo "filesize: $(du -h $i)"; echo "idenity any ASCII and non-ASCII characters:" $(file $i); done
```
**Outputs 
snp_position.txt**

- - number of lines at: 984 snp_position.txt
- - numbers of columns : 15 snp_position.txt
- - filesize: 84K	snp_position.txt
- - idenity any ASCII and non-ASCII characters: snp_position.txt: ASCII text

**fang_et_al_genotypes.txt**

- - number of lines at: 2783 fang_et_al_genotypes.txt
- - numbers of columns : 986 fang_et_al_genotypes.txt
- - filesize: 11M	fang_et_al_genotypes.txt
- - idenity any ASCII and non-ASCII characters: fang_et_al_genotypes.txt: ASCII text, with very long lines


##Data processing 

- **Teosinte and maize file creation **

>create maize fille by grepping out groups "ZMMIL,ZMMLR,ZMMMR"

```
$grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotype.txt

```
> create teosite fille by grepping out groups "ZMPBA,ZMPIL,ZMPJA" without headers


```
$grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotype.txt

```
> extracted header from the fang_et_al_genotypes.txt 

```
$head -n 1 fang_et_al_genotypes.txt > header_fang_et_al_genotypes.txt
```
> adding headers to filles :- teosinte_genotype.txt and maize_genotype.txt

```
$cat header_fang_et_al_genotypes.txt maize_genotype.txt > headed_maize_genotype.txt
```
```
$cat header_fang_et_al_genotypes.txt teosinte_genotype.txt > headed_teosinte_genotype.txt
```

> removed 1,2 uneccessary colunms using the cut command

```
$cut -f 3-968 headed_teosinte_genotype.txt > edit_headed_teosinte_genotype.txt

```
```
$cut -f 3-968 headed_maize_genotype.txt > edit_headed_maize_genotype.txt
```

- **Transpose the data files **

>transposed files with headers 
```
$awk -f transpose.awk edit_headed_teosinte_genotype.txt > transposed_teosinte_genotypes.txt
```
```
$awk -f transpose.awk edit_headed_maize_genotype.txt > transposed_maize_genotypes.txt
```

- **sorting all the files by SNP id**

```
$sort -k1,1 snp_position.txt > sorted_snp_position.txt 
```

``` 
$sort -k1,1 transposed_teosinte_genotypes.txt > sorted_teosinte.txt
```
```
$sort -k1,1 transposed_maize_genotypes.txt > sorted_maize.txt
```
- **checks for sorted filles**
```
$sort -k1,1 -c  sorted_snp_position.txt |echo $?
```
```
$sort -k1,1 -c  sorted_maize.txt |echo $?
```
```
$sort -k1,1 -c  sorted_teosinte.txt |echo $?

```
> all the outputs were 0, implying sorted 

- **Joining files**
> joining files with colunm one as the commnon colunm
```
$join -1 1 -2 1 sorted_snp_position.txt sorted_maize.txt > maize_snpdata.txt
```
```
$join -1 1 -2 1 sorted_snp_position.txt sorted_teosinte.txt > teosinte_snpdata.txt

```

**- checks for join **

```$wc -l sorted_snp_position.txt``` ```$wc -l sorted_maize.txt ``` ```$wc -l sorted_teosinte.txt ```
>outputs 984, 966, 966 lines respectively 

```$wc -l maize_snpdata.txt ``` ```$wc -l teosinte_snpdata.txt```
>outputs 965 lines respectively

- **sort by chromosome** 
>sorting by column 3 which has chromosome numbers 
```
$sort -k3,3n maize_snpdata.txt > maize_snpdata_sorted_chr.txt
```
```
$sort -k3,3n teosinte_snpdata.txt > teosinte_snpdata_sorted_chr.txt
```
checks 
```sort -k3,3n teosinte_snpdata_sorted_chr.txt |echo $?``` 
```sort -k3,3n maize_snpdata_sorted_chr.txt |echo $?```
> outputs 0

- substitution with ? or - using sed command

> replacing the missing data (tabs) with ?
```
$sed 's/-t/?/g' maize_snpdata_sorted_chr.txt > maize_snpdata_sorted_chr_with_quest.txt
``` 
```
$sed 's/-t/?/g' teosinte_snpdata_sorted_chr.txt > teosinte_snpdata_sorted_chr_with_quest.txt
```

>replacing the missing data (?) with -

```
$sed 's/?/-/g' maize_snpdata_sorted_chr.txt > maize_snpdata_sorted_chr_with_dash.txt

```
```
$sed 's/?/-/g' teosinte_snpdata_sorted_chr.txt > teosinte_snpdata_sorted_chr_with_dash.txt
```

**- Extracting chromosomes to seperate file**
```
$ for i in {1..10}; do awk '$3=='$i'' maize_snpdata_sorted_chr_with_quest.txt > maize_chrom"$i"_quest.txt; done
```
```
$for i in {1..10}; do awk '$3=='$i'' teosinte_snpdata_sorted_chr_with_quest.txt > teosinte_chrom"$i"_quest.txt; done
```
```
$for i in {1..10}; do awk '$3=='$i'' maize_snpdata_sorted_chr_with_dash.txt > maize_chrom"$i"_dash.txt; done
```
```
$for i in {1..10}; do awk '$3=='$i'' teosinte_snpdata_sorted_chr_with_dash.txt > teosinte_chrom"$i"_dash.txt; done
```

**- sorting chromosomes based increasing SNP position**

```
$for i in {1..10}; do sort -k4,4n maize_chrom"$i"_quest.txt > maize_chrom"$i"_quest_incre_snp_position.txt; done
```
```
$for i in {1..10}; do sort -k4,4n teosinte_chrom"$i"_quest.txt > teosinte_chrom"$i"_quest_incre_snp_position.txt; done
```
- **Sorting chromosomes by decreasing snp position**
```
$for i in {1..10}; do sort -k4,4nr  teosinte_chrom"$i"_dash.txt > teosinte_chrom"$i"_dash_decr_snp_position.txt; done
```
```
$for i in {1..10}; do sort -k4,4nr  maize_chrom"$i"_dash.txt > maize_chrom"$i"_dash_decr_snp_position.txt; done
```
- **SNPs with unknown positions **

```
$grep "unknown" maize_snpdata_sorted_chr.txt > maize_unknown.txt
```
```
$grep "unknown" teosinte_snpdata_sorted_chr.txt > teosinte_unknown.txt
```

- **SNPs with multiple positions **

```
$grep "multiple" teosinte_snpdata_sorted_chr.txt > teosinte_muitiple.txt

```
```
$grep "multiple" maize_snpdata_sorted_chr.txt > maize_multiple.txt
```
**Supplemental files **
all filles worked with during the assignment are in the directory ==brian_zebosi_unix_assignment== with subfolders
orginal having origin files for the assignment, working having other files/intermediate files used to produced the final files, final_file has the final product file required in the assignment 

> command for creating the filles

```
$ mkdir -p brian_zebosi_unix_assignment/{original_file,working_file,final_file}

```

further final_files subdivided into teosinte,maize,unknown,multiple
teosinte has the file SNPs for each chromosome in teosite, maize has the file SNPs for each chromosome in maize, unknown files for unknown positions for maize and teosite, multiple has files with multiple snp positions. 
  

**upload brian_zebosi_unix_assignment to git repository**
- git cloned the repository
```
$git clone https://github.com/bzebosi/bzebosi_unix_assigment

```
-moved the directory brian_zebosi_unix_assignment to cloned repository bzebosi_unix_assigment

```
$mv brian_zebosi_unix_assignment bzebosi_unix_assigment
```
-staged the bzebosi_unix_assigment repository 

```
$git add brian_zebosi_unix_assignment
```
- Commited the file to the local repository
```
$git commit -m "initial commit (brian_zebosi_unix_assignment)"
```

```
$git push origin master
```









