# LHCb-topo-trigger
LHCb RUN-II topological trigger upgrading

## Links

* If you are interested in details, see [paper](http://arxiv.org/pdf/1510.00572)


## LHCb trigger system RUN-I

<img src='https://raw.githubusercontent.com/tata-antares/LHCb-topo-trigger/master/img/triggers-system.PNG' width=250 alt='LHCb trigger system' />

## LHCb trigger system RUN-II: upgrading
For RUN-II new scheme is applied:

* HLT1 track
* HLT1 2-body 
* HLT2 n-body

<img src='https://raw.githubusercontent.com/tata-antares/LHCb-topo-trigger/master/img/sheme.png' width=350 alt='new topo scheme' />


## LHC
* Sample: one proton-proton bunches collision, called Event (40MHz)
* Event consists of the secondary vertices (SVR) or tracks, where particles are produced
* Features: an SVR, tracks and its products physical characteristics reconstructed from the detectors (momentum, mass, angles, impact parameter)

<img src='https://raw.githubusercontent.com/tata-antares/LHCb-topo-trigger/master/img/bdecayinjet.png' width=350 alt='LHC event' />

## Data
* Training data are set of SVRs for *HLT2 n-body* and *HLT1 2-body* or trakcs for *HLT1 track* all events
* Monte Carlo 2015 data (signal data) were simulated for various types of interesting events (different decays):
  * all decays are used in *HLT1 2-body* and *HLT1 track* training
  * six types of decays are used for *HLT2 n-body* training and all for testing
* Minimum bias data (real data for a small period of time) are used as background data
* Event is interesting from physical point of view if it contains at least one SVR, where searched decay happens

<img src='https://raw.githubusercontent.com/tata-antares/LHCb-topo-trigger/master/img/triggers-svg.png' width=250 alt='Event which passes trigger system' />

## Features:
* SumPT (sumpt): sum of transverse momentums (pt) for all tracks in the SVR;
* MCOR (mcor): "corrected" mass of the SVR;
* IPChi2 (ipchi2): impact parameter chi2 of the SVR;
* MinPT (minpt): the minimum of tracks pt in the SVR;
* FDChi2 (fdchi2): flight distance chi2 of the SVR from the p-p collision;
* NIPChi2LT16 (nlt16):  number of tracks in the primary vertex with IPChi2 < 16;
* N (n): number of tracks in the SVR;
* NHLT1 (n1trk): number of tracks passing HLT1 (high level trigger first stage);
* VChi2 (chi2): vertex chi2 of the SVR;
* Eta (eta): pseudorapidity;
* PT (pt): transverse momentum;
* M (m): mass of the SVR;
* MinFDR (fdr): min radial (x-y plane) flight distance to any p-p collision;
* SumIPchi2 (sumipchi2): sum of IPchi2 for all tracks in the SVR;

## ML problem
* Output rate is fixed, thus, false positive rate (FPR) for events is fixed
* Goal is to improve efficiency for each type of signal events 
* We improve true positive rate (TPR) for fixed FPR for events

<img src='https://raw.githubusercontent.com/tata-antares/LHCb-topo-trigger/master/img/roc_events.png' width=550 alt='ROC curve interpretation' />

## Production trigger system
There are two possibilities to speed up prediction operation for production stage:
* Bonsai boosted decision tree format (BBDT)
  * Features hashing using bins before training 
  * Converting decision trees to n-dimensional table (lookup table)
  * Table size is limited in RAM, thus count of bins for each features should be small
  * Discretization reduces quality
* Post-pruning (MatrixNet includes several thousand trees)
  * Train MatrixNet with several thousands trees
  * Reduce this amount of trees to a hundred
  * Greedily choose trees in a sequence from the initial ensemble to minimize a modified loss function (exploss for background and logloss for signal)
  * Change values in leaves (tree structure is preserved)

## Results
![Comparison HLT2 efficiency (HLT-high level trigger) relation to HLT1 between Run 1 and  new trigger system (without random forest trick). These channels are reconstructible signal decays with pt(B) > 2 GeV and tau(B) > 0.2 ps.](https://github.com/tata-antares/LHCb-topo-trigger/raw/master/img/LHCb_triggers.png)

## Reproducibility
* download root files to folder *datasets*
* run [preprocessing](https://github.com/tata-antares/LHCb-topo-trigger/blob/master/0_Preprocessing.ipynb) to create .csv files with tracks and SVRs 
* [HLT1 track](https://github.com/tata-antares/LHCb-topo-trigger/blob/master/HLT1-track.ipynb) creates models and plots for *HLT1 track* trigger
* [HLT1 2-body](https://github.com/tata-antares/LHCb-topo-trigger/blob/master/HLT1.ipynb) creates models and plots for *HLT1 2-body* trigger
* [HLT2 n-body] (https://github.com/tata-antares/LHCb-topo-trigger/blob/master/HLT2.ipynb) creates models and plots for *HLT2 n-body* trigger
* [BBDT and post-pruning](https://github.com/tata-antares/LHCb-topo-trigger/blob/master/HLT2-TreesPruning.ipynb) creates production models with BBDT format and post pruning for *HLT2 n-body*
* [BBDT format](https://github.com/tata-antares/LHCb-topo-trigger/blob/master/BBDT-prepare.ipynb) creates *.bbdt* files (lookup table) from trained trees 

## Requirements
* [rep](http://github.com/yandex/rep) 0.6.3
* [rep_ef](https://github.com/anaderi/REP_EF) 0.6.2
* [hep_ml](https://github.com/arogozhnikov/hep_ml) 0.3.0
