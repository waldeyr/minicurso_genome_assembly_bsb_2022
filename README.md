# :school: Minicurso Montagem de Genoma


## :point_right: Setup the environment

#### How to install Conda?

* Download Anaconda software from  https://www.anaconda.com/products/individual
* Install it (https://docs.anaconda.com/anaconda/install)

#### How to set up the environment using conda?

##### Option 01:

Download the file [environment.yml](https://raw.githubusercontent.com/waldeyr/minicurso_genome_assembly_bsb_2022/main/environment.yml)

`conda env create -f environment.yml`

##### Option 02:

`conda create -n pipelines python=3.6 r=3.6 -y`

#### How to enter in the conda environment?

`conda activate pipelines`

#### How to setup the channels (repositories) with the needed tools?

```
conda config --add channels bioconda
conda config --add channels conda-forge
```

#### How to install the needed tools into the DisciplinaBioinfo environment?

`conda install pandas numpy jupyterlab jupyter nano readline=6.2 sra-tools entrez-direct bwa fastqc fastp spades quast star htseq seqtk samtools=1.13 bcftools=1.13 r-xml freebayes bedtools vcflib rtg-tools matplotlib -y`

* R packages (run it from the R prompt):

`install.packages('IRkernel')`

`install.packages("BiocManager")`
    
`BiocManager::install(c("limma","edgeR","Glimma","data.table","org.Mm.eg.db", "statmod"))`


## :notebook_with_decorative_cover: De novo assembly of a Brazillian isolate of Sars-Cov-2 Genome

### Enter in the conda environmet

`conda activate pipelines`


### Obtaining the raw material

* SARS-CoV-2 genome sequencing Rio Grande do Sul/Brazil, Dec 2020; Total RNA from SARS-CoV-2 positive samples was converted to cDNA. Viral whole-genome amplification was performed according to the Artic Network. Available in [SRR13510367](https://trace.ncbi.nlm.nih.gov/Traces/sra/?run=SRR13510367).

`fastq-dump --accession SRR13510367 --split-files --outdir rawdata -v`

### Generating a quality report for the sequenced reads

`fastqc rawdata/SRR13510367_1.fastq rawdata/SRR13510367_2.fastq`

### Filtering data with fastp

`mkdir filtered_data`

`fastp --thread 4 -p -q 30 -i rawdata/SRR13510367_1.fastq -I rawdata/SRR13510367_2.fastq -o filtered_data/SRR13510367_1_FILTERED.fastq -O filtered_data/SRR13510367_2_FILTERED.fastq &`

`mv fastp.* filtered_data/`

### Running the asembly with Spades

`spades.py -t 4 -o assembly --careful -k 21,33,55 -1 filtered_data/SRR13510367_1_FILTERED.fastq -2 filtered_data/SRR13510367_2_FILTERED.fastq &`

### generate a report about the assembly with Quast

* Download the Sars-Cov-2 reference genome

`esearch -db nucleotide -query "NC_045512.2" | efetch -format fasta > NC_045512.2.fasta`

* Performe the report

`quast assembly/scaffolds.fasta -R NC_045512.2.fasta`
