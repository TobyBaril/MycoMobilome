# MycoMobilome

```
                            ------------
                          /  (_)_   _    \
                       /)      (_) (_)      \
                      |       _          _   |
                     | _     (_)   _    (_) _ |
                     |(_)  _      (_)  _   (_)|
                     |____(_)_________(_)_____|
                      \\\\\\\\\||||||||///////
                               |      |
                               |      |
                               |      |
                                \____/

                                     _     _ _                      
  /\/\  _   _  ___ ___   /\/\   ___ | |__ (_) | ___  _ __ ___   ___ 
 /    \| | | |/ __/ _ \ /    \ / _ \| '_ \| | |/ _ \| '_ ` _ \ / _ \
/ /\/\ \ |_| | (_| (_) / /\/\ \ (_) | |_) | | | (_) | | | | | |  __/
\/    \/\__, |\___\___/\/    \/\___/|_.__/|_|_|\___/|_| |_| |_|\___|
        |___/                                                       
```

[![DOI](https://zenodo.org/badge/1084897377.svg)](https://doi.org/10.5281/zenodo.17473060)

A non-redundant database of transposable element consensus sequences for the fungal kingdom.

To learn about the purpose and benefits of MycoMobilome, please refer to the [preprint on BioRxiv](https://www.biorxiv.org/content/10.1101/2025.10.28.685023v1).

MycoMobilome was generated from all publicly available fungal genome resources from JGI (excluding restricted assemblies) and NCBI. This is an uncurated database, but all consensus sequences have been generated using a consistent and reproducible curation process performed using [Earl Grey](https://github.com/TobyBaril/EarlGrey/), followed by several post-processing steps to refine TE consensus sequences, remove those generated from multi-copy host genes, identify TE copies with ORFs that match known TE domains, and those with homology to known TEs from across eukaryotic diversity. 

We aim to provide this _uncurated_ database as a starting point to facilitate investigations across fungal diversity. We welcome efforts to curate sequences, even a couple in your favourite lineages, or to contribute new sequences as you generate them in new genome assemblies. We aim to make this process easy, while ensuring contributions are correctly acknowledged, through the use of the [MycoMobilome Zenodo Community](https://zenodo.org/communities/mycomobilome/).

By using this database, we hope to improve consistency in naming conventions, whilst reducing the duplication of effort often accompanying repeat annotation in new genome assemblies, where researchers will often perform a _de novo_ annotation without linking new families to existing ones from other lineages. We will update the database and incorporate community contributions when appropriate, and will always release new versions with persistent DOIs and version control.

Below, we describe how to download and use the repeat database, how the database was generated, and how you can contribute to future iterations.

## How to use this database for repeat annotation

To make best use of this database, and to annotate fungal genomes using it, we suggest installing and using [Earl Grey](https://github.com/TobyBaril/EarlGrey/), which is available from Bioconda or Docker. Below, we describe the process from Bioconda. Full install instructions for other methods can be found on the main Earl Grey page.

1. Install Earl Grey

```
mamba install -n earlgrey bioconda::earlgrey
```

2. Download the MycoMobilome database and save on your system

```
mkdir -p MycoMobilome_db && cd MycoMobilome_db
curl -O "https://zenodo.org/record/17037469/files/MycoMobilome_v1.0.tar.gz"
```

3. Uncompress the database files

```
tar -zxvf MycoMobilome_v1.0.tar.gz
```

4. Make a note of which database you would like to use. We provide three different databases to fit your needs. 

Consensus sequences are annotated with three evidence categories:
 - _PE: Protein evidence supporting the classification
 - _DA: Protein evidence contradicting the classification (i.e. disagreement)
 - _NE: No protein evidence available

If you have specific hits you are interested in, we provide the ORF domain hits and known repeat hits for you to assess supporting evidence as and when required.

Three versions of the database are provided:
  - `MycoMobilome_v1.0-allConsensus_TE_library.fasta`: All known and unknown TE consensus sequences detected across fungal diversity. Most useful for most use cases.
  - `MycoMobilome_v1.0-proteinEvidence_TE_library.fasta`: All TE consensus sequences with ORF hits to known TE proteins. Note the evidence markers in sequence headers and that this subset will not contain any non-autonomous TEs (i.e. SINEs, MITEs, solo LTRs, etc).
  - `MycoMobilome_v1.0-unknown_TE_library.fasta`: All TE consensus sequences with NO protein evidence supporting their status as true TEs. These have the potential to be real given little existing knowledge of TE diversity across the kingdom. Many of these are likely non-autonomous elements, such as MITEs (non-autonomous DNA elements), solo LTRs, and SINEs, which will NOT be found in the `proteinEvidence` subset. However, some sequences are also likely to be erroneous, so use carefully.

In addition to these three database files, the following files are also provided:
  - `MycoMobilome_v1.0_assemblyRecord.xlsx`: A record of all publicly available genome assemblies used to generate MycoMobilome. Here, you will find information on assembly length, N50, L50, GC content, species phylogenetic information, genome assembly source and ID, publication, and BUSCO scores.
  - `MycoMobilome-hitsToKnownTransposonProteins-repetPfam35.txt`: A TAB-separated file showing hmmscan hits for each MycoMobilome consensus sequence open reading frame to TE domains from the [REPET Pfam 35.0 and Gypsy DB curated TE domain dataset](https://urgi.versailles.inrae.fr/download/repet/profiles/ProfilesBankForREPET_Pfam35.0_GypsyDB.hmm.tar.gz). Here, `qseqid` ends with _n, where n is the ORF number. The query sequence to match to MycoMobilome sequence headers can be found in the column named `qseqid_noFrame`.
  - `MycoMobilome-hitsToKnownTransposonProteins-rmRepeatPeps.txt`: A TAB-separated file showing BLASTp hits for each MycoMobilome consensus sequence open reading frame to TE domains from the RepeatMasker RepeatPeps.lib file supplied with RepeatMasker v4.1.9.  

5. The database can now be used for repeat annotation. Now, think carefully about how you want to proceed: (a) I want to annotate TEs already present in the database and that's it. (b) I want to annotate TEs already present in the database, then perform _de novo_ TE curation, combine these sequences with MycoMobilome, then re-annotate the genome with a new combined library.
  - A.  I want to annotate TEs already present in the database and that's it.
  - ```
    # in this case, activate the earlgrey environment, and use your desired set of MycoMobilome with the -l flag in earlGreyAnnotationOnly
    mamba activate earlgrey

    # the basic command structure is:
    earlGreyAnnotationOnly -g [input_genome_assembly.fasta] -s [name_to_use_for_outputs] -o [output_directory] -l [MycoMobilome_library.fasta] -t [threads]

    # e.g. for an assembly of zymoseptoria tritici (zymtri1.fasta) in the current directory with 16 CPU threads
    earlGreyAnnotationOnly -g zymtri1.fasta -o zymtri1 -o ./outputs/ -l MycoMobilome_v1.0-allConsensus_TE_library.fasta -t 16

    # note the potential optional parameters and refer to our thoughts on best practices for some recommendations.
    ```
  - B.  I want to annotate TEs already present in the database, then perform _de novo_ TE curation to find new things in my genome assembly, then combine these sequences with MycoMobilome and re-annotate the genome with a new combined library.
  - ```
    # activate the earlgrey environment, and use your desired set of MycoMobilome with the -l flag in earlGrey
    mamba activate earlgrey

    # the basic command structure is:
    earlGrey -g [input_genome_assembly.fasta] -s [name_to_use_for_outputs] -o [output_directory] -l [MycoMobilome_library.fasta] -t [threads]

    # e.g. for an assembly of zymoseptoria tritici (zymtri1.fasta) in the current directory with 16 CPU threads
    earlGrey -g zymtri1.fasta -s zymtri1 -o ./outputs/ -l MycoMobilome_v1.0-allConsensus_TE_library.fasta -t 16

    # note the potential optional parameters and refer to our thoughts on best practices for some recommendations.
    ```

6. Enjoy the summary information given by Earl Grey, which can be found in the `[species]_summaryFiles` directory in your Earl Grey run.

7. If you have curated some sequences, or even _de novo_ detected some new ones, please consider uploading these to the [MycoMobilome Community](https://zenodo.org/communities/mycomobilome/). We will review these and generate a new non-redundant TE library incorporating submissions. You will also get a DOI for your submission, which you can use to publish data notes or allow researchers to use and correctly reference your contributions!

## How this database was generated

The MycoMobilome database was generated using a standardised _de novo_ TE curation approach among all publicly available fungal genome resources (n=4,309 genomes). A table containing information on all assemblies used to generate version 1.0 of this database is provided within `MycoMobilome_v1.0` in the file `MycoMobilome_v1.0_assemblyRecord.xlsx`.

Each genome was used to generate putative TE consensus sequences using `earlGreyLibConstruct` in Earl Grey (v4.4.0)[^fn1], configured with Dfam curated elements (v3.7)[^fn2], using default settings. All putative consensus sequences were combined into a single FASTA file containing 773,843 entries. A non-redundant TE library was constructed using a scalable cascaded clustering approach using MMseqs2[^fn3] `easy-cluster` with `--min-seq-id 0.8 -c 0.8 --cov-mode 1 --cluster-reassign`, resulting in 354,315 non-redundant sequences. Representative sequences for each cluster were extracted and labelled with the species name from which the representative originated.

Open reading frames (ORFs) were detected in all six frames of each consensus sequence using `transeq` in EMBOSS (v6.6.0)[^fn4] with `-clean -frame 6`. Matches to known host proteins were identified using the Fungi RefSeq[^fn5] database (Release 228) and Diamond BLASTp[^fn6] with `--sensitive --matrix BLOSUM62 --evalue 1e-3`. Potential hits were combined for each query sequence. Sequences with hits to RefSeq, and either no hits to known TE protein domains, or partial hits to known TE protein domains that do not overlap with RefSeq hit coordinates, were labelled as potential host genes and removed from the MycoMobilome dataset. Any hits to proteins labelled as `uncharacterized|hypothetical|low quality|predicted protein` were kept due to the potential to be TE-derived.

Matches to known TE proteins were identified using two complementary approaches: (i) Using HMMscan in HMMER (v3.4)[^fn7] to detect homology to known TE protein domains curated by the [REPET group](https://urgi.versailles.inrae.fr/download/repet/profiles/ProfilesBankForREPET_Pfam35.0_GypsyDB.hmm.tar.gz). Matches were identified using `hmmscan -E 10 --noali`. Hits were filtered to retain those where `fseq_evalue <=0.001` and `fseq_bitscore >= 50`. Hits were retained as potential TEs unless the query also matched RefSeq proteins, in which case they were removed to avoid including host genes or chimeric TE–host gene models.

(ii) Using BLASTp to detect homology to known TE protein domains supplied with RepeatMasker (v4.1.5) `RepeatPeps.lib`.(repeatmasker.org). Matches were identified using `blastp -evalue 1e-3`. Nested hits were removed to retain the highest quality protein hit for each query, followed by combining adjacent and overlapping hits. Hits were retained as potential TEs unless non-overlapping hits to the same query were also found in the RefSeq hits set, in which case these were removed due to the potential that these hits could be host genes, or chimeric TE-host gene models. 

A total of 24,571 consensus sequences were identified as putative host genes and removed from the database, resulting in a potential TE consensus set containing 329,744 sequences. This set was further filtered to remove all putative TE consensus sequences <120bp in length, as these are likely to be poor quality and incomplete. In addition, the base composition of each consensus was calculated using `seqtk comp` (https://github.com/lh3/seqtk) and all sequences with an N content >=5% were removed due to being poor quality, reducing the final MycoMobilome library to 276,641 sequences.

For each consensus sequence, if there are hits to known TE protein domains, the sequences were labelled as "supported". Following this, the identity of each protein domain hit was evaluated to determine whether the consensus sequence classification is supported by protein hits from the REPET profiles bank or RepeatMasker RepeatPeps. If the identified domains support the consensus classification, the consensus sequence is labelled with `_PE` for `protein evidence`. If the identified domains conflict with the consensus classification, the consensus sequence is labelled with `_DA` for `disagreement`. If there are no identified domains, the consensus sequence is labelled with `_NE` for `no evidence`. The appropriate domains for each classification are defined in the table below:

 |High level TE classification | Appropriate Domain Hits from REPET                                                     | RepeatMasker RepeatPeps  |
 |-----------------------------|----------------------------------------------------------------------------------------|--------------------------|
 | DNA                         | Tase,Tase*,DDE,HTH,[ATP,INT,AP for crypton,maverick]                                   | DNA                      |
 | RC                          | HEL,EN,RPA                                                                             | RC                       |
 | LTR                         | RT,INT,RH,GAG,AP,VirusRelated,LTRrelated,Caulimovirus,ClassIrelated,ENV                | LTR                      |
 | LINE                        | RT,EN,RH,GAG,ClassIrelated,LINErelated                                                 | LINE                     |
 | PLE                         | RT,EN,ClassIrelated                                                                    | PLE                      |
 | Retroposon                  | RT,INT,RH,GAG,AP,VirusRelated,LTRrelated,Caulimovirus,ClassIrelated,ENV,EN,LINErelated | Retroposon               |

Sequences are named with the convention `MycMob1.0_family-[n]-[six digit species code]_[protein evidence]#[high level classification]/[sub level classification] @[genus species]`. Protein hits to known TE proteins are provided with MycoMobilome to support further investigation in specific use cases. No changes were made to classifications assigned during automated curation, therefore this database should be treated as _uncurated_ and caution should be used to check important or interesting TE loci on a case-by-case basis. Please note that _all_ nonautonomous elements will have the label `_NE` as they do not contain any intact protein domains. This does not mean they are not real TEs. As such, for most use cases we suggest using the complete MycoMobilome v1.0 dataset, unless you are specifically interested in autonomous TEs only. 

