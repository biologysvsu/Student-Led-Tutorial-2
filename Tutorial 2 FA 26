# Student-Led-Tutorial-2
# Task: Tutorial for De Novo Assembly Using SPAdes
# Date: February 20th

## **Objective**
Create a tutorial for your peers on conducting a de novo genome assembly using the SPAdes assembler. This tutorial will walk students through preparing the data, executing SPAdes, assessing the quality of the assembly, and visualizing the results.

---

## **Required Software**
1. **SPAdes**: For de novo genome assembly.
   - Will be done via slurm.
2. **QUAST**: For assembly quality assessment.
   - Will be done via slurm.
3. **Bandage**: For assembly visualization.
   - [Bandage GitHub](https://rrwick.github.io/Bandage/)

---

## **Set Up**
   Log into HPC and go into your /ocean/projects/agr250001p/username 

```bash
cd /ocean/projects/agr250001p/user.name
```
## Create New Fork 
Create your own fork of 'Student-Led-Tutorial-2' to have your own copy of the repository.
  
```bash
git clone https://github.com/user-name/Student-Led-Tutorial-2.git
```
```bash
cd Student-Led-Tutorial-2
```
```bash
interact
```

## Install Dependencies
- Edit .slurm script.

```bash
vi install_dependencies.slurm
```
- Edit email address. (I don't want your emails)

- Download the .slurm script.
```bash
source install_dependencies.slurm
```

## **Data to Use**
- **Sample Dataset**: The data we will use is whole genomic data from an anvironmental source. Specifically sequences obtained from seal feces to determine whether they pose a threat of bacterial and viral fecal contamination. 
- Download the paired-end seal poop FASTQ files:
```bash
fastq-dump --split-files ERR13601257
```

- Output files:
   - ERR13601257_1.fastq (forward reads).
   - ERR13601257_2.fastq (reverse reads).
- Subsample files for the tutorial, otherwise, the run will take up a lot of time.
```bash
#Make sure seqtk is installed in the HPC beforehand. If not, use a conda environment.
seqtk sample -s100 ERR13601257_1.fastq 0.25 > ERR13601257_subsampled_1.fastq   #Samples 25% of reads in file
seqtk sample -s100 ERR13601257_2.fastq 0.25 > ERR13601257_subsampled_2.fastq   #Samples 25% of reads in file
```

## **Tasks and Deliverables**
### **Part 1: Data Preparation**
1. Download the raw sequencing data in FASTQ format (using the command above).
2. Perform a quality check on the reads using FastQC (optional but recommended):
   - FastQC Documentation.
   - Run FastQC;

```bash
./FastQC/fastqc ERR13601257_subsampled_1.fastq EER13601257_subsampled_2.fastq #if using subsamples, replace file names accordingly.
```

3. Optionally trim low-quality bases and adapters using Trimmomatic:
```bash
   java -jar ./Trimmomatic-0.39/trimmomatic-0.39.jar PE -phred33 ERR13601257_subsampled_1.fastq ERR13601257_subsampled_2.fastq trimmed_1.fastq unpaired_1.fastq trimmed_2.fastq unpaired_2.fastq ILLUMINACLIP:adapters.fa:2:30:10 SLIDINGWINDOW:4:20 MINLEN:36
```   

### **Part 2: Running SPAdes**
1. Perform de novo assembly using SPAdes:
```bash
python3 ./SPAdes-3.15.0-Linux/bin/spades.py -1 trimmed_1.fastq -2 trimmed_2.fastq -o spades_output
```
- -1 and -2: Paired-end reads.
- -o: Specify the output directory (e.g., spades_output).

2. Explore the SPAdes output files:
   - *contigs.fasta*: Assembled contigs. 
   - *scaffolds.fasta*: Assembled scaffolds (contigs linked by paired-end reads).
   - *assembly_graph.gfa*: Graph representation of the assembly using Bandage.

### **Part 3: Assessing Assembly Quality**
1. Use QUAST to evaluate assembly quality:
```bash
python3 ./quast-5.3.0/quast.py -o quast_output spades_output/contigs.fasta
```   

2. Review key metrics:
   - N50: Length of the shortest contig in the smallest set covering 50% of the genome.
   - Total number of contigs.
   - Total assembly length.
   - GC content.

### **Part 4: Visualizing the Assembly**
1. Use Bandage to visualize the assembly graph:
- Load the SPAdes assembly graph (spades_output/assembly_graph.gfa) into Bandage.
- Identify contigs and potential misassemblies.
- Save a screenshot of the graph.
