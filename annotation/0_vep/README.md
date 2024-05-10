# Indexing, sorting and [VEP](https://www.ensembl.org/info/docs/tools/vep/index.html) annotation

Along with VEP annotation wwe will use MaxEntScan plugin and ConSplice precomputed variants.

Install VEP, MaxEntScan plugin and download Homo Sapiens reference following official [documentation](https://www.ensembl.org/info/docs/tools/vep/index.html)

Download ConSplice precomputed variants
```bash
gsutil -m cp -r \
  "gs://pdivas/ConSplice_for_PDIVAS" \
  .
```
For VEP annotation VCF must be sorted and have appropriate indexes in the header.
```bash
for file in ../init/*vcf; do \
name=`basename $file`; \
bcftools reheader \
--fai ../ensembl-vep/vep_cache/homo_sapiens/111_GRCh38/Homo_sapiens.GRCh38.dna.toplevel.fa.gz.fai \
$file | bcftools sort -o ${name%.vcf}_indexed.vcf; \
done;
```
To annotate VCF files with VEP we used following command
```bash
for file in *vcf; do \
vep --cache ../ensembl-vep/vep_cache/ \
--offline \
--cache_version 111 \
--assembly GRCh38 \
--hgvs --pick_allele_gene \
--fasta ../ensembl-vep/vep_cache/homo_sapiens/111_GRCh38/Homo_sapiens.GRCh38.dna.toplevel.fa.gz \
--vcf --force \
--custom ../ConSplice_for_PDIVAS/ConSplice.50bp_region.inverse_proportion_refo_hg38.bed.gz,ConSplice,bed,overlap,0 \
--plugin MaxEntScan,../ensembl-vep/fordownload,SWA,NCSS \
--fields "Consequence,SYMBOL,Gene,INTRON,HGVSc,STRAND,ConSplice,MES-SWA_acceptor_diff,MES-SWA_acceptor_alt,MES-SWA_donor_diff,MES-SWA_donor_alt" \
-i $file -o ${file%vcf}vep.vcf
done;
```