## Some thoughts on best practices

TE annotation is compute-intensive, and runtime will be proportional to the size and repeat complexity of the genome of interest. We recommend making use of high-performance computing facilities if you have access to these. Access to large amounts of RAM is also very important!

There are several options available when annotating with Earl Grey. From our experience across ~6,000 genomes spanning eukaryotic diversity, we have a few recommendations that might help in your studies. 

 - We suggest starting with a test dataset to ensure the pipeline is correctly configured, although most users should not have issues with the conda package.
 - If you are going to be running Earl Grey with the _de novo_ detection, you must configure with the required partitions of Dfam. Without these, it is not possible to automatically classify putative TEs. Performance will also depend on which partitions are installed, so check carefully and install all those that might be useful to you. If you are looking at `MycoMobilome`, the most useful partition is likely to be that which contains Fungi (although very few of the sequences in this are curated!).
 - If you plan to pre-mask with known MycoMobilome repeats and then de novo detect new ones, consider clustering your libraries using `-c yes`. This ensures your genome assembly is annotated with a non-redundant library generated from both MycoMobilome and your new repeats.
 - Very short TE annotations are likely to be spurious or low-quality. If you want to remove these, you can use `-m yes` in Earl Grey to remove all TE annotations <100bp in length.
 - Are you planning to do gene annotation next? You can use `-d yes` to get Earl Grey to generate a soft-masked version of the input genome assembly for use in other tools!
 - Segmental duplications can be detected and curated as putative TEs. If you have instances of this and it is reducing TE library quality, you may want to consider increasing the minimum number of sequences required to build a consensus by using `-a [min number]`. The default is currently 3 sequences.
 - Helitrons are incredibly challenging to detect using homology-based approaches. Earl Grey includes the HELIANO[^fn8] tool to detect these using structural approaches. If you would like to use HELIANO detection, you can activate this using `-e yes` in your Earl Grey command.

