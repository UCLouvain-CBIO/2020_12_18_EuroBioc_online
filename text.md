

## Single-cell technology

As you may already know, single-cell omics are a set of techniques 
that generates measurements for one cell. This allows you to model in
more details confounding effects such as the presence of different 
cell types, as illustrated on the left, or the presence of continuous
effects as often observed in developmental processes. The scRNA-Seq is
probably the most popular single-cell technology and it has matured 
very quickly over the last few years. Several standardized analysis 
pipelines came and I highly recommend the Bioconductor book about 
orchestrating single-cell analyses. However, when you are interested 
in cellular function, you would rather look at proteins rather than 
RNA. You might think that RNA is a good proxy for protein though. Yes
and no. 

## Single cell proteomics

If you look at a global scale, so across tissues across genes, you can
say there is a good correlation between RNA and protein. See here each 
dot represent the expression of a gene in different tissues at RNA 
and protein level. However, if you focus on a particular gene across 
tissues, you can see in some cases that proteinre not explained by 
RNA. So looking at proteins and RNA gives complementary but **different** 
information. 

Popular tools for acquiring single-cell proteomics data are flow 
cytometry or CITE-Seq. They are excellent tools as they provide high
throughput and high sensivity. However, they have the drawback that 
they rely on antibodies. So, the acquisition can only detect the 
targets of the antibody you have, and antibodies can be subjected to 
specificity issues. Therefore, we might be interest in getting 
shotgun proteomics as for scRNA-Seq, but is this possible when
proteins cannot be amplified by PCR? 

## MS-based single-cell proteomics

Well it is. See here, this graph shows the copy numbers of RNA, 
proteins and peptide (which are fragments of proteins). RNA are 
present at low copy number and therefore it is essential to amplify 
those copies by PCR in order to measure them. On the other hand, 
proteins are between 1 and 2 order of magnitude more abundant that RNA.
First, this decreases the sampling error. But also, this means we 
could measure those proteins by mass spectrometry. 

## MS-based single-cell proteomics

This challenge was tackled by several groups and several amazing new
development have emerged in the last three years. Recently, the Slavov
lab has reached a milestone by sequencing around 1500 cells with 3000 
quantified proteins and an average 600 proteins per cell. This is how 
the mass spectrometry pipeline works. The proteins from isolated cells 
are first extracted and digested into peptides. The peptides are then 
separated by liquid chromatography based on their chemical properties. 
The separated peptides are then analysed by a mass spectrometer that 
generates mass fingerprints, called spectra. The spectra are matched
against a database to retrieve the peptide sequence. Identified 
spectra are then called peptide to spectrum match or PSM and can be 
quantified based on the signal intensity. I should tell you here that
due to the current technical limitations at most 10 single-cells can 
be acquired per run. We'll see the implication of this later.

## Our contribution

So, mass spectrometry-based single-cell proteomics is an emerging field 
that requires new computational developments. And we want to tackle 
the lack of good computational tools for handling SCP data. Our 
contribution lies in three parts: 

## Data framework 

Let's first talk about the data framework. So we are interest in 
single-cell proteomics, so we need tools for single-cell and 
proteomics. For the single-cell part it seems obvious to use the 
Single-cell experiment package. It provides a compact data 
representation where quantitative data can be retrieved along with 
gene and sample annotations as well as single-cell specific features. 

On the proteomics side, the Laurent recently has released the 
QFeatures class that is an extension of the MultiAssayExperiment 
class. Remember that the acquisition started from proteins that were
digested in peptides that were measured as PSMs. Well, the idea is to 
go the other way around and computationally reconstruct the protein 
information from the PSM data. The different levels of information are 
stored in separate objects and the relationships between the rows are 
preserved. See here, several PSMs can be matched to a single peptide 
and several peptides can be part of the same protein. 

If we combine the 2 classes, we get our SCP framework. 

## Data framework 

So the SCP framework is a QFeatures object that contains 
SingleCellExperiment objects. The PSM level contains several assays as
all single-cell cannot be acquired in a single, so we keep them 
separated and integrate them into the peptide data. So again how the 
relationships between features are preserved. Ok this is nice 
conceptually, but can you see it in action? Let's get some example. 

## scpdata

You can find published single-cell proteomics dataset in the scpdata 
package that currently contains 11 datasets accessible throgh EH. I 
want to give a little shout out to the ExperimentHub project as it 
makes disseminating data so easy. Look at this, if you want the SCoPE2 
dataset with the 1500 cells you just need to load the library and call
the specht2019v3 function. ExperimentHub will take care of caching the
data for you. 

When we look at the overview of the SCoPE2 dataset we can see that it 
is indeed a QFeatures object with mutliple assays. The 177 first are
the PSM data in different batches and the last and before last are 
the peptide and protein data. See that all assays are SCE. Now we have
the PSM data, we can process it to reconstruct the peptide and protein
data. 

## scp

On the left I show you the main steps that were used to process the 
SCoPE2 data. The implementation was hard to read and therefore cannot
be applied to other data. On the right, we reproduce the analysis, but
using our tools. Most functions are implemented in QFeatures and MAE, 
but scp is an extension to those packages and provides scp specific 
functionalities such as reading in data or computing single-cell 
metrics. I hope you will agree when I say this code is easy to read 
and you don't need to be an expert in R to get a general idea of what 
the code does. Furthermore, tweaking steps and methods becomes very 
easy as you just have to change the order of operations or change 
method arguments to adapt the pipeline. 

## 

So we applied our tools to reproduce the SCoPE2 analysis and here are 
the results. 























