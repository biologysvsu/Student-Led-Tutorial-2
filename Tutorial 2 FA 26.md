# Student-Led-Tutorial-2
# Task: Tutorial for De Novo Assembly Using SPAdes
# Date: October 22nd

## **Objective**
Create a tutorial for your peers on conducting a de novo genome assembly using the SPAdes assembler. This tutorial will walk students through preparing the data, executing SPAdes, assessing the quality of the assembly, and visualizing the results.

For this tutorial, we will be analyzing DNA sequencing data collected from seal feces, or seal poop. Seal feces can contain bacteria, viruses, and other microorganisms that may enter the surrounding environment through fecal contamination. Studying these samples can help scientists better understand microbial communities and investigate potential environmental health concerns.

Our goal is to take the short DNA sequences collected from these samples and assemble them into longer sequences. This is called de novo assembly, which means putting DNA sequences together without using a reference genome. We will then evaluate our assembly and visualize the results.

---
Before getting started, we need a few different programs. Each program has a specific purpose and helps us complete a different part of the assembly process.

## **Required Software**
1. **SPAdes**: For de novo genome assembly.
   - Will be done via slurm.
2. **QUAST**: For assembly quality assessment.
   - Will be done via slurm.
3. **Bandage**: For assembly visualization.
   - [Bandage GitHub](https://rrwick.github.io/Bandage/)
  
We will also use FastQC to check the quality of our DNA reads, Trimmomatic to clean them, and seqtk to make our dataset smaller.
---

## **Set Up**
   Log into HPC and go into your /ocean/projects/bio260081p/username 

```bash
cd /ocean/projects/bio260081p/user.name
```


## Install Dependencies
Before we can start analyzing the DNA, we need to make sure all the programs are installed. These programs will help us prepare, assemble, and evaluate our sequencing data.

We'll start by opening the installation script and updating the email address so we can receive notifications about our jobs.

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
- **Sample Dataset**: The data we will use is whole genomic data from an environmental source. We will specifically analyze sequences obtained from seal feces to determine whether they pose a threat of bacterial and viral fecal contamination. 
- Download the paired-end seal poop FASTQ files:
```bash
fastq-dump --split-files ERR13601257
```

- Output files:
   - ERR13601257_1.fastq (forward reads).
   - ERR13601257_2.fastq (reverse reads).
 
We have two files because the DNA was sequenced using paired-end sequencing. This means DNA fragments were read from both ends, giving us more information to help assemble the sequences.

- Subsample files for the tutorial, otherwise, the run will take up a lot of time.
```bash
#Make sure seqtk is installed in the HPC beforehand. If not, use a conda environment.
seqtk sample -s100 ERR13601257_1.fastq 0.05 > ERR13601257_subsampled_1.fastq   #Samples 5% of reads in file
seqtk sample -s100 ERR13601257_2.fastq 0.05 > ERR13601257_subsampled_2.fastq   #Samples 5% of reads in file
```

## **Tasks and Deliverables**
Before putting our DNA sequences together, we need to make sure we're working with good-quality data. Poor-quality sequences or leftover adapters can make it harder to assemble the DNA correctly.

This is why we'll check the quality of our sequences first and clean them if necessary.
### **Part 1: Data Preparation**
1. Download the raw sequencing data in FASTQ format (using the command above).
2. Perform a quality check on the reads using FastQC (optional but recommended):
   - FastQC Documentation.
   - Run FastQC;
FastQC allows us to look at the overall quality of our DNA sequences. It checks for things like low-quality bases, adapter contamination, and unusual sequence patterns.

This gives us an idea of whether our data are ready for assembly or need additional cleaning.
```bash
./FastQC/fastqc ERR13601257_subsampled_1.fastq ERR13601257_subsampled_2.fastq #if using subsamples, replace file names accordingly.
```

3. Optionally trim low-quality bases and adapters using Trimmomatic:
After looking at our FastQC results, we can use Trimmomatic to clean up our reads. Trimmomatic removes matching adapter sequences and trims sections of DNA that have low sequencing quality.

For our sample, FastQC detected adapter contamination, so trimming is a useful step before moving on to SPAdes.
```bash
   java -jar ./Trimmomatic-0.39/trimmomatic-0.39.jar PE -phred33 ERR13601257_subsampled_1.fastq ERR13601257_subsampled_2.fastq trimmed_1.fastq unpaired_1.fastq trimmed_2.fastq unpaired_2.fastq ILLUMINACLIP:adapters.fa:2:30:10 SLIDINGWINDOW:4:20 MINLEN:36
```   
Now that we've checked and cleaned our reads, we're ready for the main part of our tutorial: assembling the DNA sequences.
### **Part 2: Running SPAdes**
This is where we start putting our DNA pieces together.

SPAdes takes the short DNA reads and finds overlapping regions between them. It uses those overlaps to reconstruct longer DNA sequences called contigs.

Since we're assembling the sequences without using an existing reference genome, this process is called de novo assembly.

1. Perform de novo assembly using SPAdes:
```bash
python3 ./SPAdes-3.15.0-Linux/bin/spades.py -1 trimmed_1.fastq -2 trimmed_2.fastq -o spades_output -t 16 -m 28
```
- -1 and -2: Paired-end reads.
- -o: Specify the output directory (e.g., spades_output).

2. Explore the SPAdes output files:
   - *contigs.fasta*: Assembled contigs. 
   - *scaffolds.fasta*: Assembled scaffolds (contigs linked by paired-end reads).
   - *assembly_graph.gfa*: Graph representation of the assembly using Bandage.

### **Part 3: Assessing Assembly Quality**
QUAST is a program that helps us understand the quality and characteristics of our assembly.

It gives us measurements such as how many DNA sequences were assembled, how long they are, and how fragmented the assembly might be.

These measurements help us evaluate our results, although they cannot confirm accuracy by themselves.
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
1. Go to https://ondemand.bridges2.psc.edu/
- Go to your oceans/projects/bio260081p/*username*/spades.slurm
- Download the file assembly_graph.fastg
2. Use Bandage to visualize the assembly graph:
- Load the SPAdes assembly graph (spades_output/assembly_graph.fastg) into Bandage.
  
### **Part 5: Blasting Contigs**
- Identify contigs and select a node to BLAST
- Go to options and press Wed BLAST selected node
- Under Program Selection choose somewhat similar sequences
- BLAST your node and see what you get!
