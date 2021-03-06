Changelog
=========

2.5 (??-??-????)
----------------

**New features**:

- msm: Added Augmented Markov Models. A way to include averaged experimental
  data into estimation of Markov models from molecular simulations. The method is described in [1]. #1111

- References:

  [1] Olsson S, Wu H, Paul F, Clementi C, Noe F: Combining Experimental and Simulation Data
      via Augmented Markov Models" In revision.

**Fixes**:

- datasets: fixed get_multi_temperature_data and get_umbrella_sampling_data for Python 3. #1102
- coordinates: fixed StreamingTransformers (TICA, Kmeans, etc.) not respecting the in_memory flag. #1112
- coordinates: made TrajectoryInfoCache more fail-safe in case of concurrent processes. #1122
- msm: fix setting of dt_model for BayesianMSM. This bug led to wrongly scaled time units for mean first passage times,
  correlation and relaxation times as well for timescales for this estimator. #1116
- coordinates: Added the covariance property of time-lagged to CovarianceLagged. #1125

2.4 (05-19-2017)
----------------

**New features**:

- msm: variational scores for model selection of MSMs. The scores are based on the variational
  approach for Markov processes [1, 2] and can be employed for both reversible and non-reversible
  MSMs. Both the Rayleigh quotient as well as the kinetic variance [3] and their non-reversible
  generalizations are available. The scores are implemented in the `score` method of the MSM
  estimators `MaximumLikelihoodMSM` and `OOMReweightedMSM`. Rudimentary support for Cross-validation
  similar as suggested in [4] is implemented in the `score_cv` method, however this is currently
  inefficient and will be improved in future versions. #1093

- config: Added a lot of documentation and added `mute` option to silence PyEMMA (almost completely).

- References:
    [1] Noe, F. and F. Nueske: A variational approach to modeling slow processes
        in stochastic dynamical systems. SIAM Multiscale Model. Simul. 11, 635-655 (2013).
    [2] Wu, H and F. Noe: Variational approach for learning Markov processes
        from time series data (in preparation).
    [4] Noe, F. and C. Clementi: Kinetic distance and kinetic maps from molecular
        dynamics simulation. J. Chem. Theory Comput. 11, 5002-5011 (2015).
    [3] McGibbon, R and V. S. Pande: Variational cross-validation of slow
        dynamical modes in molecular kinetics, J. Chem. Phys. 142, 124105 (2015).

- coordinates:
   - kmeans: allow the random seed used for initializing the centers to be passed. The prior behaviour
     was to init the generator by time, if fixed_seed=False. Now bool and int can be passed. #1091

- datasets:
   - added a multi-ensemble data generator for the 1D asymmetric double well. #1097

**Fixes**:

- coordinates:
  - StreamingEstimators: If an exception occurred during flipping the `in_memory` property,
    the state is not updated. #1096
  - Removed deprecated method parametrize. Use estimate or fit for now. #1088
  - Readers: nice error messages for file handling errors (which file caused the error). #1085
  - TICA: raise ZeroRankError, if the input data contained only constant features. #1055
  - KMeans: Added progress bar for collecting the data in pre-clustering phase. #1084

- msm:
  - ImpliedTimescales estimation can be interrupted (strg+c, stop button in Jupyter notebooks). #1079

- general:
  - config: better documentation of the configuration parameters. #1095


2.3.2 (2-19-2017)
-----------------

**New features**:

thermo:

- Allow for periodicity in estimate_umbrella_sampling().
- Add *_full_state getter variants to access stationary properties on the full set of states
  instead of the active set.

**Fixes**:

coordinates:

- [TICA] fixed regularization of timescales for the non-default feature **commute_map**. #1037, #1038

2.3.1 (2-6-2017)
----------------

**New features**:

- msm:
   - ImpliedTimescales: enable insertion/removal of lag times.
     Avoid recomputing existing models. #1030

**Fixes**:

- coordinates:
   - If Estimators supporting streaming are used directly, restore previous behaviour. #1034
     Note that estimators used directly from the API were not affected.


2.3 (1-6-2017)
--------------

**New features**:

- coordinates:
   - tica: New option "weights". Can be "empirical", which does the same as before,
     or "koopman", which uses the re-weighting procedure from [1] to compute equi-
     librium covariance matrices. The user can also supply his own re-weighting me-
     thod. This must be an object that possesses a function weights(X), that assigns
     a weight to every time-step in a trajectory X. #1007
   - covariance_lagged: This new method can be used to compute covariance matrices
     and time-lagged covariance matrices between time-series. It is also possible
     to use the re-weighting method from [1] to compute covariance matrices in equi-
     librium. This can be triggered by the option "weights", which has the same spe-
     cifications as in tica. #1007

