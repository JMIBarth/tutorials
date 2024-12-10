# Bayesian Phylogenetic Inference (Activity 6)

## Objective

Previously, we covered retrieving homologous sequences ([Activity 1](../dataset_compilation/README.md)), aligning them ([Activity 2](../multiple_sequence_alignment/README.md)), selecting the best-fit substitution model ([Activity 3](../substitution_model_selection/README.md)), and using maximum likelihood (ML) for phylogenetic inference ([Activity 4](../maximum_likelihood_phylogenetic_inf/README.md)). This tutorial focuses on using Bayesian inference using the software BEAST 2 to infer a time-calibrated phylogeny.


## Table of contents

* [Background](#software)
* [Requirements](#software)
* [6.1 Test the effect of different clock models (strict vs. relaxed clock)](#clock_models)
	* [6.1.1 Infer the Charadriidae phylogeny using the strict clock model](#co1_strict)
		* [6.1.1.1 Generate a XML configuration file in BEAUti](#beauti)
		* [6.1.1.2 Inspect the XML configuration file](#xml)
		* [6.1.1.3 Run the MCMC analysis using BEAST 2](#beast)
		* [6.1.1.4 Visually inspect the posterior estimates using Tracer](#tracer)
	* [6.1.2 Infer the Charadriidae phylogeny using the relaxed clock model](#co1_relaxed)
	* [6.1.3 Compare the results of the strict and relaxed clock analyses](#strict_vs_relaxed)
		* [6.1.3.1 Compare the log files using Tracer](#compare_tracer)
		* [6.1.3.2 Compare the tree files using TreeAnnotator and FigTree](#compare_trees)
* [Optional: Sample from the prior distribution and ignore the data](#nodata)
* [6.2 Estimate the time of arrival of the endemic New Zealand shorebirds](#dispersal_dating)

<a name="background"></a>
## Background

<details>
 <summary>Background Bayesian inference (click here)</summary>

--------
**Background**: In contrast to maximum-likelihood inference, Bayesian inference can take into account prior expectations. In the context of phylogenetic inference, this means for example that base frequencies can be constrained towards values that are considered realistic. In addition, prior probabilities can be used to constrain divergence times in phylogenetic divergence-time estimation. In combination with an assumption about the molecular clock, these can then inform about the overall timeline of diversification.  

--------
</details>

<details>
 <summary>Background about the software and data (click here)</summary>

-------- 
**Software**: Here we use the Bayesian software package BEAST2 ([Bouckaert et al. 2019](https://doi.org/10.1371/journal.pcbi.1006650)) to infer a time-calibrated phylogeny. The package contains several programs:  
- BEAUti: GUI for generating XML configuration files.  
- BEAST 2: Runs the Bayesian analysis.  
- TreeAnnotator: Summarizes trees.  
- Tracer ([Rambaut et al. 2018](https://doi.org/10.1093/sysbio/syy032)) Assesses stationarity of the Bayesian analysis.

**Data**: The same genes, sequences, and species from previous analyses will be used. BEAST 2 requires alignments to include identical species with consistent naming (previously, some species were missing in the 12s alignment, and names varied by accession numbers). These adjustments were made to the alignments: [`12s.nex`](data/12s.nex), [`co1.nex`](data/co1.nex), [`rag1.nex`](data/rag1.nex). 

--------
</details>
  
<a name="software"></a>
## Requirements

The software packages used in this activity must be installed locally (if you haven't done so previously), as no online tools are available. 

* **BEAST 2** ([Bouckaert et al. 2019](https://doi.org/10.1371/journal.pcbi.1006650)). Download the current BEAST 2 package (2024: v2.7.7.) according to your platform from the [BEAST 2 website](http://www.beast2.org/). The BEAST 2 package includes BEAUti, BEAST 2 itself, TreeAnnotator, and other tools. Since all the programs are written in Java, no compilation is needed. 

	After downloading and extracting the BEAST 2 package, test the programs as follows:  
	- **BEAST**: Test if BEAST can be opened. If not, make sure you have Java installed and updated to the required version.
	- **BEAUti**: Open the BEAUti program from the BEAST 2 folder. A dialog "New Packages are available to install" will appear. Click **Yes** to update any available packages.

	<kbd>![](./img/beauti_015.png)</kbd>
	
	- **TreeAnnotator**:Similarly, open TreeAnnotator and check if it launches correctly. 

* **Tracer** ([Rambaut et al. 2018](https://doi.org/10.1093/sysbio/syy032)). Download [Tracer](https://github.com/beast-dev/tracer/releases) (2024: v.1.7.2) according to your platform ([Mac OS X](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.dmg), [Linux](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer_v1.7.2.tgz), or [Windows](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.zip)) Just like BEAST 2, Tracer is written in Java and should work on any system.

* **FigTree** To visualize and edit the tree, we will again use the program [FigTree](https://github.com/rambaut/figtree/releases/tag/v1.4.4). You should already have this software installed from the Maximum Likelihood tutorial. If not, download FigTree for [Mac OS X](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree.v1.4.4.dmg), [Linux](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree_v1.4.4.tgz), or [Windows](https://github.com/rambaut/figtree/releases/download/v1.4.4/FigTree.v1.4.4.zip).



<a name="clock_models"></a>
## 6.1 Test the effect of different clock models (strict vs. relaxed clock)

A strict clock model assumes that every branch in a phylogenetic tree evolves according to the same evolutionary rate, while the uncorrelated relaxed clock allows each branch to have its own evolutionary rate.

<a name="co1_strict"></a>
### 6.1.1 Infer the Charadriidae phylogeny using the strict clock model

<a name="beauti"></a>
#### 6.1.1.1 Generate a XML configuration file in BEAUti

Download the file [`co1.nex`](data/co1.nex). Open BEAUti (“Bayesian Evolutionary Analysis Utility”), click “**Import Alignment**” in BEAUti’s “**File**” menu, and select the downloaded co1 alignment.

<kbd>![](./img/beauti_001.png)</kbd>

The BEAUti interface has six tabs: “Partitions”, “Tip Dates”, “Site Model”, “Clock Model”, “Priors”, and “MCMC”. The first of these (“**Partitions**”) is currently selected. Skip the “Tip Dates” tab, and proceed to the “**Site Model**” tab.

<details>
 <summary>But, what are "Tip Dates" for? (click here)</summary>

--------
The Tip Dates tab allows specifying sample collection times for time-calibrating viral phylogenies. However, for our data, the small sampling time differences (a few years) are negligible compared to the divergence times of the sequences.

--------
</details> 

In the “**Site Model**” tab you can select substitution and state frequency models, such as the Jukes-Cantor model, the HKY model or the GTR model. The default **Jukes-Cantor (JC69)** model is selected and sufficient for now. 

<kbd>![](./img/beauti_002.png)</kbd>

Proceed to the “**Clock Model**” tab. The default “**Strict Clock**” model is selected, which we’ll use for now. At the right of this window, the checkbox for “**estimate**” is inactive because no age constraints have been set. Without age constraints, the analysis relies on a fixed rate for the molecular clock. However, once age constraints are specified, the checkbox will activate automatically.

<kbd>![](./img/beauti_003.png)</kbd>

Go to the “**Priors**” tab. Here, prior probability distributions for all model parameters can be specified. With the simple Jukes-Cantor model selected, only a few parameters are listed: Two rows, one for the diversification model (**Tree.t**) where currently the “Yule Model” (the Yule process) is selected, and another for the prior probability for the only parameter of this model, the “**birthRate**” parameter (*i.e.*, the speciation rate).

Click on the drop-down menu that currently says “**Yule Model**”. Select the “**Birth Death Model**” to assume speciation as well as extinction. An additional row has been added for the “**BDBirthRate**” and the **“BDDeathRate**”, both rates are estimated during the analysis. 

<details>
 <summary>Background: BDDeathRate and BDBirthRate and their prior constraints (click here)</summary>

--------

In BEAST 2, the BDBirthRate and the BDDeathRate do not correspond to speciation ( $λ$ ) and extinction ( $μ$ ) rates directly. Instead, the “BDBirthRate” is the so-called “net diversification rate” which is the difference between the speciation and the extinction rate ( $λ-μ$ ). The “BDDeathRate” is the “relative extinction rate, the ratio between extinction and speciation ( $μ/λ$ ).

Click on the **triangle** next to "**BDDeathRate**" to see the prior constrains. The relative extinction rate is typically constrained to lie between 0 and 1. If it was greater than 1, the extinction rate would be assumed greater than the speciation rate, having clades go extinct rather than diversify.

Also click on the **triangle** next to "**BDBirthRate**". This rate has an uniform probability distribution with a lower limit of 0 and an unrealistically high upper limit of 10,000. The unit for this is “net diversification per branch per million years” meaning that the upper limit could only be reached if we had a phylogeny in which several ten thousands of species would have evolved each million years. Clearly this is not the case. Nevertheless, for now we leave the uninformative prior probability as it is. 

--------
</details> 

Click on the “**+ Add Prior**” button below the two rows. This will allow the specification of clades (called “taxon sets” by BEAST 2) that can then be constrained to be monophyletic and/or to be of a certain age (MCRA prior, which stands for “most recent common ancestor”). Select the “**MCRA prior**”. 

<kbd>![](./img/beauti_004.png)</kbd>

A pop-up window will appear, allowing you to specify a label (name) for the clade and select species. In the top field, specify “**all**” as the “**Taxon set label**”. Leave the “Filter” field empty. In the field at the bottom left of the pop-up window, **select all the species** (command-A), then click the “**>>**” button to move them into the ingroup of the clade, the window to the right. Click “**OK**”. We have now defined a clade that contains all species. This step allows us to constrain the age of the phylogeny root. 

<kbd>![](./img/beauti_005.png)</kbd>

To constrain the root age of the phylogeny, click the  **[none] drop-down menu** in the fourth row, to the right of the “all.prior” button. From the list of distribution types, select “**Log Normal**” to apply a log-normal prior probability distribution for the root age.

<kbd>![](./img/beauti_006_2.png)</kbd>

To specify the log-normal distribution parameters for the prior probability, click the **black triangle** to the left of the "all.prior" button. This will reveal three parameter fields for **M** (mean), **S** (Sigma, standard deviation), and **Offset** (defines the minimum of the distribution, usually 0). Set the following values:

- M (mean): 50.0. 
- S (standard deviation): 0.01. 
- Offset: 0. 

This places a narrow constraint on the root age, assuming diversification began 50 +/- 1 million years ago. This is of course overly confident, and we will perform a more realistic time calibration later. 

Tick the box for "**Mean in real space**" The distribution should now center around 50, with a 2.5% quantile at 49 and a 97.5% quantile at 51. If the distribution isn’t visible, re-enter the standard deviation as 0.01. Click the black triangle again to close the window. 

<kbd>![](./img/beauti_006.png)</kbd>

Click on “**+ Add Prior**” again to add another “**MCRA prior**". Name the Taxon set label: “**charadriidae**”. This clade (right window) should include all species except *Haematopus ater*. Click "**OK**".

<kbd>![](./img/beauti_007.png)</kbd>

This clade should not be constrained with an age but instead to be monophyletic, so check the “**monophyletic**” box on the very right of this prior. 

<kbd>![](./img/beauti_008.png)</kbd>

We add monophyly constraints for two reasons: first, they reduce the “tree space” to be explored by the MCMC, ensuring only monophyletic clades are considered; second, they define the outgroup species correctly.

Finally, go to the “**MCMC**” tab. Set the MCMC “**Chain Length**” to two million (2,000,000) steps and leave the other fields (“Store Every”, “Pre Burnin”, and “Num Initialization Attempts”) as default. 

<kbd>![](./img/beauti_009.png)</kbd>

Click the triangle to the left of “**tracelog**”, and specify the log file name as `co1_strict_clock.log`.
Then, click the triangle next to “**treelog**”, and set the file name to  `co1_strict_clock.trees`. 

<kbd>![](./img/beauti_009_2.png)</kbd>

Finally, click on “**Save As**” in BEAUti’s “**File**” menu and save the configuration file as `co1_strict_clock.xml`.

<a name="xml"></a>
#### 6.1.1.2 Inspect the XML configuration file

BEAUti is not a black box. To see how your specifications are translated into the XML format for BEAST 2, open the generated XML file [`co1_strict_clock.xml`](res/co1_strict_clock.xml) in a text editor. 

<details>
 <summary> Background: Explanations on the XML content (click here)</summary>

--------
You will see the sequence alignment with tag “**sequence**” near the top of the file, followed by some specifications for “**maps**”; these are short names used by BEAST 2 for distribution classes. Around line 51, you’ll see an element starting with “**run**”, where the length of the MCMC chain is specified.

Parameters that are estimated during the analysis are initiated between the “**state**” elements on the next lines. This is followed by the phylogeny parameter, specified between the “**init**” tags. 

Just below that, the "**distribution**" tag begins, including the elements “**posterior**” and “**prior**”. Here you’ll find the clade definitions for the clades “all” and “charadriidae”. 

Below these, around line 135, you should see the “**likelihood**” element which includes the site model. You can see that the parameters for “**mutationRate**”, “**gammaShape**”, and “**proportionInvariant**” are all fixed (estimate=“false”). 

After line 147, the rest of the XML file includes the "**operators**", which define the methods used to modify model parameters during the MCMC search to propose new states (such as step or scale operators). 

Finally, the “**logger**” elements define which output should be written to the log files with endings “.log” and “.trees”, and to the screen. 

--------
</details>

<a name="beast"></a>
#### 6.1.1.3 Run the MCMC analysis using BEAST 2

Open the software BEAST 2 and click on “**Choose File...**” to select the XML input file [`co1_strict_clock.xml`](res/co1_strict_clock.xml). Leave everything else at their default values and click “**Run**” to start the analysis. Don't go for coffee yet, this should finish very fast.

You can estimate the time the run will take by checking the time/Msamples output in the screen log. For example, 28s/Msamples means 28 seconds for 1 million samples. 

<details>
 <summary> Note on thread/CPU usage (click here)</summary>

--------
 
You may choose more than one thread depending on your computer; however, with a single data partition, BEAST 2 will only use a single thread for the calculation of the likelihood and specifying more threads can rather slow it down. For long alignments of multi-gene datasets with several partitions that might run weeks, [optimization](https://beast.community/performance) in terms of threads, instances, CPU, and GPU can be useful. 

--------
</details>

<details>
 <summary> Issue with closing the BEAST 2 App on Mac OS X (click here)</summary>

--------
If you experience difficulties quitting the BEAST 2 App on Mac OS X, either "Force quit" BEAST 2 via the Activity Monitor or execute BEAST 2 via the Terminal using the command `/Applications/BEAST_2.7.7/bin/beast -threads 1 co1_strict_clock.xml`

--------
</details>

<kbd>![](./img/beast_001.png)</kbd>

<a name="tracer"></a>
#### 6.1.1.4 Visually inspect the posterior estimates using Tracer

If you don’t have the software Tracer yet, download and install [Tracer](https://github.com/beast-dev/tracer/releases) according to your platform ([Mac OS X](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.dmg), [Linux](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer_v1.7.2.tgz), or [Windows](https://github.com/beast-dev/tracer/releases/download/v1.7.2/Tracer.v1.7.2.zip)). 

Once the BEAST 2 analysis has finished, open the file with the ending [`.log`](res/co1_strict_clock.log) with Tracer by simply dragging the file to the “**Trace files**” panel, or by using the “**+**” button in the same window, or by choosing “**File**” > “**Import Trace file**” from the menu. You should now see a window with four parts: 

<kbd>![](./img/tracer_001_2.png)</kbd>

**Trace files** in the top left corner, lists the loaded log files – currently this is just a single file. This part of the window also specifies the number of states found in this file and the burn-in. 

<details>
	<summary> Background: burn-in (click here)</summary>

--------
The burn-in is the number of initial MCMC states removed to account for the period during which the chain hasn't yet reached the optimal region of the parameter space. This is visible in the MCMC trace as fluctuating low probabilities that gradually rise and stabilize. During the post-burnin (stationary) phase, samples are assumed to represent the true posterior probability distribution. The burn-in length depends on dataset size, model complexity, and chain length, but as a rule of thumb, discard at least the first 10% of the chain.

--------
</details>

In the **Traces** section at the bottom left of the Tracer window, statistics are shown for the posterior probability, likelihood, prior, and estimated parameters. 
The second column displays the mean estimates for each parameter, while the third column shows the “ESS” (effective sample size) value, a measure of MCMC convergence. If the ESS values are below 200, the parameter estimates may not be reliable, and the MCMC search should be extended.

**Summary statistics** in the top right part of the Tracer window provide detailed statistics for the parameter currently selected in the bottom left section of the window.

**A graph of MCMC samples** is displayed in the bottom right. By default, it shows a histogram, but when you click the “**Trace**” tab at the top right, you can see how the states of the samples change over the course of the MCMC. If the plot resembles a "hairy caterpillar" lying horizontally, it indicates that the parameter estimates have reached stationarity.

<kbd>![](./img/tracer_001.png)</kbd>

![](../img/question_icon.png) In the “**Traces**” panel, click on the parameter “**TreeHeight**”, which represents the posterior estimate for the length of the tree from the root to the tips (i.e., the age of the root). We set a prior on the root age with a mean of 50 and a standard deviation of +/- 0.01. Have a look at the summary statistics (**Estimates**), what are the values for mean and the 95% HPD (“highest posterior density”) interval? 

<details>
	<summary> Answer (click here)</summary>

--------
The “TreeHeight” is almost exactly 50 with a HPD between about 49 and 51, just as we specified it to be.

<kbd>![](./img/tracer_002.png)</kbd>

--------
</details>

<a name="co1_relaxed"></a>
## 6.1.2 Infer the Charadriidae phylogeny using the relaxed clock model

If you have BEAUti still open, you can continue editing. Otherwise, re-open the previous XML [`co1_strict_clock.xml`](res/co1_strict_clock.xml) in BEAUti by clicking on "**File**" and then "**Load**" in the menu bar. 

Go directly to the “**Clock Model**” tab, and change the model from “Strict Clock” to “**Optimised Relaxed Clock (ORC)**”. 

<details>
	<summary> Background: ORC (click here)</summary>

--------
The ORC ([Douglas et al., 2021](https://doi.org/10.1371/journal.pbio.0040088)) is a recent implementation of the relaxed clock model with optimized operators that improve the efficiency of the MCMC chain compared to the earlier uncorrelated log-normal clock of [Drummond et al. (2006)](https://doi.org/10.1371/journal.pbio.0040088). 

--------
</details>

Go to the “**Prior**” tab. 

<details>
	<summary> Error message "Could not add entry for M" (click here)</summary>

--------

If there is an error message "Could not add entry for M", close BEAUTi, re-open, and load the [`co1_strict_clock.xml`](res/co1_strict_clock.xml) XML by clicking on "**File**" and then "**Load**" in the menu bar. Go directly to the “**Clock Model**” tab, and change the model from “Strict Clock” to “**Optimised Relaxed Clock (ORC)**”. Continue to the “**Prior**” tab, the error message should not re-appear.

--------
</details>

In the “**Prior**” tab, you have three new priors, "**ORCRates**", the log-normal prior distribution for the rates of the relaxed clock, “**ORCsigma**”, the standard deviation of this log-normal prior distribution, and “**ORCucldMean**”, a multiplication factor for all relaxed clock rates. Don't change anything here, but ...

![](../img/question_icon.png) ... what would happen, if we would set the standard deviation to zero?

<details>
	<summary> Answer (click here)</summary>

--------
If the standard deviation would be zero, there would be no variation in rates among branches, meaning that the model would be identical to a strict clock. 

--------
</details>

Finally, go to the “**MCMC**” tab, and change the name of the log file to `co1_relaxed_clock.log` and of the tree file to `co1_relaxed_clock.trees`. Save the XML file under the name `co1_relaxed_clock.xml` by clicking “**Save As**” from the “**File**” menu. 

As before, use **BEAST 2** to run an analysis, but now use the XML file [`co1_relaxed_clock.xml`](./res/co1_relaxed_clock.xml) as input. If BEAST 2 is still open from the previous analysis, close and re-open it to load the new XML file. Leave everything at their default values and click “**Run**” to start the analysis. This should only take a few minutes. You can check how long with the time/Msamples given in the screen log.


<a name="strict_vs_relaxed"></a>
## 6.1.3. Compare the results of the strict and relaxed clock analyses

<a name="compare_tracer"></a>
### 6.1.3.1 Compare the log files using Tracer

Once the analysis has finished, add the log file [`co1_relaxed_clock.log`](./res/co1_relaxed_clock.log) to Tracer, with the log of the strict clock model still open. 

![](../img/question_icon.png) Look at the "**Trace**" tab and the ESS values, do you consider the analysis as converged?

<details>
	<summary> Answer (click here)</summary>

--------
Check the “Trace” of the posterior for convergence. Does it look like a hairy caterpillar? Are all ESS (effective sample size) values above 200? 

While an ESS larger than 200 typically indicates stationarity or convergence (for multiple replicates), this is just a reference value. Higher ESS values may be needed for reliable convergence, or lower values might be sufficient in some cases. In Tracer, ESS values below 100 are highlighted in red (indicating unreliable results), and values between 100 and 200 are shown in yellow (trust only after individual inspection). If many ESS values are below 200, it's likely the chain hasn't run long enough.

--------
</details>

In the "**Trace File tab**", select both trace files. You will see summary statistics and graphs for both analyses. In the bottom-left part of the window, have a look at the ”**posterior**” and “**likelihood**”. 

![](../img/question_icon.png) Which of the two models appears to be better? And why can we not interpret the result like that?

<details>
	<summary> Answer (click here)</summary>

--------
The posterior and likelihood for the relaxed-clock model are better (less negative values) than for the strict-clock model. However, remember these values cannot be directly compared between models because the relaxed-clock model has many more parameters, leading to a higher likelihood and posterior. Similar to maximum likelihood, where models are compared using a "likelihood ratio test" or "AIC," models in a Bayesian framework can be compared using Bayes Factors.

<kbd>![](./img/tracer_003.png)</kbd>

--------
</details>

![](../img/question_icon.png) Select ”**mrca.age(charadriidae)**” to see more detailed statistics about the age estimate of Charadriidae. Identify the lower and upper boundaries of the 95% HPD interval for this age. How does the width of the interval for the strict clock compare to that for the relaxed clock?

<details>
	<summary> Answer (click here)</summary>

--------
The HPD of the relaxed clock is wider since the relaxed clock allows the substitution rate to vary. This may well also be more appropriate. 

<kbd>![](./img/tracer_004.png)</kbd>

--------
</details>

<a name="compare_trees"></a>
### 6.1.3.2 Compare the tree files using TreeAnnotator and FigTree

<details>
	<summary> Optional: Inspect the entire posterior tree sample (click here)</summary>

--------
Open the file [`co1_strict_clock.trees`](./res/co1_strict_clock.trees) in [FigTree](https://github.com/rambaut/figtree/releases/tag/v1.4.4). The displayed phylogeny may look odd, with many very short branches, as it's the first tree sampled from the MCMC. In the second tab of the left menu, you'll see “**Current Tree: 1 / 5001**” (or another number depending on how many trees were logged). This is the starting tree that was randomly generated by BEAST 2 to initiate the MCMC chain. At the right of the top menu, you’ll see two buttons for “**Prev/Next**”. If you click on the symbol for “**Next**” repeatedly, you can see how the sampled phylogenies have changed throughout the MCMC search. Instead of clicking on this icon 5,000 times to see the last phylogeny, click on the **triangle** to the left of “Current Tree: X/5001” in the menu. This will open a field where you can directly enter the number of the tree that you’d like to see. Type “**5,001**” and hit enter. This will display the last sampled phylogeny, which should look more realistic. However, keep in mind that this is just one tree from the posterior tree sample, and may not represent the entire collection of trees sampled during the MCMC. This collection is called the “posterior tree sample”.

--------
</details>

To generate a representative phylogeny summarizing the information from the posterior tree sample, open the program **TreeAnnotator**, part of the BEAST 2 package. In "**Input Tree File**" load the tree file [`co1_strict_clock.trees`](./res/co1_strict_clock.trees). Also specify the same name, but with the ending `.tree` instead of `.trees` for the "**Output Tree File**". Define a burn-in percentage according to your interpretation of the log file in Tracer (usually 10 to 50%). Leave the default options for “Posterior probability limit” and “Target tree type” to generate a “**Maximum clade credibility tree**”, a summary tree for Bayesian analyses that selects the tree in which the sum of posterior probabilities for all its clades is the highest among the posterior sample ([Heled & Bouckaert, 2013](https://doi.org/10.1186/1471-2148-13-221)). However, as “**Node heights**”, choose “Mean heights” rather than the default “Common Ancestor heights”. Mean heights are calculated for all trees in the set where the clade is monophyletic, whereas common ancestor heights are calculated based on the average over all trees in the set.  Then, click “**Run**”. This should only take seconds.

<kbd>![](./img/treeannotator_001.png)</kbd>

Close the program and re-open it to repeat the summarizing of trees with the same settings for the [`co1_relaxed_clock.trees`](./res/co1_relaxed_clock.trees) file. Save it as `co1_relaxed_clock.tree`.

Open both the [`co1_strict_clock.tree`](./res/co1_strict_clock.tree) and [`co1_relaxed_clock.tree`](./res/co1_relaxed_clock.tree) files in [FigTree](https://github.com/rambaut/figtree/releases/tag/v1.4.4) in two separate windows. Apply the next steps to both tree files. 

Sort the taxa according to node order using **"Decreasing node order**" in FigTree's "**Tree**" menu (or command-d). This should move *Haematopus ater* to the top of the plot. 

To see the support values for each node, set a tick next to “**Node Labels**” in the menu on the left and click on the triangle to open the menu. Choose “**posterior**” from the drop-down menu next to “**Display**”. Most clades are rather well-supported with a posterior probability close to 1. For Bayesian posterior probabilities, a value above 0.95 is considered reliable and means that the tree is correct with a probability of 95% (assuming that the model is correct).

To see the age estimates, check the box next to “**Scale axis**”. This will display a time scale grid, but the ages on the scale at the bottom may appear incorrect. To fix this, click the "**triangle**" next to “Scale Axis” to open the corresponding field, and then tick the “**Reverse axis**” option. 

![](../img/question_icon.png) Where did we place the age constraint? Does the age corresponds to the age we specified?

<details>
	<summary> Answer (click here)</summary>

--------
We set a "root" age constraint on the root including all species in the analysis. Yes, the root age lies at the specified value around 50 million years ago. 

<kbd>![](./img/figtree_001.png)</kbd>

--------
</details>

![](../img/question_icon.png) Compare the tree topologies and branch lengths. Do you notice a difference?

<details>
	<summary> Answer (click here)</summary>

--------
The tree topology appears very similar between the two phylogenies; however, there are some topological differences and some of the branches vary in length. 

Most importantly, in the strict clock phylogeny, *Thinornis rubricollis*, and *T. novaeseelandiae* are sister, but in the relaxed clock phylogeny, *E. melanops* is sister to *T. novaeseelandiae*.
If you recall the co1 maximum-likelihood phylogeny from [Activity 4.4](../maximum_likelihood_phylogenetic_inf/README.md), you may remember that *E. melanops* had a much longer branch that *T. novaeseelandiae*. In the strict clock analysis, this apparent discrepancy in branch rates may have led to the position of the *T. rubricollis* / *T. novaeseelandiae* clade as sister species to *E. melanops*. In contrast, these different rates could be accommodated in the analysis using the relaxed clock model, allowing shared substitutions between *E. melanops* and *T. novaseelandiae* to dominate over the differences in the mutation rate.

Strict clock (left image), relaxed clock (right imgge).
<kbd>![](./img/figtree_002.png)</kbd>

--------
</details>


<a name="nodata"></a>
## Optional: Sample from the prior distribution and ignore the data

**Note**: If you are short on time, please proceed directly to [part 6.3](#dispersal_dating), our most important analysis where the dispersal time of the four endemic New Zealand species is estimated.

In this optional activity another BEAST 2 analysis is set up, in which the likelihood of the data is completely ignored and instead MCMC samples are taken strictly according to the prior probability. Sampling from the prior without regarding the data can help us to assess how informative our data is and whether there is conflicting information between the prior and the data.

To sample only from the prior probability, go back to BEAUti. Reload the file “co1_relaxed_clock.xml” in case you closed the program. Proceed directly to the MCMC tab. Change the names for tracelog and treelog to `co1_relaxed_clock_from_prior.log` and `co1_relaxed_clock_from_prior.trees`, respectively. Also change the frequency at which states are written to the screen (“**Log Every**”) from 1,000 to 10,000. At the very bottom check the box for “**Sample From Prior**”. Save the xml using “Save as” using the name `co1_relaxed_clock_from_prior.xml`.

Open [`co1_relaxed_clock_from_prior.xml`](./res/co1_relaxed_clock_from_prior.xml) as "**Input file**" for BEAST 2 and start the analysis by clicking on “**Run**”. This analysis should finish very quickly. 
Open the log file in Tracer (keep the two previous log files in Tracer open or re-open them). You may get a warning that some traces cannot be displayed. This is because we do not have traces for the likelihood since we ran the analysis without the data.
On the left side, select the “**posterior**” and the “**prior**” jointly (holding the “command” key). In the upper right panel, keep “**Estimates**” to view the two distributions together and compare the graphs among the three analysis (by clicking on the different trace files in the upper panel). 

![](../img/question_icon.png) What is the obvious difference between the prior/posterior distributions with and without data? 

<details>
	<summary> Answer (click here)</summary>

--------
Without data (left image), the posterior corresponds to the prior, with data (right image), the prior probabilities are updated by the data and thus the posterior distribution should differ from the prior distribution.

<kbd>![](./img/tracer_005.png)</kbd>

--------
</details>

![](../img/question_icon.png) In the "**Trace File**" panel, select the `co1_relaxed_clock_from_prior.log` file. Then, click on the parameter “**BDDeathRate**”, the “relative extinction rate". We specified this rate to be between 0 and 1 using a uniform prior. In the histogram in the "**Estimates**" tab, you can see that our MCMC has sampled the entire uniform distribution between 0 and 1 almost homogeneously. 
Now click on the "**Trace File**" for the strict or relaxed clock. Here, the posterior rather resembles an exponential distribution with more samples towards smaller values. How can we interpret this difference?

<details>
	<summary> Answer (click here)</summary>

--------
The difference means that our data is rather informative in assuming an extinction rate closer to 0 than at higher values, which we would also expect. On the other hand, a high similarity between the prior and the posterior would suggest that the data contains little information about the parameters. Having said that, the slight tendency of the BDDeathRate posterior towards smaller values when sampled only from the prior stems actually from the data. Although we do not consider the alignments in this analysis, the number of species as well as the age constraint are used in this analysis. This is also the reason for the BDBirthRate posterior to be more similar between the analyses with or without data. The program knows that there are 22 species, so these species are assumed to have evolved within our age constraint.

<kbd>![](./img/tracer_006.png)</kbd>

--------
</details>


<a name="dispersal_dating"></a>
## 6.2 Estimate the time of arrival of the endemic New Zealand shorebirds

Finally, we will use all our data to estimate the time of arrival of the endemic New Zealand shorebirds.

Download the three alignment files for [12s](./data/12s.nex), [CO1](./data/co1.nex), and [RAG1](./data/rag1.nex) (all in Nexus format). These alignments are the ones you generated in [Activity 2 (Multiple sequence alignment)](./multiple_sequence_alignment/README.md), but modified to contain identical species and species IDs.

Close and re-open BEAUTi, go to “**File**” > “**Import Alignment**”, select all **three alignments** for [12s](./data/12s.nex), [CO1](./data/co1.nex), and [RAG1](./data/rag1.nex) and click “**open**”.  

We will partition the CO1 and RAG1 sequences into codon positions to allow for independent model assumptions. Click on the row for the **CO1 alignment** to select it. Then, click the “**Split**” button at the bottom of the BEAUti window. As suggested, split the alignment into partitions “**1 + 2 + 3**”, which will divide the alignment by codon position. Click “**OK**”. You should now see three rows for the CO1 alignment.

<kbd>![](./img/beauti_010.png)</kbd>

Now select the **RAG1** row. Similar to the final maximum likelihood analysis ([Activity 4.5](../maximum_likelihood_phylogenetic_inf/README.md)) we will partition the RAG1 gene only into two parts, combining codon positions 1 and 2, which fit a similar substitution model, but keeping codon position 3 separate. Therefore, click the “**Split**” button again, but now select to split into “**{1,2}+3**”. You should have six rows now, one for the 16s, three for CO1, and two for RAG1.

**Select all six rows** at the same time (ctrl+A or command+A or holding the “shift” key and click), then click on “**Link Trees**” near the top of the BEAUti window to force BEAST 2 to use the same phylogeny for all partitions. For our data set, this is equivalent to the concatenation that we did with IQ-TREE. If we had sequences for many more genes (tens to thousands) it would be more appropriate to use an alternative model that allows the phylogenies of genes to differ from each other to account for “coalescent” processes such as “incomplete lineage sorting”. This topic will be covered in the next lecture.

With all six rows still selected, also click on “**Link Clock Models**”. This means that the clock model that we will select for 12s will also be applied to all partitions of the CO1 and RAG1 genes. 

<details>
	<summary> Background: Linking clock models across genes (click here)</summary>

--------
With the relaxed clock model that we will select, some branches are allowed to evolve faster than other branches (= have higher substitution rates than others), but this variation in rates is not inferred separately for each gene. Thus, branches that are inferred to have a high rate in the 12s alignment will also receive a high rate for each of the CO1 and RAG1 partitions. However, this branch will still be allowed to have a higher or slower absolute rate in one partition compared to another partition because the branch rates specified by the clock model (one rate per branch) will still be multiplied by a partition-specific rate multiplier (so that in total we then have six rates per branch: one for each partition). The justification for the linking of clock models is that the speed of the molecular clock often depends on factors that are species-specific, such as metabolism and generation time, in addition to gene-specific factors.

--------
</details>

<kbd>![](./img/beauti_011.png)</kbd>

As before, skip the “Tip Dates” tab and go to the “**Site Model**” tab. Select the **12s partition** in the panel at the left and click on the drop-down menu that currently says “JC69”. Instead of the Jukes-Cantor model, select the "**HKY**" (Hasegawa-Kishino-Yano) model.

<details>
	<summary> Background: Selecting the substitution model (click here)</summary>

--------
Our previous model test in [Activity 3](./substitution_model_selection/README.md) suggested the “TIM2+F+I+G4” substitution model for the 12s alignment. However, there are two reasons why we will diverge from this suggestion:

* The first issue is that this model cannot be selected in BEAST 2. While we could assume a GTR model and set one rate equal (AC=AT), we cannot set the second rate equal. 
* The second reason is that with such complex models with many parameters, the analysis would take too long for the time frame of this course. 

For an in-depth analysis, we would probably use model selection in BEAST 2 as part of the analysis (as we did in IQ-TREE). This is possible with an add-on package: bModelTest ([Bouckaert and Drummond, 2017](https://doi.org/10.1186/s12862-017-0890-6)). 


--------
</details>

Also add a Gamma model of rate heterogeneity. To do so, specify “**4**” in the field for the “**Gamma Category Count**”.

<kbd>![](./img/beauti_012.png)</kbd>

Still in the “Site Model” tab, select **all CO1 and RAG1 partitions** in the panel at the left of the window. The main part of the window should then show the option “**Clone from 12s**”. Click “**OK**” to use the same site model as for 12s for all other partitions. 

<kbd>![](./img/beauti_013.png)</kbd>

Then, continue to the “**Clock Model**” tab. Choose again the “**Optimised Relaxed Clock (ORC)**” as we did before.

Proceed to the “**Priors**” tab. Unlike before, when using BEAUti, a large number of parameters will now be listed, primarily related to the HKY site model (base frequencies, gamma, and kappa, which is the transition/transversion rate ratio). 
From the drop-down menu at the top of the window, select the “**Birth Death Model**” instead of “Yule Model” as the diversification model ("**Tree.t**").

<kbd>![](./img/beauti_014_2.png)</kbd>

Then scroll to the bottom of the window, where you should see the “**+ Add Prior**” button at the end of the list of parameters. Use this button to again define clades. When asked “**Which prior do you want to add**”, choose “**MRCA prior**”. Define the following two clades: 
 
1) “**charadriidae**”, including all species except for *Haematopus ater* (just as before). Once specified, tick the box “**monophyletic**” next to it.  
2) “**charadriidae\_except\_pluvialis**”, including all species except for *Haematopus ater* and *Pluvialis squatarola*.  
This second clade is where we will place our age constrain, which is not on the root this time, but on the internal node splitting *Pluvialis* from all other Charadriidae. Since the basal topology of Charadriidae disagrees between previous molecular phylogenies, we will use an age constraint for the most ancient well-supported internal node rather than the root. Also, the fossil record of Charadriidae is limited to fragmented remains where the taxonomic assignment is difficult. Therefore, we use a constraint from a large-scale phylogeny of extant birds, which was dated on the basis of ten well-known fossils ([Jetz et al. 2012](https://doi.org/10.1038/nature11631)).  

In order to constrain the age of “**charadriidae\_except\_pluvialis**”, click the drop-down menu in the third column and select the “**Log Normal**” distribution. To specify the parameters of the log normal distribution for the prior probability, click the **black triangle** at the very left. This time we will choose a more meaningful but still rather narrow age constrain according to the results by [Jetz et al. (2012)](https://doi.org/10.1038/nature11631). Set a tick in the box next to "**Mean In Real Space**" and enter **38.85** for the **mean (M)** and **0.1** for the **standard deviation (S)**, leave the offset at 0. This means that the clade Charadriidae, except Pluvialis, is around 38.85 million years old, with a soft minimum and maximum age of divergence between 45.6 and 32.8 Ma.

<kbd>![](./img/beauti_014.png)</kbd>

Continue to the “**MCMC**” tab. Use 5,000,000 as the chain length, but change the names of the output files: Click on the triangle to the left of “**tracelog**” and specify `div_date.log`. Then click on the black triangle to the left of “**treelog**”. Specify `div_date.trees` as the file name. Finally, save the XML file as `div_date.xml` and open it in BEAST 2 to run the MCMC search. The thread/CPU usage should be adjusted because multiple partitions are included. For me the run-time was shortest using 3 threads (~3 min/Msample; 15 min total) and longer with more or fewer threads.

This is a good moment to take a coffee break. However, if your analysis will need much more than 15 min, instead of waiting you may use the result files provided in this repository for the following steps.

![](../img/question_icon.png) Open the file [`div_date.log`](./res/div_date.log) in Tracer and find out if the MCMC search has reached stationarity (*i.e.*, do all parameters have ESS values above 200 in the bottom left part of the Tracer window?

<details>
	<summary> Answer (click here)</summary>

--------
Probably not as 5 million iterations is a relatively short chain for this data set and model. For a comparison, you may open the [`div_date_long.log`](./res/div_date_long.log) file, containing 20 million iterations.

--------
</details>

![](../img/question_icon.png) How much percent should be cut away from the beginning of the MCMC chain as “burnin”? 

<details>
	<summary> Answer (click here)</summary>

--------
This depends on how quickly the chain reached stationarity, but about 10% is often used.

--------
</details>

Then, open the program "**TreeAnnotator**" to generate a “**Maximum clade credibility tree**”. Specify a burnin percentage according to your interpretation of the log file in Tracer. For “**Node heights**”, choose “**Mean heights**” As input file, select the file [`div_date.trees`](./res/div_date.trees) and specify `div_date.tree` as the output file name. Then, click “**Run**”.

Once TreeAnnotator has finished, open the new file [`div_date.tree`](./res/div_date.tree) in **FigTree**. Sort the taxa according to node order using "**Decreasing node order**" in FigTree's "**Tree**" menu. To see the support values for each node, set a tick next to “**Node Labels**” in the menu on the left and click on the triangle next to it. Then choose “**posterior**” from the drop-down menu to the right of “**Display**”. You’ll see that most clades are well-supported. To also see the age estimates, check the box next to “**Scale axis**” and set a tick for “**Reverse axis**” in this field. To also see the uncertainty in the age estimates, tick the box next to “**Node Bars**” and click on the triangle. Choose the first item from the drop-down menu for “**Display**”, the “**height\_95%\_HPD**”. You should then see blue bars on each node, these indicate the age range within which the node lies with 95% certainty.

![](../img/question_icon.png) Inspect the tree topology, are there differences compared to the maximum likelihood tree?

<details>
	<summary> Answer (click here)</summary>

--------
Yes, but mostly for rather weakly supported clades.

--------
</details>

![](../img/discussion_icon.png) Identify the four New Zealand endemic species. Can we interpret their dispersal patterns with more confidence? When did they arrive in New Zealand? Was it one or multiple dispersal events? Discuss with your neighbours.

<details>
	<summary> Discussion points (click here)</summary>

--------

* Where in the phylogeny are the New Zealand endemics?
* One or several clades?
* Origin of this clade / these clades
* 95% HPD
* Posterior probabilities

--------
</details>

<details>
	<summary> Answer (click here)</summary>

--------
The clade combining the three plovers endemic to New Zealand (*A.frontalis*, *C. bicinctus* and *C. obscurus*) apparently originated about 18 Ma, with a 95% HPD of ca. 13 – 24 Ma. In contrast to the maximum likelihood tree, the three New Zealand species from a well-supported clade (PP 1.0) in the Bayesian tree, indicating that they originate from a single dispersal event and have speciated on New Zealand.  

However, *Thinornis novaeseelandiae*, the fourth New Zealand species, may have arrived a little later (about 11 Ma, 95% HPD ca. 5.5 – 17 Ma) in an independent dispersal event, since it was resolved within the paraphyletic Charadrius group.

<kbd>![](./img/div_date_long.tree.png)</kbd>

--------
</details>

