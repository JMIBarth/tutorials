# Substitution Model Selection (Activity 3)

## Objective

Perform selection of the best-fit substitution model for phylogenetic inference.  In Activity 1, we learned how to retrieve homologous sequences, and in Activity 2 we aligned these sequences to match homologous sites. In this activity, we will select an appropriate substitution model using maximum likelihood (ML) inference.
 

## Table of contents

* [Run model selection for the 12s alignment](#model_12s)


<a name="model_12s"></a>
## 3.1 Run model selection for the 12s alignment

 <details>
  <summary>Background: Maximum likelihood inference software (click here)</summary>  
  
--------

One of the earliest programs for phylogenetic analysis is the software [PAUP*](http://phylosolutions.com/paup-test/) by Dave Swofford that you used on the first course day. It was developed in the late 80s and has traditionally been one of the most frequently used and cited phylogenetic programs. However, the continuous advancing of sequencing technologies has led to a massive growth of datasets, promoting the development of newer and faster tools for phylogenetic inference. These include for example: [PhyML](http://atgc.lirmm.fr/phyml/) ([Guindon and Gascuel, 2003](https://doi.org/10.1080/10635150390235520)), and [RAxML](https://cme.h-its.org/exelixis/web/software/raxml/index.html) ([Stamatakis, 2006](https://doi.org/10.1093/bioinformatics/btl446)).  

In this Activity, we will make use of one of the latest of these developments, the software [IQ-TREE](http://iqtree.cibiv.univie.ac.at) ([Nguyen et al. 2015](https://doi.org/10.1093/molbev/msu300)). IQ-TREE takes advantage of an optimized implementation of the likelihood functions for better computational efficiency while yielding comparable or even better phylogenetic estimations. All programs listed above are also available on web-servers, but only PhyML and IQ-TREE include an automated substitution-model selection step, using SMS ([Lefort et al., 2017](https://doi.org/10.1093/molbev/msx149)) and ModelFinder ([Kalyaanamoorthy et al., 2017](https://doi.org/10.1038/nmeth.4285)), respectively. Finally, for IQ-TREE a very detailed and user-friendly [manual](http://www.iqtree.org/doc/) is available. 

--------
</details>

In the lecture, we discussed different evolutionary models that may be more or less suitable for our dataset at hand: different base frequencies, varying rates of substitution (for example between transitions and transversions), and rate variation among sites. Combining these parameters, a plethora of evolutionary models can be obtained (in IQ-TREE’s ModelFinder, 88 models of sequence evolution are tested against each other!).