- msm:
   - estimate_markov_model: New option "weights". Can be empirical, which does the
     same as before, or "oom", which triggers a transition matrix estimator based
     on OOM theory to compute an equilibrium transition matrix from possibly non-
     equilibrium data. See Ref. [2] for details. #1012, #1016
   - timescales_msm: The same change as in estimate_markov_model. #1012, #1016
   - TPT: if user provided sets A and B do not overlap (no need to split), preserve order of user states. #1005

- general: Added an automatic check for new releases upon import. #986

- References:
   [1] Wu, H., Nueske, F., Paul, F., Klus, S., Koltai, P., and Noe, F. 2017. Bias reduced variational
        approximation of molecular kinetics from short off-equilibrium simulations. J. Chem. Phys. (submitted),
        https://arxiv.org/abs/1610.06773.
   [2] Nueske, F., Wu, H., Prinz, J.-H., Wehmeyer, C., Clementi, C., and Noe, F. 2017. Markov State Models from
        short non-Equilibrium Simulations - Analysis and Correction of Estimation Bias. J. Chem. Phys.
        (submitted).


**Fixes**:

- coordinates:
   - kmeans: fixed a rare bug, which led to a segfault, if NaN is contained in input data. #1010
   - Featurizer: fix reshaping of AnglesFeature. #1018. Thanks @RobertArbon

- plots: Fix drawing into existing figures for network plots. #1020


2.2.7 (10-21-16)
----------------

**New features**:

- coordinates:
   - for lag < chunksize improved speed (50%) for TICA. #960
   - new config variable "coordinates_check_output" to test for "NaN" and "inf" values in
     iterator output for every chunk. The option is disabled by default. It gives insight
     during debugging where faulty values are introduced into the pipeline. #967


**Fixes**:

- coordinates:
   - save_trajs, frames_from_files: fix input indices checking. #958
   - FeatureReader: fix random access iterator unitcell_lengths scaling.
     This lead to an error in conjunction with distance calculations, where
     frames are collected in a random access pattern. #968
- msm: low-level api removed (use msmtools for now, if you really need it). #550

2.2.6 (9-23-16)
---------------

**Fixes**:

- msm: restored old behaviour of updating MSM parameters (only update if not set yet).
  Note that this bug was introduced in 2.2.4 and leads to strange bugs, eg. if the MSM estimator
  is passed to the Chapman Kolmogorov validator, the reversible property got overwritten.
- coordinates/TICA: Cast the output of the transformation to float. Used to be double. #941
- coordinates/TICA: fixed a VisibleDeprecationWarning. #941. Thanks @stefdoerr

2.2.5 (9-21-16)
---------------

**Fixes**:

- msm: fixed setting of 'reversible' attribute. #935

2.2.4 (9-20-16)
---------------

**New features**:

- plots: network plots can now be plotted using a given Axes object.
- thermo: TRAM supports the new parameter equilibrium which triggers a TRAMMBAR estimation.
- thermo: the model_active_set and msm_active_set attributes in estimated MEMMs is deprecated; every
  MSM in models now contains its own active_set.
- thermo: WHAM and MBAR estimations return MultiThermModel objects; return of MEMMs is reserved for
  TRAM/TRAMMBAR/DTRAM estimations.

**Fixes**:

- coordinates: MiniBatchKmeans with MD-data is now memory efficient
  and successfully converges. It used to only one batch during iteration. #887 #890
- coordinates: source and load function accept mdtraj.Trajectory objects to extract topology. #922. Thanks @jeiros
- base: fix progress bars for modern joblib versions.
- plots: fix regression in plot_markov_model with newer NumPy versions #907. Thanks @ghoti687.
- estimation: for n_jobs=1 no multi-processing is used.
- msm: scale transition path times by time unit of MSM object in order to get
  physical time scales. #929

2.2.3 (7-28-16)
---------------

**New features**:

- thermo: added MBAR estimation

**Fixes**:

- coordinates: In case a configuration directory has not been created yet, the LRU cache
  of the TrajInfo database was failed to be created. #882


2.2.2 (7-14-16)
---------------

**New features**:

- coordinates: SQLite backend for trajectory info data. This enables fast access to this data
  on parallel filesystems where multiple processes are writing to the database. This greatly
  speeds ups reader construction and enables fast random access for formats which usually do not
  support it. #798
- plots: new optional parameter **arrow_label_size** for network plotting functions to use a custom
  font size for the arrow labels; the default state and arrow label sizes are now determined by the
  matplotlib default. #858
