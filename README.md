## Yosia Mugume_ UNIX Assignment
### Data inspection
### Attributes of fang_et_al_genotypes

I used 'head' to look a the first 5 rows of fang_et_al_genotypes.

```
$ head -n 5 fang_et_al_genotypes.txt 
```

However,because of the very many columns of the dataset, this was not very clear so i will use 'awk' to make determine the number of columns of the data set.

```
$ head -n 5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
``` 

Next head abd | to print the contents of the first 5 rows and strictly considering 10 columns. This way i am also able to see the headers for the data set

```
$ head -5 fang_et_al_genotypes.txt | cut -f 1-10 | column -t
```

Next i used 'tail' and awk' to find out the number of columns at the end of the data set

```
$ tail -n 5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

Next i counted the number of rows

```
$ wc -l fang_et_al_genotypes.txt 
```
i also run a code to find out the presence or abscence of ASCII characters in the data

```
$ file fang_et_al_genotypes.txt 
```

I used 'du' to find out the size of the data

```
$ du -h fang_et_al_genotypes.txt 
```


**By inspecting this file I learned that:**

1. it has 986 columns
2. its has 2783 rows
3. data set contains ASCII text, with very long lines
4. the file size is 6.1M
5. the dataset has headers

###Attributes of snp_position.txt

I used 'head' to look a the first 5 rows of fang_et_al_genotypes.

```
$ head -n 5 snp_position.txt
```

However,because of the very many columns of the dataset, this was not very clear so i will use 'awk' to make determine the number of columns of the data set.

```
$ head -n 5 snp_position.txt | awk -F "\t" '{print NF; exit}'
``` 

Next head abd | to print the contents of the first 5 rows and strictly considering 10 columns. This way i am also able to see the headers for the data set

```
$ head -5 snp_position.txt | cut -f 1-10 | column -t
```

Next i used 'tail' and awk' to find out the number of columns at the end of the data set

```
$ tail -n 5 snp_position.txt | awk -F "\t" '{print NF; exit}'
```

Next i counted the number of rows

```
$ wc -l snp_position.txt
```
i also run a code to find out the presence or abscence of ASCII characters in the data

```
$ file snp_position.txt
```

I used 'du' to find out the size of the data

```
$ du -h snp_position.txt
```

**By inspecting this file I learned that:**

1. it has 15 columns
2. its has 984 rows
3. data set contains ASCII text
4. the file size is 38k
5. the dataset has headers

**The inspection done above has helped us to see that the two datasets are arranged diffrently interms of rows and columns. This will be helpful when we do data processing in the next step will need to tarnsponse them.**

### Data processing
**1. Processing snp_formatted_position.txt**

The first step i did was to format the snp_position.txt data such thatit can be arranged same way as the fang data since we are going to combine the two data sets

```
$ cut -f 1,3,4 snp_position.txt > snp_formatted_position.txt
```

Next i sorted the the new formatted data. In this sorting i had to exclude the header and sorted by column 1 and so i named my new sorted file as sorted_snp_formatted_position.txt

```
$ (head -n 1 snp_formatted_position.txt && tail -n +2 snp_formatted_position.txt | sort -k1,1) > sorted_snp_formatted_position.txt
```
I then checked to see if the new formated data was sorted well without sorting the header

```
$ sort -c -k1,1 sorted_snp_formatted_position.txt

