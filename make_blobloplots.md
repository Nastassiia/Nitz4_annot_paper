## Blobplots for whole genome (nuclear + organellar)


- Reads alignment to original scaffolded genome

```
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2-build --seed 1 /storage/ao006/Nitz4_project/RAY_output/RAY_output.45/nitz4.ray.K45/Scaffolds.fasta nitz4.origscaff

/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2 --seed 1 -k 1 --very-fast-local -p 16 -x /scratch/ao006/Nitz4_project/bowt.origscaff.for.blobo/nitz4.origscaff.db/nitz4.origscaff \
-1 /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_fw.fq \
-2 /storage/ao006/Nitz4_project/trimmomat_output/Nitz4_trimmed_rev.fq \
--reorder -S nitz4.orscaff.reads.aln.sam

```

- Convert SAM alignment generated by bowtie2 to BAM

```
samtools view -S -b -T ../Scaffolds.fasta  nitz4.orscaff.reads.aln.sam -o nitz4.orscaff.reads.aln.bam

```

- Blast scaffold against Genbank 'non-redundant' database

```
blastn -task megablast -query ~/Desktop/Nitz4/blobology_viz/blobo_origscaff_viz/raw_stuff/Scaffolds.fasta -db $BLASTDB/nt/nt -evalue 1e-5 -num_threads 4 -max_target_seqs 1 -outfmt '6 qseqid staxids bitscore' -out ~/Desktop/Nitz4/blobo_origscaff_viz/nitz4.scaff.nt.bitscore.megablast

```

- Create Blobplots

```
#create bloboplots database
blobtools create -i ../raw_stuff/Scaffolds.fasta -b ../orig.scaff.reads.aln/nitz4.orscaff.reads.aln.bam -t ../nitz4.scaff.nt.bitscore.megablast -o nitz4.bitscor.bloplot 1>log.bl.create.txt

#create a view of a blobDB file
blobtools view -i nitz4.bitscor.bloplot.blobDB.json 1>log.bl.view.txt

#create bloboplot
blobtools blobplot -i nitz4.bitscor.bloplot.blobDB.json 1>log.bl.blobp.txt

```


[Blobplot_original_scaffolds](https://github.com/Nastassiia/Nitz4_annot_paper/blob/master/plots/nitz4.original.scaffold.png)


## Blobplots for plastid-reads free genome

 - Plastid-free reads alignment to plastid-free scaffolded genome

```
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2-build --seed 1 /storage/ao006/Nitz4_project/RAY_output/RAY_noplast_K45/nitz4.ray.noplast.K45/Scaffolds.fasta nitz4.plastfree.scaff

/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2 --seed 1 -k 1 --very-fast-local -p 16 -x /scratch/ao006/Nitz4_project/bowtie_runs/bowt.plast.free.reads.blobo/nitz4.K45.plastfree.DB/nitz4.plastfree.scaff \
-1 /storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_1.fq \
-2 /storage/ao006/Nitz4_project/bowt2_output/bowt_nitz4_aln/Nitz4_noplastom_2.fq  \
--reorder -S nitz4.plastfree.reads.aln.sam
```

- Convert SAM alignment generated by bowtie2 to BAM

```
samtools view -S -b -T Scaff.K45.plastfree.nitz4.fasta nitz4.plastfree.reads.aln.sam -o nitz4.plastfree.reads.aln.bam
```

- Blast scaffold against Genbank 'non-redundant' database

```
blastn -task megablast -query ~/Desktop/Nitz4/blobo.plastfree.scaff.nitz4/raw_stuff/Scaff.K45.plastfree.nitz4.fasta -db ~/soft/blastdb/nt/nt -evalue 1e-5 -num_threads 4 -max_target_seqs 1 -outfmt '6 qseqid staxids bitscore' -out ~/Desktop/Nitz4/blobo.plastfree.scaff.nitz4/nitz4.plastfree.K45.scaff.megablast 2>hist.megabl.txt
```

 - Create Blobplots

```
#create bloboplots database
blobtools create -i Scaff.K45.plastfree.nitz4.fasta -b nitz4.plastfree.reads.aln.bam -t ../nitz4.plastfree.K45.scaff.megablast -o nitz4.plastfree.blobplot 1> bl.create.plastfree.txt

#create a view of a blobDB file
blobtools view -i nitz4.plastfree.blobplot.blobDB.json 1>bl.view.txt

#create bloboplot
blobtools blobplot -i nitz4.plastfree.blobplot.blobDB.json  1> blobplot.txt

```

[Blobplot_plastid-free_scaffolds](https://github.com/Nastassiia/Nitz4_annot_paper/blob/master/plots/nitz4.plastfree.scaffold.png)

## Blobplots for organellar-reads free genome

 - Organellar-free reads alignment to organelle-free scaffolded genome

```
/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2-build --seed 1 /storage/ao006/Nitz4_project/RAY_output/RAY.K45.org.free.scaff.nitz4/RAY.outp.45/nitz4.no.org.K45/Scaffolds.fasta nitz4.orgfree.scaff

/share/apps/bowtie2/bowtie2-2.2.8/bin/bowtie2 --seed 1 -k 1 --very-fast-local -p 16 -x /scratch/ao006/Nitz4_project/bowtie_runs/bowt.organ.free.reads.blobo/nitz4.K45.orgfree.DB/nitz4.orgfree.scaff \
-1 /storage/ao006/Nitz4_project/bowt.get.rid.mito.reads/nitz4_cont4.15_aln/Nitz4_no_organel_1.fq \
-2 /storage/ao006/Nitz4_project/bowt.get.rid.mito.reads/nitz4_cont4.15_aln/Nitz4_no_organel_2.fq \
--reorder -S nitz4.orgfree.reads.aln.sam

```

- Convert SAM alignment generated by bowtie2 to BAM

```
samtools view -S -b -T Scaff.K45.orgfree.nitz4.fasta nitz4.orgfree.reads.aln.sam -o nitz4.orgfree.reads.aln.bam

```

- Blast scaffold against Genbank 'non-redundant' database

```
blastn -task megablast -query ~/Desktop/Nitz4/blobo.organfree.scaff.nitz4/raw_stuff/Scaff.K45.orgfree.nitz4.fasta -db /home/nata/soft/blastdb/nt/nt -evalue 1e-5 -num_threads 4 -max_target_seqs 1 -outfmt '6 qseqid staxids bitscore' -out ~/Desktop/Nitz4/blobo.organfree.scaff.nitz4/nitz4.orgfree.K45.scaff.megablast 2>hist.megabl.txt  

```

 - Create Blobplots

```
#create bloboplots database
blobtools create -i raw_stuff/Scaff.K45.orgfree.nitz4.fasta -b raw_stuff/nitz4.orgfree.reads.aln.bam -t nitz4.orgfree.K45.scaff.megablast -o nitz4.orgfree.blobplot 1> bl.org.free.txt

#create a view of a blobDB file
blobtools view -i nitz4.orgfree.blobplot.blobDB.json 1> bl.view.txt

#create bloboplot
blobtools blobplot -i nitz4.orgfree.blobplot.blobDB.json 1> blobplot.txt

```
[Blobplot_organelle-free_scaffolds](https://github.com/Nastassiia/Nitz4_annot_paper/blob/master/plots/nitz4.orgfree.scaffolds.png)