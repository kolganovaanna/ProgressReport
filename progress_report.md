## General information

- Author: Anna kolganova
- Date: 2025-11-30
- Environment: Pitzer cluster at OSC via VS Code
- Working dir: `/fs/ess/PAS2880/users/kolganovaanna/FinalProject/ProgressReport`

## Assignment background

This is a final project progress report focusing on analyzing 16S sequencing data obtained by processing rumen fluid samples. 

## Protocol

**Part A**: Obtaining and describing samples 

1. Study selection and description

I obtained samples from the study proposed and found by Menuka: [research paper](https://academic.oup.com/ismej/article/16/11/2535/7474101#435086839). This study focuses on exploring metabolically distinct microorganisms found in the rumen microbiota of 24 beef cattle. I was interested in the study because I work with ruminal microbiome in the scope of my Ph.D. research. Analyzing such datasets can help me work on my own data in the future. 


2. Obtaining specific samples

I obtain specific samples by searching PRJNA736529 project number (the study project number) on the NIH website: [datasets](https://www.ncbi.nlm.nih.gov/biosample?LinkName=bioproject_biosample_all&from_uid=736529)
I chose samples #1 (run SRR14784363) and #14 (run SRR14784377). 


3. Run descriptions

* Run SRR14784363

This run consists of 99.44% identified reads and 0.56% unidentified reads. Out of the 99.44%, domain Bacteria composes 99.24% and domain Archaea contributes only 0.15%. The length of each read is 250. Learn more about the run using this link: [SRR14784363](https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&page_size=10&acc=SRR14784363&display=metadata)

* Run SRR14784377

This run consists of 97.83% identified reads and 2.17% unidentified reads. Out of the 97.83%, domain Bacteria composes 96.80% and domain Archaea contributes only 0.95%. The length of each read is 250. Learn more about the run using this link: [SRR14784377](https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&page_size=10&acc=SRR14784377&display=metadata)

Menuka kindly helped me to download both forward and reverse reads. I copied these files from this directory: '/fs/ess/PAS2880/users/menuka/Anna_help'. Each run corresponds to one biological sample, but it includes multiple files: the lite file plus the paired-end FASTQ files (_1 for forward reads and _2 for reverse reads).

I chose 2 runs instead of 1 to make sure that the code I am going to develop is reproducible, as reproducibility is one of the core principles of any data analysis. Additionally, the first run contains substantially fewer Archaea compared to the second run, while the second run also has slightly fewer Bacteria. Comparing the sequences from these two runs can provide insights into differences in the structure of their microbial populations. It is possible that some of the rumen fluid donors (the 24 beef cattle) are lower methane emitters than others due to variations in their microbiome composition, which is an interesting distinction to explore.


**Part B**

4. Setting up

To create the directory for this project, I used the following commands:

```bash
mkdir ProgressReport/
```

To structurize the project, I created a couple of directories within ProjectProposal:

```bash
touch progress_report.md
mkdir data results scripts
```

I went into this directory '/fs/ess/PAS2880/users/kolganovaanna/FinalProject/ProjectProposal/data_Menuka/' to copy the files with reads into 'ProgressReport/data/' directory, in which I also created 2 separate folders for the runs (I did the same for 'results/'). For copying files, I used these commands:

```bash
cp SRR14784363.lite.1 \
   SRR14784363.lite.1_1.fastq \
   SRR14784363.lite.1_2.fastq \
   /fs/ess/PAS2880/users/kolganovaanna/FinalProject/ProgressReport/data/SRR14784363/

cp SRR14784377.lite.1 \
   SRR14784377.lite.1_1.fastq \
   SRR14784377.lite.1_2.fastq \
   /fs/ess/PAS2880/users/kolganovaanna/FinalProject/ProgressReport/data/SRR14784377/
```
I then returned back to /fs/ess/PAS2880/users/kolganovaanna/FinalProject/ProgressReport/ using 'cd'. 


I then initialized a Git repository, added .gitignore, and commited to README for the first time. 

```bash
git init
git add progress_report.md
git commit -m "Committing to progress_report to set up"
echo "results/" > .gitignore
echo "data/" >> .gitignore
git add .gitignore
git commit -m "Adding a Gitignore file"
```

5. General analysis of the reads

First, I want to gather some general information about these files. I ran the following commands: 

```bash
ls -lh data/SRR14784363/SRR14784363.lite.1_1.fastq
ls -lh data/SRR14784363/SRR14784363.lite.1_2.fastq
ls -lh data/SRR14784377/SRR14784377.lite.1_1.fastq
ls -lh data/SRR14784377/SRR14784377.lite.1_2.fastq
```

The outputs were:

```bash
-rw-rw----+ 1 kolganovaanna PAS2880 42M Nov 30 13:09 data/SRR14784363/SRR14784363.lite.1_1.fastq
-rw-rw----+ 1 kolganovaanna PAS2880 42M Nov 30 13:09 data/SRR14784363/SRR14784363.lite.1_2.fastq

-rw-rw----+ 1 kolganovaanna PAS2880 41M Nov 30 13:09 data/SRR14784377/SRR14784377.lite.1_1.fastq
-rw-rw----+ 1 kolganovaanna PAS2880 41M Nov 30 13:09 data/SRR14784377/SRR14784377.lite.1_2.fastq
```
This tells us that the files are approximately the same size 


I then counted the total number of lines and the number of gemonic features in the files using these commands:
> results/
```bash
wc -l data/SRR14784363/SRR14784363.lite.1_1.fastq 
wc -l data/SRR14784363/SRR14784363.lite.1_2.fastq

grep -v "^@" data/SRR14784363/SRR14784363.lite.1_1.fastq | wc -l
grep -v "^@" data/SRR14784363/SRR14784363.lite.1_2.fastq | wc -l

wc -l data/SRR14784377/SRR14784377.lite.1_1.fastq 
wc -l data/SRR14784377/SRR14784377.lite.1_2.fastq

grep -v "^@" data/SRR14784377/SRR14784377.lite.1_1.fastq | wc -l
grep -v "^@" data/SRR14784377/SRR14784377.lite.1_2.fastq | wc -l
```

The outputs were:

```bash
296916 data/SRR14784363/SRR14784363.lite.1_1.fastq
296916 data/SRR14784363/SRR14784363.lite.1_2.fastq

222687
222687

287948 data/SRR14784377/SRR14784377.lite.1_1.fastq
287948 data/SRR14784377/SRR14784377.lite.1_2.fastq

215961
215961
```

I will have the outputs printed in the 2 separate files under results/SRR14784363 or results/SRR14784377. I will use ">>" insteaf of ">" so that the outputs don't overwrite each other. I will also make the file readable so it's not just a bunch of unstructurized information:

```bash
echo "File size:" >> results/SRR14784363/r1_general_info.txt
ls -lh  data/SRR14784363/SRR14784363.lite.1_1.fastq  >> results/SRR14784363/r1_general_info.txt

echo "File size:" >> results/SRR14784363/r2_general_info.txt
ls -lh  data/SRR14784363/SRR14784363.lite.1_2.fastq  >> results/SRR14784363/r2_general_info.txt

echo "File size:" >> results/SRR14784377/r1_general_info.txt
ls -lh  data/SRR14784377/SRR14784377.lite.1_1.fastq  >> results/SRR14784377/r1_general_info.txt

echo "File size:" >> results/SRR14784377/r2_general_info.txt
ls -lh  data/SRR14784377/SRR14784377.lite.1_2.fastq  >> results/SRR14784377/r2_general_info.txt

echo "Total lines:" >> results/SRR14784363/r1_general_info.txt
wc -l data/SRR14784363/SRR14784363.lite.1_1.fastq  >> results/SRR14784363/r1_general_info.txt

echo "Total lines:" >> results/SRR14784363/r2_general_info.txt
wc -l data/SRR14784363/SRR14784363.lite.1_2.fastq  >> results/SRR14784363/r2_general_info.txt

echo "Total lines:" >> results/SRR14784377/r1_general_info.txt
wc -l  data/SRR14784377/SRR14784377.lite.1_1.fastq  >> results/SRR14784377/r1_general_info.txt

echo "Total lines:" >> results/SRR14784377/r2_general_info.txt
wc -l  data/SRR14784377/SRR14784377.lite.1_2.fastq  >> results/SRR14784377/r2_general_info.txt

echo "Reads(no headers):" >> results/SRR14784363/r1_general_info.txt
grep -v "^@"  data/SRR14784363/SRR14784363.lite.1_1.fastq | wc -l  >> results/SRR14784363/r1_general_info.txt

echo "Reads(no headers):" >> results/SRR14784363/r2_general_info.txt
grep -v "^@"  data/SRR14784363/SRR14784363.lite.1_2.fastq | wc -l  >> results/SRR14784363/r2_general_info.txt

echo "Reads(no headers):" >> results/SRR14784377/r1_general_info.txt
grep -v "^@"  data/SRR14784377/SRR14784377.lite.1_1.fastq | wc -l >> results/SRR14784377/r1_general_info.txt

echo "Reads(no headers):" >> results/SRR14784377/r2_general_info.txt
grep -v "^@"  data/SRR14784377/SRR14784377.lite.1_2.fastq | wc -l >> results/SRR14784377/r2_general_info.txt
```

6. Printing specific lines from the files

I want to look at some specific examples of reads that my fastq files contain. I remember from GA3 that in order for this to happen, we need a script. I will create an .sh file under scripts/

```bash
touch scripts/lines.sh
```

The write a script that will show first and last reads (each read = 4 lines):

```bash
#!/bin/bash
set -euo pipefail

file="$1"

echo "First read in $file:"
head -n 4 "$file"

echo ""
echo "Last read in $file:"
tail -n 4 "$file"
```

Then, I am going to run it for my fastq files:

```bash
bash scripts/lines.sh  data/SRR14784363/SRR14784363.lite.1_1.fastq  > results/SRR14784363/printed_lines_r1.txt
bash scripts/lines.sh  data/SRR14784363/SRR14784363.lite.1_2.fastq  > results/SRR14784363/printed_lines_r2.txt
bash scripts/lines.sh  data/SRR14784377/SRR14784377.lite.1_1.fastq  > results/SRR14784377/printed_lines_r1.txt
bash scripts/lines.sh  data/SRR14784377/SRR14784377.lite.1_2.fastq  > results/SRR14784377/printed_lines_r2.txt
```

In the Project proposal, I involved Github Copilot (agent) here to answer what do the question marks we see in the output files mean.

My prompt was: "# Explain simply and briefly what ???? mean under sequences reads"

The answer: "Those question marks are the per‑base quality scores for each base in the read. In modern FASTQ (Phred+33) the character '?' (ASCII 63) encodes a Phred score of 30, which means the base call is high quality (≈0.1% error probability)."

**Note**: Please let me know if adding screenshots of the promt and the answer is required. 

This tells me that my reads are good quality. 

7. Using Kraken in attempt to separate Bacteria from Archaea

In the proposal, I mentioned that I might want to explore if I can tell which reads belong to Archaea and which to Bacteria. Menuka confirmed that `Kraken` can be used for this purpose. However, I am still not able to figure out how to install it. I tried using 'kraken2' command but it didn't work because I dont kraken installed. I can't figure out how to do it and will need help with this, if I am to include it in my final project.

I then updated my GitHub repo:

```bash
git add scripts/*.sh progress_report.md
git commit -m "Part B"
```