- coordinates: save_trajs takes optional parameter "image_molecules" to correct for broken
  molecules across periodic boundary conditions. #841

**Fixes**:

- coordinates: set chunksize correctly. #846
- coordinates: For angle features it was possible to use both cossin=True and deg=True, which
  makes not sense. #857
- coordinates: fixed a memory error in kmeans clustering which affected large data sets (>=64GB). #839
- base: fixed a bug in ProgressReporter (_progress_force_finish in stack trace). #869
- docs: fixed a lot of docstrings for inherited classes both in coordinates and msm package.


2.2.1 (6-21-16)
---------------

**Fixes**:

- clustering: fixed serious bug in **minRMSD** distance calculation, which led to
  lots of empty clusters. The bug was introduced in version 2.1. If you used
  this metric, please re-assign your trajectories. #825
- clustering: fixed KMeans with minRMSD metric. #814
- thermo: made estimate_umbrella_sampling more robust w.r.t. input and fixed doumentation. #812 #827
- msm: low-level api usage deprecation warnings only show up when actually used.

2.2 (5-17-16)
-------------

**New features**:

- thermo: added TRAM estimation.
- thermo: added plotting feature for implied timescales.
- thermo: added Jupyter notebook examples: :ref:`ref-notebooks`.
- thermo: show convergence progress during estimation.

**Fixes**:

- clustering: fix parallel cluster assignment with minRMSD metric.
- base: during estimation the model was accessed in an inappropriate way,
  which led to the crash "AttributeError: object has no attribute '_model'" #764.
- coordinates.io: fixed a bug when trying to pyemma.coordinates.load certain MD formats.
  The iterator could have returned None in some cases #790.
- coordiantes.save_traj(s): use new backend introduced in 2.1, speed up for non random
  accessible trajectory formats like XTC. Avoids reading trajectory info for files not
  being indexed by the input mapping. Fixes #788.


2.1.1 (4-18-2016)
-----------------
Service release. Fixes some

**New features**:

- clustering: parallelized clustering assignment. Especially useful for expensive to
  compute metrics like minimum RMSD. Clustering objects now a **n_jobs** attribute
  to set the desired number of threads. For a high job number one should use a
  considerable high chunk size as well.

**Fixes**:

- In parallel environments (clusters with shared filesystem) there will be no
  crashes due to the config module, which tried to write files in users home
  directory. Config files are optional by now.


2.1 (3-29-2016)
---------------

**New features**:

- thermo package: calculate thermodynamic and kinetic quantities from multi-ensemble data

  - Added estimators (WHAM, DTRAM) for multi-ensemble MD data.
  - Added API functions to handle umbrella sampling and multi-temperature MD data.

- msm/hmsm:

  - Maximum likelihood estimation can deal with disconnected hidden transition
    matrices. The desired connectivity is selected only at the end of the
    estimation (optionally), or a posteriori.
  - Much more robust estimation of initial Hidden Markov model.
  - Added option stationary that controls whether input data is assumed to be
    sampled from the stationary distribution (and then the initial HMM
    distribution is taken as the stationary distribution of the hidden
    transition matrix), or not (then it's independently estimated using the EM
    standard approach). Default: stationary=False. This changes the default
    behaviour w.r.t. the previous version, but in a good way: Now the
    maximum-likelihood estimator always converges. Unfortunately that also
    means it is much slower compared to previous versions which stopped
    without proper convergence.
  - Hidden connectivity: By default delivers a HMM with the full hidden
    transition matrix, that may be disconnected. This changes the default
    behaviour w.r.t. the previous version. Set connectivity='largest' or
    connectivity='populous' to focus the model on the largest or most populous
    connected set of hidden states
  - Provides a way to measure connectivity in HMM transition matrices: A
    transition only counts as real if the hidden count matrix element is
    larger than mincount_connectivity (by default 1 over the number of hidden
    states). This seems to be a much more robust metric of real connectivity
    than MSM count matrix connectivity.
  - Observable set: If HMMs are used for MSM coarse-graining, the MSM active
    set will become the observed set (as before). If a HMM is estimated
    directly, by default will focus on the nonempty set (states with nonzero
    counts in the lagged trajectories). Optionally can also use the full set
    labels - in this case no indexing or relabelling with respect to the
    original clustered data is needed.
  - Hidden Markov Model provides estimator results (Viterbi hidden
    trajectories, convergence information, hidden count matrix). Fixes #528
  - BayesianHMSM object now accepts Dirichlet priors for transition matrix and
    initial distribution. Fixes #640 (general, not only for HMMs) by allowing
    estimates at individual lag times to fail in an ImpliedTimescales run
    (reported as Warnings).

