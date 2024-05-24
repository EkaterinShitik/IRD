# [CI-SpliceAI](https://github.com/YStrauch/CI-SpliceAI__Annotation) annotation
1. Install CI-SpliceAI via conda
```
conda create -n cis_use python=3.8
conda activate cis_use
pip install "cispliceai[cpu]"
```
To set up GPU follow these [instructions](https://ci-spliceai.com/install/) 

2. Download reference file [GRCh37/hg19](http://hgdownload.cse.ucsc.edu/goldenPath/hg19/bigZips/hg19.fa.gz) or [GRCh38/hg38](http://hgdownload.cse.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz). *Or use from the previous step.*
3. Annotate files from `3_spliceai` folder:
```
for file in ../3_spliceai/*vcf; do \
name=`basename $file`; \
cis-vcf -a grch38 -i $file -o ${name%vcf}cis.vcf -d 100 GRCh38.primary_assembly.genome.fa; \
done;
```