# Metagenomics_for_wastewater_surveillance
## 1.Data source
The samples were collected from wastewater treatment plants located in Moscow, Russia.
## 2.Obtaining the data
The amplicon paired-end reads were obtaiend from the NCBI database with sratoolkit: ```module load sratoolkit.3.0.7``` then ```fasterq-dump SRR23892276 --split-files```. subsamples were used in this workflow.
## 3.Workflow
This workflow exaplains the processing of metagenomic data processing to operational Taxonomic Units used for organism classification.
### 3.1 Pairwise overlapping reads were merged using FLASH v.1.2.11
```module load flash-1.2.11``` was ran to load the tool into the enivironmnet. 
<pre>flash SRR23892276_1.fastq  SRR23892276_2.fastq -o SRR23892276</pre>
Output is a a combined ```.fastq``` file
### 3.2 Quality check
The tool ```fastqc``` was used to check the read quality . ```module load fastqc-0.11.7```  was ran to load the tool into the enivironmnet and the tool was ran:
<pre>fastqc SRR23892276.extendedFrags.fastq</pre>
Output is an ```.html``` file that you can view in your web browser. 
Trimming of low quality was then performed with ```sickle``` for the read:
<pre>sickle se -f SRR23892276.extendedFrags.fastq -t sanger -o SRR23892276_merged_trimmed.fastq -q 30</pre>
Output is a trimmed ```.fastq``` file.
### 3.3 Sequences clustering into operational taxonomic units and dereplicate sequences
In this step, long reads were assembled and refined using short reads to construct the genome
```module load usearch```  was ran to load the tool into the enivironmnet. 
<pre>usearch -fastx_uniques SRR23892276_merged_trimmed.fastq -fastaout uniques.fa -sizeout</pre>
Output is a ```.fa``` file
### 3.4 OTU clustering at 97% identity, removal of singletons, chimera filtering
<pre>usearch -cluster_otus uniques.fa -otus otus.fa -relabel OTU</pre>
Output is ```otus.fa``` which contain high-quality OTU representative sequences
### 3.5 Map reads back to OTUs to calculate abundance table
<pre>usearch -usearch_global SRR23892276_merged_trimmed.fastq -db otus.fa -id 0.97 -strand both -otutabout otu_table.txt</pre>
Output is ```.txt``` is the OTU count matrix for downstream analysis auch as alpha-beta diversity and taxonomy assignment




