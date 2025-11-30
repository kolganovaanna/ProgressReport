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

```bash
echo "File size:" >> results/run1_general_info.txt
ls -lh data/SRR14784363.fastq  >> results/run1_general_info.txt

echo "File size:" >> results/run2_general_info.txt
ls -lh data/SRR14784377.fastq >> results/run2_general_info.txt

echo "Total lines:" >> results/run1_general_info.txt
wc -l data/SRR14784363.fastq >> results/run1_general_info.txt

echo "Total lines:" >> results/run2_general_info.txt
wc -l data/SRR14784377.fastq >> results/run2_general_info.txt

echo "Reads (no headers):" >> results/run1_general_info.txt
grep -v "^@" data/SRR14784363.fastq | wc -l >> results/run1_general_info.txt

echo "Reads (no headers):" >> results/run2_general_info.txt
grep -v "^@" data/SRR14784377.fastq | wc -l >> results/run2_general_info.txt
```