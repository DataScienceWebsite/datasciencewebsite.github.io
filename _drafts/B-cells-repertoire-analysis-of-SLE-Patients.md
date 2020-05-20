---
title: "B cells repertoire analysis of SLE Patients"
collection: portfolio
---

## Abstract
Systemic lupus erythematosus (SLE) is an autoimmune disease in which the B lymphocytes produce pathogenic auto-antibodies targeting healthy tissues. The exact causes of the diseases are unknown but involves a combination of genetic and environmental factors. In this project I used the AntibodyMap database to extract the heavy chains of healthy subject and SLE Patients. My interest was particularly to compare the IgM heavy chains repertoire between healthy control and SLE patients. I used the Immcantation pipeline developed by Steven Kleinstein's team and my owns custom scripts to compare the B cell repertoire of SLE patient to healthy controls. 

## Method
The code for this analysis is available on this [repository](https://github.com/wguesdon/Tipton_2015_analysis).

The Heavy Chains sequences were isolated from the AntibodyMap website. The heavy chains sequences were sequenced on the MiSeq (Illumina) platform.
and were published by Tipton CM and collaborator in Nature Immunology.

Once obtained the Heavy Chains sequences were submitted to IMGT High Vquest for annotation. 
The annotated sequences were then processed using the Alakazam package and ChangeO scripts in order to remove non-functional sequences and identify B cells clones. After manual inspection an Hamming distance of 0.1 was used as the clonal threshold. 
The isotypes of the heavy chains was added through a custom script and the repertoire properties was analysed using the Alakazam package. 

The database filtering was realized with the Dplyr package and data visualization was performed using ggplot2.

## Results
The principals findings are available in the power point presentation [here](https://wguesdon.github.io/files/SLE_repertoire_analysis.pptx).

## Conclusion
This analysis highlight differences between healthy control and SLE patients B cells repertoire clonal diversity, V-gene usage and  physicochemical property properties.

The most significant difference are:

* a decreased clonal diversity of SLE patients heavy chains.
* an increased proportion of IGHV4, and particularly VH4-34 in the IgM chains of SLE patients.  
* a decreased somatic hypermutation frequencies in heavy chains of SLE patients.