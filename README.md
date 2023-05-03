## Table of contents
0. [Download Files](#download)
1. [Introduction](#intro)
2. [CARD Website and Antibiotic Resistance Ontology](#cardweb)
3. [RGI for Genome Analysis](#rgigenome)
4. [RGI at the Command Line](#rgicommand)

<a name="download"></a>
## Download Files

If you are doing this demo live, you can download all the files we will be viewing here: https://github.com/agmcarthur/vtec2023-amr/tree/main/downloads_for_demo

<a name="intro"></a>
## Introduction

This module gives an introduction to prediction of antimicrobial resistome and phenotype based on comparison of genomic DNA sequencing data to reference sequence information. While there is a large diversity of reference databases and software, this tutorial is focused on the Comprehensive Antibiotic Resistance Database ([CARD](http://card.mcmaster.ca)) for genomic AMR prediction.

There are several databases (see [here](https://www.nature.com/articles/s41576-019-0108-4/tables/2) for a list) which try and organise information about AMR as well as helping with interpretation of resistome results. Many of these are either specialised on a specific type of resistance gene (e.g., [beta-lactamases](http://bldb.eu/)), organism (e.g., [_Mycobacterium tuberculosis_](https://github.com/jodyphelan/tbdb)), or are an automated amalgamation of other databases (e.g., [MEGARes](https://megares.meglab.org/)). There are also many tools for detecting AMR genes each with their own strengths and weaknesses (see [this paper](https://www.frontiersin.org/articles/10.3389/fpubh.2019.00242/full) for a non-comprehensive list of tools!).

The "Big 3" databases that are comprehensive (involving many organisms, genes, and types of resistance), regularly updated, have their own gene identification tool(s), and are carefully maintained and curated are: 

1. Comprehensive Antibiotic Resistance Database ([CARD](https://card.mcmaster.ca)) with the Resistance Gene Identifier ([RGI](https://github.com/arpcard/rgi)).
2. National Center for Biotechnology Information's National Database of Antibiotic Resistant Organisms ([NDARO](https://www.ncbi.nlm.nih.gov/pathogens/antimicrobial-resistance/)) with [AMRFinderPlus](https://www.ncbi.nlm.nih.gov/pathogens/antimicrobial-resistance/AMRFinder/).
3. [ResFinder](https://cge.cbs.dtu.dk/services/ResFinder/) database with its associated [ResFinder](https://bitbucket.org/genomicepidemiology/resfinder/src/master/) tool.

In this practical we are going to focus on CARD and the associated RGI tool because:
* The [Antibiotic Resistance Ontology](https://card.mcmaster.ca/ontology/36006) it is built upon is a great way to organize information about AMR.
* CARD is the most heavily used database internationally, with over 5000 citations.
* We are biased. CARD is Canadian and pretty much all the workshop faculty collaborate or are part of the group that develops CARD! See [Alcock *et al.* 2023. CARD 2023: expanded curation, support for machine learning, and resistome prediction at the Comprehensive Antibiotic Resistance Database. *Nucleic Acids Research*, 51, D690-D699](https://pubmed.ncbi.nlm.nih.gov/36263822/).

<a name="cardweb"></a>
## CARD Website and Antibiotic Resistance Ontology

The relationship between AMR genotype and AMR phenotype is complicated and no tools for complete prediction of phenotype from genotype exist. Instead, analyses focus on prediction or catalog of the AMR resistome - the collection of AMR genes and mutants in the sequenced sample. While BLAST and other sequence similarity tools can be used to catalog the resistance determinants in a sample via comparison to a reference sequence database, interpretation and phenotypic prediction are often the largest challenge. To start the tutorial, we will use the Comprehensive Antibiotic Resistance Database ([CARD](http://card.mcmaster.ca)) website to examine the diversity of resistance mechanisms, how they influence bioinformatics analysis approaches, and how CARD’s [Antibiotic Resistance Ontology](https://card.mcmaster.ca/ontology/36006) (ARO) can provide an organizing principle for interpretation of bioinformatics results.

CARD’s website provides the ability to: 

* Browse the [Antibiotic Resistance Ontology](https://card.mcmaster.ca/ontology/36006) (ARO) and associated knowledgebase.
* Browse the underlying AMR detection models, reference sequences, and SNP matrices.
* Download the ARO, reference sequence data, and indices in a number of formats for custom analyses.
* Perform integrated genome analysis using the Resistance Gene Identifier (RGI).

In this part of the tutorial, your instructor will walk you through the following use of the CARD website to familiarize yourself with its resources:

1. What are the mechanisms of resistance described in the Antibiotic Resistance Ontology?
2. Examine the NDM-1 beta-lactamase protein, it’s mechanism of action, conferred antibiotic resistance, it’s prevalence, and it’s detection model. 
3. Examine the AAC(6')-Iaa aminoglycoside acetyltransferase, it’s mechanism of action, conferred antibiotic resistance, it’s prevalence, and it’s detection model. 
4. Examine the fluoroquinolone resistant gyrB for *M. tuberculosis*, it’s mechanism of action, conferred antibiotic resistance, and it’s detection model. 
5. Examine the MexAB-OprM efflux complex with MexR mutations, it’s mechanism of action, conferred antibiotic resistance, it’s prevalence, and it’s detection model(s). 

<details>
  <summary>Answers:</summary>
    
1. 
	+ antibiotic target alteration
	+ antibiotic target replacement
	+ antibiotic target protection
	+ antibiotic inactivation
	+ antibiotic efflux
	+ reduced permeability to antibiotic
	+ resistance by absence
	+ modification to cell morphology
	+ resistance by host-dependent nutrient acquisition   
2. NDM-1: antibiotic inactivation; beta-lactams (penam, cephamycin, carbapenem, cephalosporin); over 40 pathogens (lots of ESKAPE pathogens) - note strong association with plasmids; protein homolog model
3. AAC(6')-Iaa: antibiotic inactivation; aminogylcosides; _Salmonella enterica_; protein homolog model
4. gyrB: antibiotic target alteration; fluoroquinolones; _Mycobacterium_; protein variant model
5. MexAB-OprM with MexR mutations: antibiotic efflux; broad range of drug classes; looking at MexA sub-unit: _Pseudomonas_; efflux meta-model
                
</details>
 
<a name="#rgigenome"></a>
## RGI for Genome Analysis

As illustrated by the exercise above, the diversity of antimicrobial resistance mechanisms requires a diversity of detection algorithms and a diversity of detection limits. CARD’s Resistance Gene Identifier (RGI) currently integrates four CARD detection models: [Protein Homolog Model, Protein Variant Model, rRNA Variant Model, and Protein Overexpression Model](https://github.com/arpcard/rgi#analyzing-genomes-genome-assemblies-metagenomic-contigs-or-proteomes-a-k-a-rgi-main). Unlike naïve analyses, CARD detection models use curated cut-offs, currently based on BLAST/DIAMOND bitscore cut-offs. Many other available tools are based on BLASTN or BLASTP without defined cut-offs and avoid resistance by mutation entirely. 

In this part of the tutorial, your instructor will walk you through the following use of CARD’s [Resistome Gene Identifier](https://card.mcmaster.ca/analyze/rgi) with default settings “Perfect and Strict hits only”, "Exclude nudge", and "High quality/coverage":

* Resistome prediction for the multidrug resistant *Acinetobacter baumannii* MDR-TJ, complete genome (NC_017847).
* Resistome prediction for the plasmid isolated from *Escherichia coli* strain MRSN388634 plasmid (KX276657).
* Explain the difference in fluoroquinolone resistance MIC between two clinical strains of *Pseudomonas aeruginosa* that appear clonal based on identical MLST ([`Pseudomonas1.fasta`, `Pseudomonas2.fasta`](https://github.com/bioinformaticsdotca/IDE_2023/tree/main/module5/sequences_for_web_demo) - these files can be found in this GitHub repo). Hint, look at SNPs.

<details>
  <summary>Answers:</summary>

The first two examples list the predicted resistome of the analyzed genome and plasmid, while the third example illustrates that `Pseudomonas2.fasta` contains an extra T83I mutation in gyrA conferring resistance to fluoroquinolones, above that provided by background efflux.
                
</details>

<a name="rgicommand"></a>
## RGI at the Command Line

RGI is a command line tool as well, so we’ll do a demo analysis of 112 clinical multi-drug resistant *E. coli* from Hamilton area hospitals, sequenced on MiSeq and assembled using SPAdes (an older genome assembler). We’ll additionally try RGI’s heat map tool to compare genomes.

Login into your course account’s working directory and make a module6 directory:

```bash
cd ~/workspace
mkdir module6
cd module6
```

Take a peak at the list of E. coli samples:

```bash
ls /home/ubuntu/CourseData/IDE_data/module6/ecoli
```

RGI has already been installed using Conda, list all the available software in Conda, activate RGI, and then review the RGI help screen:

```bash
conda env list
conda activate rgi
rgi -h
```

First we need to acquire the latest AMR reference data from the CARD website:

```bash
rgi load -h
wget https://card.mcmaster.ca/latest/data
tar -xvf data ./card.json
less card.json
rgi load --card_json ./card.json --local
ls
```

We don’t have time to analyze all 112 samples, so let’s analyze 1 as an example (the course GitHub repo contains an EXCEL version of the resulting [`C0001.txt`](https://github.com/bioinformaticsdotca/IDE_2023/blob/main/module6/rgi_main_results/C0001.xlsx) file). When analyzing FASTA files we use the **main** sub-command, here with default settings “Perfect and Strict hits only”, "Exclude nudge", and "High quality/coverage":

```bash
rgi main -h
rgi main -i /home/ubuntu/CourseData/IDE_data/module6/ecoli/C0001_E_coli.contigs.fasta -o C0001 -t contig -a DIAMOND -n 4 --local --clean
ls
less C0001.json
less C0001.txt
column -t -s $'\t' C0001.txt  | less -S
```

<details>
  <summary>Discussion Points:</summary>

Default RGI **main** analysis of C0001 lists 17 Perfect annotations and 52 Strict annotations. Yet, 44 annotations are efflux components common in *E. coli* that may or may not lead to clinical levels of AMR. Nonetheless, outside of efflux there are some antibiotic inactivation, target replacement, or target alteration genes known to be high risk (e.g., sul1, TEM-1, CTX-M-15, APH(6)-Id, and gyrA mutations). This is a MDR isolate of *E. coli*.
                
</details>

What if these results did not explain our observed phenotype? We might want to explore the RGI Loose hits (the course GitHub repo contains an EXCEL version of the resulting [C0001_IncludeLoose.txt](https://github.com/bioinformaticsdotca/IDE_2023/blob/main/module6/rgi_main_results/C0001_IncludeLoose.xlsx) file), shown here with settings “Perfect, Strict, and Loose hits”, "Include nudge", and "High quality/coverage":

```bash
rgi main -h
rgi main -i /home/ubuntu/CourseData/IDE_data/module6/ecoli/C0001_E_coli.contigs.fasta -o C0001_IncludeLoose -t contig -a DIAMOND -n 4 --local --clean --include_nudge --include_loose
ls
column -t -s $'\t' C0001_IncludeLoose.txt  | less -S
```

<details>
  <summary>Discussion Points:</summary>

An additional 3 nudged Strict annotations (*Escherichia coli* PtsI with mutation conferring resistance to fosfomycin, EC-5 beta-lactamase, *Escherichia coli* EF-Tu mutants conferring resistance to pulvomycin) and 390 Loose annotations have been added to investigate for leads that could explain the observed phenotype. Note this scenario is unlikely for clinical isolates given CARD's reference data, but is possible for environmental isolates.
                
</details>

We have pre-compiled results for all 112 samples under “Perfect and Strict hits only”, "Exclude nudge", and "High quality/coverage", so let’s try RGI’s heat map tool ([pre-compiled images](https://github.com/bioinformaticsdotca/IDE_2023/tree/main/module6/rgi_main_results) can be downloaded from the course GitHub repo) (please ignore the FutureWarning):

```bash
ls /home/ubuntu/CourseData/IDE_data/module6/ecoli_json
rgi heatmap -h
rgi heatmap -i /home/ubuntu/CourseData/IDE_data/module6/ecoli_json -o genefamily_samples --category gene_family --cluster samples
rgi heatmap -i /home/ubuntu/CourseData/IDE_data/module6/ecoli_json -o drugclass_samples --category drug_class --cluster samples
rgi heatmap -i /home/ubuntu/CourseData/IDE_data/module6/ecoli_json -o cluster_both --cluster both
rgi heatmap -i /home/ubuntu/CourseData/IDE_data/module6/ecoli_json -o cluster_both_frequency --frequency --cluster both
ls
```

<details>
  <summary>Discussion Points:</summary>

The last analysis is the most informative, showing that many of these isolates share the same complement of efflux variants, yet most isolates are unique in their resistome, with a subset sharing TEM-1, sul1, and other higher risk genes.

</details>


 