```

**2. processing fang_et_al_genotypes data**

I begun by finding out the different groups in the data set. This i did because we are intrested in only using specific groups from the entire dataset.

```
$ sort -k3 fang_et_al_genotypes.txt | cut -f 3 | uniq -c | column -t
```

I found out the number of groups represented in the dataset - they are 17 groups

```
$ sort -k3 fang_et_al_genotypes.txt | cut -f 3 | uniq -c | column -t | wc -l
```

I did Extract 3 columns of interest from the fang_et_al_genotypes.txt dataset while excluding the first three columns

```
$ awk -F "\t" '$3 ~ /ZMMIL|ZMMLR|ZMMMR|Group/' fang_et_al_genotypes.txt | cut -f 1,4-986 > maize_genotypes.txt
```

```
$ awk -F "\t" '$3 ~ /ZMPBA|ZMPIL|ZMPJA|Group/' fang_et_al_genotypes.txt | cut -f 1,4-986 > teosinte_genotypes.txt
```

Then i checked the number of lines in each dataset and here is the out put.

1574 maize_genotypes.txt
     976 teosinte_genotypes.txt
    2550 total
    
 ```
 $ wc -l maize_genotypes.txt teosinte_genotype
 ```
 
 Next i transposed both maize and teosite datasets and renamed the new dataset as transposed_maize_genotypes.txt and transposed_teosinte_genotypes.txt
 
 ```
 $ awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
 ```
 
 ```
 $ awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
 ```
 
 Next i sorted the transposed maize and teosinte .txt data files and renamed the out put data as sorted_transposed_maize_genotypes.txt and sorted_transposed_teosinte_genotypes.txt
 
 ```
 $ (head -n 1 transposed_teosinte_genotypes.txt && tail -n +2 transposed_teosinte_genotypes.txt | sort -k1,1) > sorted_transposed_teosinte_genotypes_.txt
 ```

```
$ (head -n 1 transposed_maize_genotypes.txt && tail -n +2 transposed_maize_genotypes.txt | sort -k1,1) > sorted_transposed_maize_genotypes.txt
```

Next i checked to see if the new sorted and transposed files are indeed sorted. An exit status of zero confirmed to me that the data was sorted

```
$ tail -n +2 sorted_transposed_maize_genotypes.txt | sort -c -k1,1
```
```
tail -n +2 sorted_transposed_teosinte_genotypes.txt | sort -c -k1,1
```

###joining two datasets##

Next step is to join the two files (transposed and sorted  maize/teosinte and the formatted snp datafile. This will be done separately for maize and Teosinte. And i also checked to confirm that that the two data sets were joined

```
$ join --header -1 1 -2 1 -t $'\t' sorted_snp_formatted_position.txt sorted_transposed_maize_genotypes.txt > joned_maize_snp_and_genotypes_.txt
```
```
$ head joined_maize_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ join --header -1 1 -2 1 -t $'\t' sorted_snp_formatted_position.txt sorted_transposed_teosinte_genotypes.txt > joined_teosinte_snp_and_genotypes.txt
```

Creating the 20 files for both maize and tesinte from chromosome 1-10. Here under i will list only  the code for teosite from chr 1 -chr 10. Also i checked the file after running it

```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 1' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr1_teosinte_snp_and_genotypes.txt
```

```
$ head chr1_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 2' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr2_teosinte_snp_and_genotypes.txt
```

```
$ head chr1_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```

```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 3' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr3_teosinte_snp_and_genotypes.txt
```
```
$ head chr3_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```

```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 4' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr4_teosinte_snp_and_genotypes.txt
```
```
$ head chr4_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 5' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr5_teosinte_snp_and_genotypes.txt
```
```
$ head chr5_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 6' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr6_teosinte_snp_and_genotypes.txt
```
```
$ head chr6_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 7' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr7_teosinte_snp_and_genotypes.txt
```
```
$ head chr7_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 8' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr8_teosinte_snp_and_genotypes.txt
```
```
$ head chr8_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 9' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr9_teosinte_snp_and_genotypes.txt
```
```
$ head chr9_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_teosinte_snp_and_genotypes.txt && awk '$2 == 10' joined_teosinte_snp_and_genotypes.txt | sort -k 3n) > chr10_teosinte_snp_and_genotypes.txt
```
```
$ head chr10_teosinte_snp_and_genotypes.txt | cut -f 1-10 | column -t
```

Doing this in reverse order (it reverses the position to descending order

```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 1' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr1_reversed_maize_snp_genotypes.txt
```
```
$ head chr1_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 2' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr2_reversed_maize_snp_genotypes.txt
```
```
$ head chr2_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 3' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr3_reversed_maize_snp_genotypes.txt
```
```
$ head chr3_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 4' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr4_reversed_maize_snp_genotypes.txt
```

```
$ head chr4_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 5' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr5_reversed_maize_snp_genotypes.txt
```
```
$ head chr5_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 6' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr6_reversed_maize_snp_genotypes.txt
```
```
$ head chr6_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 7' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr7_reversed_maize_snp_genotypes.txt
```
```
$ head chr7_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 8' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr8_reversed_maize_snp_genotypes.txt
```
```
$ head chr8_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 9' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr9_reversed_maize_snp_genotypes.txt
```
```
$ head chr9_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```
```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$2 == 10' joined_maize_snp_and_genotypes.txt | sort -k 3nr | sed 's/?/-/g') > chr10_reversed_maize_snp_genotypes.txt
```
```
$ head chr10_reversed_maize_snp_genotypes.txt | cut -f 1-10 | column -t
```

1 file with all SNPs with unknown position in the genome: there is no need to be in a particular order

```
$ (head -n 1 joined_maize_snp_and_genotypes.txt &&awk '$3 ~ /unknown/' joined_maize_snp_and_genotypes.txt) > maize_snp_with_unknown_position.txt
```
```
$ head maize_snp_with_unknown_position.txt | cut -f 1-10 | column -t
```
1 file with all SNPs with multiple positions in the genome: there is no need to be in a particular order


I just found that out while inspecting the joined file using this


```
$ cut -f 3 joined_maize_snp_and_genotypes.txt | sort -k 1 | uniq -c 
```
So I just used awk to get the last file for maize as follows:

```
$ (head -n 1 joined_maize_snp_and_genotypes.txt && awk '$3 ~ /multiple/' joined_maize_snp_and_genotypes.txt) > maize_snp_multiple_position.txt
```
and I also checked the output:
```
$ head maize_snp_multiple_position.txt | cut -f 1-10 | column -t 
```






# UNIX-Assignment
