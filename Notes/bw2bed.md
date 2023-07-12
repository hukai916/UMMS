# How to convert bigwig file to bed file

## 1. Download example data: (using ftp)
```
cd feature_overlap/scripts/bw2bed
wget https://ftp.ncbi.nlm.nih.gov/geo/samples/GSM5224nnn/GSM5224746/suppl/GSM5224746_RB-CR116.mm10.bw
```

## 2. Install required software tools
We need two software for this task: `bigWigToWig` from UCSC and `wig2bed` from bedops.

First, create and activate a conda envrinment named "bedops":
```
conda create -n bedops
conda activate bedops
```
Then, install `bedops`:
```
conda install -c bioconda bedops
```
Now, download the `bigWigToWig` executable from UCSC:
```
# if using Mac with Intel chip
wget http://hgdownload.cse.ucsc.edu/admin/exe/macOSX.x86_64/bigWigToWig

# if using Mac with M chip
wget http://hgdownload.cse.ucsc.edu/admin/exe/macOSX.arm64/bigWigToWig
```

## 3. Convert
```
# first convert bigwig to wig:
chmod 755 bigWigToWig # add execution permission
./bigWigToWig GSM5224746_RB-CR116.mm10.bw GSM5224746_RB-CR116.mm10.wig

# then convert wig to bed:
wig2bed --zero-indexed < GSM5224746_RB-CR116.mm10.wig > GSM5224746_RB-CR116.mm10.bed
```

## Reference
1.  https://www.biostars.org/p/71692/#google_vignette