## How you can contribute

MycoMobilome is intended to be a community resource which the Fungi and further genomics communities can help to maintain and grow. If you decide to curate some sequences in your species of interest, or have _de novo_ sequences from new genome assemblies that you would like to contribute, you can do so through the [MycoMobilome Community](https://zenodo.org/communities/mycomobilome/). This will also enable you to assign a DOI to your submitted libraries, so that you and others can cite your contributions. Following submission, we will incorporate new sequences into new versions of MycoMobilome, with extra labels indicating manually-curated sequences.

We also welcome contact from individuals or groups who would like to help with the management of MycoMobilome. Please reach out to us if you would like to contribute or support our efforts in any way, big or small. Also, please share MycoMobilome if it has been useful for you. We can only grow with the help of the community!

To contribute new sequences, please follow these steps:

1. If you have _de novo_ detected TEs after pre-masking with MycoMobilome, please provide the `.strained` TE library file (found in the summary files of Earl Grey) and the cluster table (found in the strainer directory of Earl Grey).

2. In all cases, please create an upload on [Zenodo](zenodo.org) by logging in, and selecting "new upload". 

3. Assign a DOI to your upload following the Zenodo guidance. 

4. Select "Dataset" as the resource type and give your upload a meaningful title. For example "_De novo curated TE sequence library for [genus species]_".

5. In the description, please provide a description of the data, along with how it was generated. Include all software used, versions, and any relevant parameters to maximise reproducibility.

6. Add all creators, and consider using ORCID numbers for tracking contributions.

7. Be sure to upload your dataset to the MycoMobilome community. On the upload page, click the blue ‘Select a community’ button at the top and choose ‘MycoMobilome’.

8. After submission, we will be notified and will note to incorporate new submissions with updates to the database.

## What is in MycoMobilome v1.0?

A quantification of consensus sequences in MycoMobilome v1.0, split by high-level classification, protein support, and number of consensus sequences can be found in the [summary table](./MycoMobilome_v1.0_consensuslist.md).

## Citations

[^fn1]: Baril T, Galbraith J, Hayward A. Earl Grey: a fully automated user-friendly transposable element annotation and analysis pipeline. Molecular Biology and Evolution. 2024 Apr;41(4):msae068.

[^fn2]: Hubley R, Finn RD, Clements J, Eddy SR, Jones TA, Bao W, Smit AF, Wheeler TJ. The Dfam database of repetitive DNA families. Nucleic acids research. 2016 Jan 4;44(D1):D81-9.

[^fn3]: Steinegger M, Söding J. MMseqs2 enables sensitive protein sequence searching for the analysis of massive data sets. Nature biotechnology. 2017 Nov;35(11):1026-8.

[^fn4]: Rice P, Longden I, Bleasby A. EMBOSS: the European molecular biology open software suite. Trends in genetics. 2000 Jun 1;16(6):276-7.

[^fn5]: Goldfarb T, Kodali VK, Pujar S, Brover V, Robbertse B, Farrell CM, Oh DH, Astashyn A, Ermolaeva O, Haddad D, Hlavina W. NCBI RefSeq: reference sequence standards through 25 years of curation and annotation. Nucleic Acids Research. 2025 Jan 6;53(D1):D243-57.

[^fn6]: Buchfink B, Reuter K, Drost HG. Sensitive protein alignments at tree-of-life scale using DIAMOND. Nature methods. 2021 Apr;18(4):366-8.

[^fn7]: Sonnhammer EL, Eddy SR, Birney E, Bateman A, Durbin R. Pfam: multiple sequence alignments and HMM-profiles of protein domains. Nucleic acids research. 1998 Jan 1;26(1):320-2.

[^fn8]: Li Z, Gilbert C, Peng H, Pollet N. Discovery of numerous novel Helitron-like elements in eukaryote genomes using HELIANO. Nucleic Acids Research. 2024 Sep 23;52(17):e79-.
