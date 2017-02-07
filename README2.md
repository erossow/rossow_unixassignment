#Create Git Repository for Assignment



# 1. File size and inspection
# To inspect the files I used wc, du -h, and the awk line for the number of columns and found the following:
# fang_et_al_genotypes.txt
# 	2782 lines  	2744038 words 	11051938 characters
#	11MB	983 columns

$ snp_position.txt
# 	984 lines 		13198 words		8374 characters
$ du -h snp_position.txt
#	84KB	15 columns

DATA PROCESSING 

# Extracting the Maize files from the fang_et_al_genotypes.txt file to a single maize_genotype file
grep -E "(Group|ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt | cut -f 4-986 > maize_genotypes.txt

# Extracting the Teosinte files from the fang_et_al_genotypes.txt file to a single teosinte_genotype file
grep -E "(Group|ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt | cut -f 4-986 > teosinte_genotypes.txt


#Cut snp_position.txt to only include ID, chromosome, position
$ cut -f 1,3,4 snp_position.txt > snp_position_cut.txt


#transpose genotype files
$ awk -f transpose.awk maize_genotypes_cut.txt > trans_maize_genotypes.txt
$ awk -f transpose.awk teosinte_genotypes_cut.txt > trans_teosinte_genotypes_cut.txt

#Sort position and genotype files' ID column because they will be merged by it
$ sort -k1,1 maize_genotypes_trans.txt > maize_genotypes_cst.txt
$ sort -k1,1 teosinte_genotypes_cut_trans.txt > teosinte_genotypes_cst.txt
$ sort -k1,1 snp_position_cut.txt > snp_position_cut_sorted.txt

#Check to see if sorted with cut -f 1 | head

#join genotypes+snp_positions to make files for each species
$ join -1 1 -2 1 snp_position_cut_sorted.txt maize_genotypes_cst.txt > maize_joined.txt
$ join -1 1 -2 1 snp_position_cut_sorted.txt teosinte_genotypes_cst.txt > teosinte_joined.txt
$ join -t $'\t' -1 1 -2 1  snp_position_cut_sorted.txt maize_genotypes_cst.txt > maize_joined.txt

#remove data for each chromosome and sort the position data then pipe into own file for both corn and teosinte
awk '$2==1' join_maize.txt | sort -k3,3g > maizecs1fw.txt
awk '$2==10' join_teosinte.txt | sort -k3,3g > teosintecs10fw.txt

#Do the reverse sort (-r option) and change '?' to '-' using sed for each chromosome

awk '$2==1' join_maize.txt | sort -k3,3gr |sed 's/?/-/g' > maizecs1rv.txt
awk '$2==10' join_teosinte.txt | sort -k3,3gr |sed 's/?/-/g' > teosintecs10rv.txt

