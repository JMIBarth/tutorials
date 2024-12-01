# Substitution Model Selection (Activity 3)

## Objective

In this activity, we will select the best-fit substitution model for phylogenetic inference. After retrieving homologous sequences ([Activity 1](../dataset_compilation/README.md)) and aligning them to match homologous sites ([Activity 2](../multiple_sequence_alignment/README.md)), we will use maximum likelihood (ML) methods to evaluate and select the most suitable substitution model. This improves the accuracy of phylogenetic inferences by reflecting the evolutionary processes relevant to our dataset.
 

## Table of contents

* [3.1 Run model selection for the 12s alignment](#model_12s)
* [3.2 Inspect the model selection log file](#log_12s)
* [3.3 Inspect the model selection results file](#results_12s)
* [(Optional) Run model selection for the CO1 and RAG1 alignments](#model_co1_rag)


<a name="model_12s"></a>
## 3.1 Run model selection for the 12s alignment

 <details>
  <summary>Background: Maximum likelihood inference software (click here)</summary>  
  
--------

On the first course day you used [PAUP*](http://phylosolutions.com/paup-test/), developed by Dave Swofford in the late 1980s, one of the earliest programs for phylogenetic analysis. It has been one of the most frequently used and cited phylogenetic programs. However, as sequencing technologies have advanced, leading to larger datasets, newer and faster tools for phylogenetic inference have emerged. Some examples include: [PhyML](http://atgc.lirmm.fr/phyml) ([Guindon and Gascuel, 2003](https://doi.org/10.1080/10635150390235520)) and [RAxML](https://cme.h-its.org/exelixis/web/software/raxml/index.html) ([Stamatakis, 2014]( https://doi.org/10.1093/bioinformatics/btu033)).  

In this Activity, we will use [IQ-TREE](http://iqtree.cibiv.univie.ac.at) ([Minh et al., 2020](https://doi.org/10.1093/molbev/msaa015)), which offers optimized likelihood functions for efficient and accurate phylogenetic estimations. While all listed programs have web-server versions, only PhyML and IQ-TREE include automated substitution-model selection: "Smart Model Selection" ([Lefort et al., 2017](https://doi.org/10.1093/molbev/msx149)) and "ModelFinder" ([Kalyaanamoorthy et al., 2017](https://doi.org/10.1038/nmeth.4285)), respectively. IQ-TREE also provides a detailed and user-friendly [manual](http://www.iqtree.org/doc/). 

--------
</details>

In the lecture, we explored evolutionary models with differing base frequencies, substitution rates (e.g., transitions vs. transversions), and site-specific rate variation. These parameters generate diverse models. For example, IQ-TREE’s ModelFinder evaluates 88 sequence evolution models to identify the best fit for the dataset.

Go to the [IQ-TREE web server](http://iqtree.cibiv.univie.ac.at). This site provides a web interface for ML inference including the most frequently used features of IQ-TREE. 

 <details>
  <summary>Optional: Download and locally install IQ-TREE (click here)</summary>  
  
--------

In case the web server is busy or you plan to use IQ-TREE for your future own work, you may also download and locally install [IQ-TREE (2024: v2.3.6 )](http://www.iqtree.org/#download).

--------
</details>

On the website, click the “**Model selection**” tab. Upload the 12s alignment, which we generated in the last activity ([`12s_ncbi_ed_cut_realn_filtered.fasta`](../multiple_sequence_alignment/res/12s_ncbi_ed_cut_realn_filtered.fasta)), in [Fasta](../multiple_sequence_alignment/res/12s_ncbi_ed_cut_realn_filtered.fasta), [Phylip](../multiple_sequence_alignment/res/12s_ncbi_ed_cut_realn_filtered.phy), or [Nexus](../multiple_sequence_alignment/res/12s_ncbi_ed_cut_realn_filtered.nex) format (if needed, download the file via the provided links).  
Under “**Options**”, change “**Selection criterion**” to AIC, leave other settings as default, and click “**submit job**” (no email required).

<details>
  <summary>Optional: Command for locally installed IQ-TREE (click here)</summary>

--------

`PATH/TO/iqtree -s PATH/TO/12s_ncbi_ed_cut_realn_filtered.fasta -m MF -AIC`

--------
</details>

<kbd>![](./img/iqtree_001.png)</kbd>


<a name="log_12s"></a>
## 3.2 Inspect the model selection log file

Navigate to the “**Analysis Results**” tab to find your model selection analysis. The job should complete quickly, as indicated in the “**Summary**” sub-tab. Next to it are the “Run Log” and “Full Result” sub-tabs. Go to “**Run Log**” and inspect the content.

<details>
  <summary>Optional: Log for locally installed IQ-TREE (click here)</summary>

--------

This is equal to the `.log` file if you ran IQ-TREE on your computer.

--------
</details>

The [log](./res/12s_ncbi_ed_cut_realn_filtered.fasta.log) contains some general information about the software, followed by a list of our sequence IDs and then the 88 tested models of sequence evolution. 

<kbd>![](./img/iqtree_002.png)</kbd>

At the start of the model tests, the log output shows: “Create initial parsimony tree by phylogenetic likelihood library (PLL)... “. At the end, the best models are listed according to three criteria: 

* the Akaike Information Criterion (AIC)
* the Corrected Akaike Information Criterion (AICc – adjusted for short alignments to prevent overfitting)
* and the Bayesian Information Criterion (BIC)

ModelFinder calculates log-likelihoods for 88 models using an initial parsimony tree and applies the above listed criteria to score each model. The AIC is calculated as:
```math
AIC = 2 k -2 log(L)
```
where $k$ is the number of free parameters and $L$ is the likelihood after all free parameters have been optimized (*i.e.*, the maximum likelihood).
These criteria are very similar to likelihood-ratio tests in that they assess model fit relative to the other models, but they have the advantage that they can also be used to compare models that are not “nested” (two models are nested if one of them has all the parameters of the other models plus additional parameters).

![](../img/question_icon.png) The first model in the table, the **Jukes-Cantor (JC)** model, is the most simple substitution model as it assumes equal rates. Therefore, we would expect the degrees of freedom (df) – or the number of free parameters (k) – to be 0. However, the df are 31. What could these be?

<details>
  <summary>Answer (click here)</summary>

--------

These are the branch lengths, the substitutions that have accumulated over time between the nodes in a phylogeny. Because we have 17 species, there are 2 x 17 - 3 = 31 branches in an unrooted phylogeny.

--------
</details>

![](../img/question_icon.png) Below, the third model is given as **JC+G4**, do you know what the +G4 stands for?

<details>
  <summary>Answer (click here)</summary>

--------

This is the gamma model of rate variation with four categories of rate multipliers.

--------
</details>

![](../img/question_icon.png) The 13th model is **HKY+F**, what does the +F stands for?

<details>
  <summary>Answer (click here)</summary>

--------

The HKY model assumes different rates between transitions and transversions, the +F indicates that base frequencies are also estimated with this model.

--------
</details>

![](../img/question_icon.png) Which is the model that fits best our data? 

<details>
  <summary>Answer (click here)</summary>

--------

Best-fit model: TIM2+F+I+G4 chosen according to AIC. If it is a model that you do not know, you may look up its assumptions in the [IQ-TREE manual](http://www.iqtree.org/doc/Substitution-Models), but see also 3.3 below.


--------
</details>

A model is preferred if its AIC score is at least 4 points lower (=better) than another's. The BIC is similar to the AIC, but penalizes free parameters more strongly – that is, it shows less tolerance at higher dimensional models. However, most of the times the methods will agree on the preferred model(s) and it is always good to consider and report both, AIC and BIC.

![](../img/discussion_icon.png) Copy and paste the table of 88 models, their log-likelihoods (-lnL), degrees of freedom (df, the parameters (k) used), and the AIC and BIC scores into a spreadsheet. Sort the table by AIC and note the difference between the best three scores. Sort the table by BIC scores. How is the difference here? Do AIC and BIC agree on the best models? Compare your results with your neighbours and discuss differences.

<details>
  <summary>Discussion points (click here)</summary>

--------

* best models according to AIC 
* best models according to BIC
* stochasticity

--------
</details>


<a name="results_12s"></a>
## 3.3 Inspect the model selection results file

Switch to the “**Full Result**” tab, where models are sorted by AIC and a “weights” column is added. These weights add to 1 and indicate how much better the best model is compared to all other models. Do the weights align with your log file inspection?  

<kbd>![](./img/iqtree_003.png)</kbd>

In the [full results](./res/12s_ncbi_ed_cut_realn_filtered.fasta.iqtree), after the model scorings, the best fit substitution model is shown. According to AIC, the best model is: “TIM2+F+I+G4”, the “transition model” with variable base frequencies and transition rates, but two pairs of transversion rates that are set to be equal. For TIM, there is one rate for A to C and G to T, and another for A to T and C to G, while for TIM2 the pairing is AC=AT and CG=GT. The rate parameter (R) is set to 1.0 for “G-T” and the other parameters are calculated according to it. Because in the TIM2 model, GT equals CG, the rate C to G is also 1.0. Then, the empirical base frequencies, the rate matrix (Q matrix) for modeling the transition rates, and the four categories of rate variation are shown, followed by the parsimony tree.  
Download these results by clicking on the link “**Download selected jobs**”.


<a name="model_co1_rag"></a>
## (Optional) Run model selection for the CO1 and RAG1 alignments

Repeat the model selection under the AIC criterion for the CO1 ([`co1_bold_ed_aln_cut.fasta`](../multiple_sequence_alignment/res/co1_bold_ed_aln_cut.fasta)) and rag1 alignments ([`rag1_ncbi_ed_aln_filtered.fasta`](../multiple_sequence_alignment/res/rag1_ncbi_ed_aln_filtered.fasta)).

Download the results and compare the best fit models and the weights, as well as the rate parameters and empirically estimated base frequencies among the different alignments.