- coordinates:
    - Completely re-designed class hierachy (user-code/API unaffected).
    - Added trajectory info cache to avoid re-computing lengths, dimensions and
      byte offsets of data sets.
    - Random access strategies supported (eg. via slices).
    - FeatureReader supports random access for XTC and TRR (in conjunction with mdtraj-1.6).
    - Re-design API to support scikit-learn interface (fit, transform).
    - Pipeline elements (former Transformer class) now uses iterator pattern to
      obtain data and therefore supports now pipeline trees.
    - pipeline elements support writing their output to csv files.
    - TICA/PCA uses covartools to estimate covariance matrices:
        + This now saves one pass over the data set.
        + Supports sparsification data on the fly.

**Fixes**:

- HMM Chapman Kolmogorov test for large datasets #636.
- Progressbars now auto-hide, when work is done.


2.0.4 (2-9-2016)
----------------
Patch release to address DeprecationWarning flood in conjunction with Jupyther notebook.

2.0.3 (1-29-2016)
-----------------

**New features**:

- msm: added keyword "count_mode" to estimate_markov_model, to specify the way
  of counting during creation of a count matrix. It defaults to the same behaviour
  like prior versions (sliding window). New options:

  - 'effective': Uses an estimate of the transition counts that are
     statistically uncorrelated. Recommended when used with a Bayesian MSM.
  - 'sample': A trajectory of length T will have T/tau counts at time indices
     0 -> tau, tau -> 2 tau, ..., T/tau - 1 -> T

- msm: added possibility to constrain the stationary distribution for BayesianMSM
- coordinates: added "periodic" keyword to features in Featurizer to indicate a
  unit cell with periodic boundary conditions.
- coordinates: added "count_contacts" keyword to Featurizer.add_contacts() method
  to count formed contacts instead of dimension of all possible contacts.
- logging: pyemma.log file will be rotated after reaching a size of 1 MB

**Fixes**:

