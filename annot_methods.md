Code for the paper



- Correction of substitution errors


```
ace 30000000 /storage/ao006/Nitz4_project/raw_data/genome_reads/FCD20HPACXX-SZAIPI025576-84_L1_1.fq /scratch/ao006/Nitz4_project/ACE_output/unz_fw_nitz4_ACE.fq

ace 30000000 /storage/ao006/Nitz4_project/raw_data/genome_reads/FCD20HPACXX-SZAIPI025576-84_L1_2.fq /scratch/ao006/Nitz4_project/ACE_output/unz_rev__nitz4_ACE.fq
```



- Trimming adapters and low-quality read-ends


```
java -jar /share/apps/trimmomatic/Trimmomatic-0.32/trimmomatic-0.32.jar PE \
/scratch/ao006/Nitz4_project/ACE_output/unz_fw_nitz4_ACE.fq.gz /scratch/ao006/Nitz4_project/ACE_output/unz_rev__nitz4_ACE.fq.gz \
Nitz4_trimmed_fw.fq.gz Nitz4_trim_junk_fw.fq.gz Nitz4_trimmed_rev.fq.gz Nitz4_trim_junk_rev.fq.gz \
ILLUMINACLIP:/storage/ao006/nitzschia_sp_nitz4/illumina_adapter_database/TruSeq_adapters.fa:2:40:15 LEADING:2 TRAILING:2 \
SLIDINGWINDOW:4:2 MINLEN:30 TOPHRED64 2>>nitz4_std_eror.log
```


- Fastqc quality check


```
# for untrimmed reads
/share/apps/fastqc/FastQC_v0.11.5/fastqc /storage/ao006/Nitz4_project/ACE_output/unz_fw_nitz4_ACE.fq
/share/apps/fastqc/FastQC_v0.11.5/fastqc /storage/ao006/Nitz4_project/ACE_output/unz_rev__nitz4_ACE.fq

#for trimmed reads
/share/apps/fastqc/FastQC_v0.11.5/fastqc /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_fw.fq
/share/apps/fastqc/FastQC_v0.11.5/fastqc /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_rev.fq
```

- Assembly with range of K-mer lengths

```
# a є {15, 21,27,33,39,45,51,59,63}
mpirun -n 16 /share/apps/Ray/Ray-2.3.1/Ray -k a -amos -p /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_fw.fq \
/storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_rev.fq -o nitz4.ray.K45
```
- R quality check

- Get rid of plastid reads with bowtie2

```
#database from Nitzschia4 plastid genome
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2-build --seed 1 /storage/ao006/Nitz4_project/raw_data/plastom/nitzschia4_complete_plastom.fa nitz4.plast

#align reads to plastid-genome db

/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2 --seed 1 -p 16 -x /storage/ao006/Nitz4_project/bowt2_output/bowt.DB.nitz4.plastom/nitz4.plast \
-1 /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_fw.fq \
-2 /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_rev.fq \
--phred64 --local --un-conc-gz Nitz4_noplastom_%.fq.gz \
--al-conc-gz Nitz4_plast_reads_%.fq.gz

```
- Assemble genome without plastid reads

```
mpirun -n 16 /share/apps/Ray/Ray-2.3.1/Ray -show-memory-usage -k 45 -amos -p /storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_1.fq  \
/storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_2.fq  -o nitz4.ray.noplast.K45
```

- Find mitochondrial scaffolds


Blast diatom mitochondrial genes from custom database to plastid free assembly


[R code to find mitochondrial scaffolds ](https://github.com/Nastassiia/Nitz4_annot_paper/blob/master/gene.fold.names.md)




- Get rid of mitochondrial reads

```
#create database from mitochondrial contig (Scaffold-4000015)
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2-build --seed 1 /scratch/ao006/Nitz4_project/bowt.get.rid.mito.reads/cont4000015.fasta cont4.15

#align reads to mitochondrial contig
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2 --seed 1 -p 16 -x /scratch/ao006/Nitz4_project/bowt.get.rid.mito.reads/cont.4000015.DB/cont4.15 \
-1 /storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_1.fq.gz \
-2 /storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_2.fq.gz \
--phred64 --local --un-conc-gz Nitz4_no_organel_%.fq.gz \
--al-conc-gz Nitz4_mito_reads_%.fq.gz

```

- Assemble genome without organellar reads with range of K-mer length

```
# a є {21,27,33,39,45,51,57,63}
mpirun -n 16 /share/apps/Ray/Ray-2.3.1/Ray -k 45 -amos -show-memory-usage -p /storage/ao006/Nitz4_project/bowt.get.rid.mito.reads/nitz4_cont4.15_aln/Nitz4_no_organel_1.fq \
/storage/ao006/Nitz4_project/bowt.get.rid.mito.reads/nitz4_cont4.15_aln/Nitz4_no_organel_2.fq  -o nitz4.no.org.K45

```

#### Identification of non-target genomes with blobplots

[Blobplots for whole, plastid-free and organelle-free reads scaffolds](https://github.com/Nastassiia/Nitz4_annot_paper/blob/master/make_blobloplots.md)

#### Generate Phaeodactylum tricornutum chosen gene-set for retraining augustus gene-prediction parameters

[Generate training gene set for augustus]()


#### Annotation of Nitzschia4 nuclear genome with MAKER
