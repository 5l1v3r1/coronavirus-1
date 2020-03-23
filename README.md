# We believe biotech can do better!
# Covid-19 treatment should be safe, effective, and efficient
# Algorithms + Data to Attack Covid-19 Genome with CRISPR

# TLDR: Delete RNA in Virus which isn't in Human

## NOTE: Please help find a lab to test this! Forward this anyone who may have a capable lab
## Contact: Bion Howard - bion@bitpharma.com - +1 843 830 2918

![CRISPR-Cas13 is an RNA-guided RNA interference system](https://github.com/bionicles/coronavirus/blob/master/diagrams/cas13.jpeg)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

- STATUS: Minimum Viable Product - Work in Progress - Ready for Peer Review
- NEXT: Design Plasmids with Promoters ([PDPN](https://epd.epfl.ch//cgi-bin/get_doc?db=hgEpdNew&format=genome&entry=PDPN_1), Sp1, Sp3, TTF-1, HNF-3α)

## Usage
```
conda create --name bio python=3.8
pip install biopython redis
conda activate bio
sudo docker run -p 6379:6379 -d redis redis-server --appendonly yes

python design-guides.py
```

## Folders
- Blacklist: Target sequences (SARS|HKU1|MERS|nCoV)
- Whitelist: Host / off-target sequences
- Guides: predicted gRNA
- Alignments: Clustal Multiple Sequence Alignments
- Parts: Sequences to include in plasmids

## Problem: Covid-19 Outbreak

- There is active silent spread of SARS-Cov-2 causing Covid-19 which is 200-400x more lethal than flu. Over 100,000 cases, with new cases outstripping recoveries, globally distributed community-acquired ("cat out of the bag") 
- Silent spread of a disease increases the probability of further increases in virulence / lethality because more hosts enable more replication events and each replication event can yield mutations which cause increased virulence / lethality

## Challenge

- How do you delete Coronavirus? 

## Opportunity

- delete coronavirus genome conserved sequences with CRISPR-Cas13 RNA-guided RNA-knockdown
- deliver with inhalers of nanoparticles / adenovirus (non-replicating)
- express therapy only in type 1 pneumocytes with podoplanin promoter

![Two different plasmid design options](https://github.com/bionicles/coronavirus/blob/master/diagrams/crispr-plasmid-options.png)

## Prepare

- install clustal omega 
- install redis-cli and docker 
- run redis for the whitelist `docker run -p 6379:6379 -d redis redis-server --appendonly yes`
- download sequences of SARS, MERS, HKU1, and as many SARS-nCoV-2 genomes as possible from ncbi (.fa or .fasta format)

## Act

- combine the SARS-nCoV-2 files with `cat $(ls -t) > combined.fasta`
- generate a SARS-nCoV-2 consensus using EMBL EMBOSS CONS https://www.ebi.ac.uk/Tools/msa/emboss_cons/ between as many SARS-nCoV-2 sequences as can be found at https://www.ncbi.nlm.nih.gov/labs/virus/vssi/#/
- copy the consensus sequence into a new file
- combine the SARS-nCoV-2 files with `cat $(ls -t) > combined.fasta`
- align the prior outbreak sequences with the new consensus with `clustal -i combined.fasta -o combined.clu -outfmt=clu`
- open the alignment and use CTRL-F and type `************` until you can't find any matches, then back up and copy this conserved substring from the SARS-nCoV-2 sequence into some file (i did this already) ... gradually delete stars to get more conserved substrings
- run `python design_guides.py` with python (I'm using 3.8)

## Reflect

- fancy stuff didn't work (adding the whole transcriptome to the whitelist was WAY too slow on my laptop...but it's embarassingly parallelizable problem to divide this text file and make kmers)
- download human transcriptome with this link: ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/001/405/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_rna.fna.gz found on page: https://www.ncbi.nlm.nih.gov/genome/?term=homo+sapiens
- tried a wobble powerset algorithm (see comments at bottom of design_guides.py) but this caused a combinatorial explosion because there are an insane number of possible 30mers. Could use a MAX ENTROPY heuristic for this algo to just choose the nucleotides which accomodate most wobble
- there are probably some gotchas in the gRNA design which haven't been accounted
- since I couldn't whitelist the whole transcriptome, the lung microRNA and long non-coding RNA aren't in the whitelist, which presents an opportunity for off target effects
- likewise, I'm not using Levenshtein distance for whitelist cache hits, only exact matches, and there weren't any. That might be a false negative because guides could tolerate some mispairing, and wobble pairing, but I wanted to produce results and then improve it later

## Troubleshoot

- redis.exceptions.ConnectionError: Error 111 connecting to localhost:6379. Connection refused.
    - check Redis is running

## References

- BLASTN Suite
- NCBI Nucleotide
- NCBI Virus
- EMBL EMBOSS CONS Consensus
- Clustal Omega
- UCSC Genome Browser
- Freije CA, et al. Programmable inhibition and detection of RNA viruses using Cas13. Mol Cell. 2019. https://doi.org/10.1016/j.molcel.2019.09.013.
- Tips and Tricks for Cas13 https://zlab.bio/cas13
- pC0046-EF1a-PspCas13b-NES-HIV https://www.addgene.org/103862/
- Grabherr MG, Haas BJ, Yassour M, Levin JZ, Thompson DA, Amit I, Adiconis X, Fan L, Raychowdhury R, Zeng Q, Chen Z, Mauceli E, Hacohen N, Gnirke A, Rhind N, di Palma F, Birren BW, Nusbaum C, Lindblad-Toh K, Friedman N, Regev A. Full-length transcriptome assembly from RNA-seq data without a reference genome. Nat Biotechnol. 2011 May 15;29(7):644-52. doi: 10.1038/nbt.1883. PubMed PMID: 21572440.
- Cock PA, Antao T, Chang JT, Chapman BA, Cox CJ, Dalke A, Friedberg I, Hamelryck T, Kauff F, Wilczynski B and de Hoon MJL (2009) Biopython: freely available Python tools for computational molecular biology and bioinformatics. Bioinformatics, 25, 1422-1423
- Glasser SW, Burhans MS, Eszterhas SK, Bruno MD, Korfhagen TR. Human SP-C gene sequences that confer lung epithelium-specific expression in transgenic mice. Am J Physiol Lung Cell Mol Physiol. 2000;278(5):L933–L945. doi:10.1152/ajplung.2000.278.5.L933
- Vanderbilt JN, Gonzalez RF, Allen L, et al. High-efficiency type II cell-enhanced green fluorescent protein expression facilitates cellular identification, tracking, and isolation. Am J Respir Cell Mol Biol. 2015;53(1):14–21. doi:10.1165/rcmb.2014-0348MA "
- Flodby, P., Borok, Z., Banfalvi, A., Zhou, B., Gao, D., Minoo, P., ... & Crandall, E. D. (2010). Directed expression of Cre in alveolar epithelial type 1 cells. American journal of respiratory cell and molecular biology, 43(2), 173-178.
- Redis.io
- APE (A Plasmid Editor)
- igem.org
- LibreOffice Draw
- Ubuntu 18.04
- Eukaryotic Promoter Database - https://epd.epfl.ch
- David B. T. Cox, Jonathan S. Gootenberg, Omar O. Abudayyeh, Brian Franklin, Max J. Kellner, Julia Joung, Feng Zhang.RNA editing with CRISPR-Cas13. Science, 24 Nov 2017 : 1019-1027 
- Ferry, Q., Lyutova, R. & Fulga, T. Rational design of inducible CRISPR guide RNAs for de novo assembly of transcriptional programs. Nat Commun 8, 14633 (2017). https://doi.org/10.1038/ncomms14633
- https://www.salk.edu/wp-content/uploads/2018/05/CasRx-marketing-summary-S2017-0221.pdf
- What is the reason for using U6 promoter for shRNA vectors? can we replace it with cmv promoter ( for shRNA without flanking by mir30)? https://www.researchgate.net/post/What_is_the_reason_for_using_U6_promoter_for_shRNA_vectors_can_we_replace_it_with_cmv_promoter_for_shRNA_without_flanking_by_mir30