- logging: do not replace existing loggers anymore. Use hierarchical logging (all loggers
  "derive" from 'pyemma' logger. So log levels etc. can be manipulated by changing this
  new 'pyemma' root logger.
- some deprecation warnings have been fixed (IPython and Python-3.5 related).

2.0.2 (11-9-2015)
-----------------

**New features**:

- coordinates: added Sparsifier, which detects constant features in data stream
  and removes them for further processing.
- coordinates: cache lengths of NumPy arrays
- coordinates: clustering.interface new methods index_clusters and sample_indexes_by_cluster
- coordinates: featurizer.add_contacts has new threshold value of .3 nm
- coordinates: featurizer.pairs gets opt arg excluded_neighbors (default (=0) is unchanged)
- coordinates: featurizer.describe uses resSeq instead of residue.index
- plots: network plots gets new arg state_labels, arg state_colors extended, textkwargs added
- plots: timescale plot accepts different units for x,y axes
- logging: full-feature access to Python logging system (edit logging.yml in .pyemma dir)

**Fixes**:

- Upon import no deprecation warning (about acf function) is shown.
- coordinates: chunksize attribute moved to readers (no consequence for user-scripts)
- coordinates: fixed bug in parallel evaluation of Estimators, when they have active loggers.
- documentation fixes

2.0.1 (9-3-2015)
----------------
Urgent bug fix: reading other formats than XTC was not possible in coordinates
pipeline. This bug has been introduced into 2.0, prior versions were not affected.

2.0 (9-1-2015)
--------------
2.0 is a major release offering several new features and a major internal
reorganization of the code.

**New features**:

- coordinates: Featurizer new features: ResidueMinDistanceFeature and GroupMinDistanceFeature.
- coordinates: PCA and TICA use a default variance cutoff of 95%.
- coordinates: TICA is scaled to produce a kinetic map by default.
- coordinates: TICA eigenvalues can be used to calculate timescales.
- coordinates: new MiniBatchKmeans implementation.
- coordinates: Early termination of pipeline possible (eg. max_clusters reached).
- coordinates: random access of input through pipeline via indices.
- msm: Estimator for Bayesian Markov state models.
- msm: MSMs can be systematically coarse-grained to few-state models
- msm: Estimators for discrete Hidden Markov Models (HMMs) and Bayesian Hidden Markov models (BHMMs).
- msm: SampledModels, e.g. generated from BayesianMSM or BayesianHMM allow statistics
  (means, variances, confidence intervals) to be computed for all properties of MSMs and HMMs.
- msm: Generalized Chapman-Kolmogorov test for both MSM and HMM models
- plots: plotting functions for Chapman-Kolmogorov tests and 2D free energy surfaces.
- plots: more flexible network plots.

**Documentation**:

- One new application-based ipython notebooks and three new methodological ipython notebooks
  are provided. All Notebooks and most of the data are provided for download at pyemma.org.
- Many improvements in API documentation.

**Code architecture**:

- Object structure is more clear, general and extensible. We have three main
  class types: Estimators, Transformers and Models. Estimators (e.g. MaximumLikelihoodMSM)
  read data and produce a Transformer or a Model. Transformers (e.g. TICA) can be employed in
  order to transform input data into output data (e.g. dimension reduction). Models
  (e.g. MSM) can be analyzed in order to compute molecular quantities of interest, such
  as equilibrium probabilities or transition rates.
- Estimators and Transformers have basic compatibility with scikit-learn objects.
- Code for low-level msm functions (msm.analysis, msm.estimation, msm.generation, msm.flux) has
  been relocated to the subsidiary package msmtools (github.com/markovmodel/msmtools). msmtools is
  part of the PyEMMA distribution but can be separately installed without depending on
  PyEMMA in order to facilitate further method development.
- Removed deprecated functions from 1.1 that were kept during 1.2


1.2.2 (7-27-2015)
-----------------
- msm estimation: new fast transition matrix sampler
- msm estimation: new feature "auto-sparse": automatically decide which datatype
  to use for transition matrix estimation.
- coordinates package: kinetic map feature for TICA (arXiv:1506.06259 [physics.comp-ph])
- coordinates package: better examples for API functions.
- coordinates package: cluster assignment bugfix in parallel environments (OpenMP).
- coordinates package: added cos/sin transformations for angle based features to
  featurizer. This is recommended for PCA/TICA transformations.
- coordinates package: added minimum RMSD feature to featurizer.
- coordinates package: Regular space clustering terminates early now, when it reaches
  max_clusters cutoff.
- plots package: use Fruchterman Reingold spring algorithm to calculate positions
  in network plots.
- ipython notebooks: new real-world examples, which show the complete workflow
- general: made all example codes in documentation work.


1.2.1 (5-28-2015)
-----------------
- general: Time consuming algorithms now display progressbars (optional).
- general: removed scikit-learn dependency (due to new kmeans impl. Thanks @clonker)
- coordinates package: new and faster implementation of Kmeans (10x faster than scikit-learn).
- coordinates package: allow metrics to be passed to cluster algorithms.
- coordinates package: cache trajectory lengths by default
                       (uncached led to 1 pass of reading for non indexed (XTC) formats).
                       This avoids re-reading e.g XTC files to determine their lengths.
- coordinates package: enable passing chunk size to readers and pipelines in API.
- coordinates package: assign_to_centers now allows all supported file formats as centers input.
- coordinates package: save_traj(s) now handles stride parameter.
- coordinates package: save_traj    now accepts also lists of files as an input
  In this case, an extra parameter topfile has to be parsed as well.
- plots package: added functions to plot flux and msm models.
- Bugfixes:

   - [msm.MSM.pcca]: coarse-grained transition matrix corrected
   - [msm.generation]: stopping states option fixed
   - [coordinates.NumPyReader]: during gathering of shapes of all files, none of them were closed.

1.2 (4-14-2015)
---------------
1.2 is a major new release which offers a load of new and useful functionalities
for coordinate loading, data processing and Markov model estimation and analysis.
In a few places we had to change existing API functions, but we encourage
everyone to update to 1.2.

- coordinate package: featurizer can be constructed separately
- coordinate package: new functions for loading data and creating file readers
  for large trajectories
- coordinate package: all clustering functions were renamed
  (e.g.: kmeans -> cluster_kmeans). Old function names do still work, but are deprecated
- coordinate package: new pipeline() function for generic data processing pipelines.
  Using pipelines you can go from data loading, over transformation via TICA or PCA,
  to clustered data all via stream processing. This avoids having to load large
  datasets into memory.
- msm package: markov_model() function creates a MSM object that offers a lot
  of analysis functions such as spectral analysis, mean first passage times,
  pcca, calculation of experimental observables, etc.
- msm package: estimate_markov_model() function creates a EstimatedMSM object
  from data. Offers all functionalities of MSM plus additional functions related
  to trajectories, such as drawing representative smaples for MSM states
- msm package: Chapman-Kolmogorow test and implied timescales calculation are more robust
- msm package: cktest() and tpt() functions now accept MSM objects as inputs
- various bug fixes

1.1.2 (3-18-2015)
-----------------

- PCCA++ now produces correct memberships (fixes a problem from nonorthonormal eigenvectors)
- Improved Coordinates API documentation (Examples, examples, EXAMPLES)
