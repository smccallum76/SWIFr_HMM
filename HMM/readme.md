# Hidden Markov Model Overview

Identifying adaptive mutations in genetic data is challenging due to the low frequency of occurrence of 
such events, and because signatures of selection are intertwined with the footprints of various other 
evolutionary forces that shape our genomes. Even when a larger region appears to be under selection, 
genomic sites that are linked to adaptive mutations have similar statistical signals, and thus can 
obfuscate the identification of the actual adaptive mutation. The new method described here uses a 
Hidden Markov Model (HMM) that allows for classification of neutral, linked, and sweep (adaptive mutation) 
genomic sites. This model is general and can be scaled to allow for an arbitrary number of classes. 
Using simulated genetic data, site-specific selection statistics are taken as input, and site probabilities 
and classifications are the resulting outputs. The Viterbi algorithm is used to identify the most likely 
path through all classes along the sequence. A stochastic backtrace method allows for the identification 
of multiple possible paths. These methods, in combination with enforcing sweep events, help to identify 
regions under selection, and allow for better localization of adaptive mutations. 

The HMMcode described here is uses alongside the SWIF(r)code described 
in detail by Sugden *et al.,* (2018). Using the Gaussian Mixture Model (GMM) and input statistics from the 
SWIF(r) code/output, the HMM provides an alternative set of classification predictions. While SWIF(r) leverages all 
input statistics, the HMM, in its current state, uses only one statistic at a time for predictions. 

A brief overview of HMM code components follows:

## `hmm_funcs.py`
This code contains the various function called by `hmm_run` for ingesting data and making classification 
predictions, specifically:
- `hmm_forward` -- Forward algorithm (probability of all observations up to a given locus)
- `hmm_backward` -- Backward algorithm (probability of the remainder of sequence from a given locus)
- `hmm_gamma` -- Probability of an observation being emitted from a hidden state relative to all other states
- `hmm_viterbi` -- Maximum likelihood path through the hidden states
- `stochastic_backtrace` -- Set of possible paths through the hidden states

## `hmm_run.py`
The run code calls the various functions described above and yields a set of hidden state predictions. Prior
to running this script the `swifr_train` code must be run as it provides the GMM parameters required by the
HMM.

The location of the test data and training data must be specified *Path to Data* section of the code. The 
number of stochastic backtrace paths are defined by the user by the variable `stoch_sims`. The output of 
this script is stored in a SQLite database that is created automatically upon running. The tables that are
generated in the database include the following:
- **hmm_predictions_Xclass** -- Table of predictions provided by the HMM at each genomic position
- **sbt_predictions_<stat>_Xclass** -- Table of stochastic backtrace predictions provided by the HMM at each genomic position


Sugden, L. A., Atkinson, E. G., Fischer, A. P., Rong, S., Henn, B. M., & Ramachandran, S. (2018). Localization 
of adaptive variants in human genomes using averaged one-dependence estimation. Nature communications, 9(1), 703.

