.. _sec-bf:

Beamforming
===========================

Acoustic beamforming can strengthen a signal propagating from a particular direction or position and suppress interfering signals [VSR13]_ [WM09]_ [TRE02]_. Such beamformers can be quickly implemented with `BTK`_. 

:numref:`fig-bod` illustrates a dependency graph of the feature pointer objects. As shown in :numref:`fig-bod`, we create multiple spectral objects, *OverSampledDFTAnalysisBankPtr()*, from the multi-channel audio signal and then feed them into the beamformer object, *SubbandGSCPtr()*. The beamformed subband component will be transformed back into the time domain through the *OverSampledDFTSynthesisBankPtr()*. 

We can easily try a different beamforming algorithm by replacing the beamforming object or cascade various processing methods by inserting before/after the beamformer process. 

.. figure::  figs/beamforming-object-dependency.png
   :name:  fig-bod
   :align:   center

Object dependency for building a beamforming chain


You may sometimes want to adapt a beamformer to a specific acoustic environment. Adapting the beamformer with a few observations is cheaper than building multiple beamformers with a large amount of data. As you can imagine, there are many publications that claim to describe a new beamforming algorithm. 
In terms of a necessary amount of adaptation data in running time, those acoustic beamforming techniques can be classified into two types of algorithms:

* Online processing that outputs a processed signal on a sample-by-sample or block-by-block basis in real time, and 

* Batch processing that requires one utterance, typically 2 or more seconds, to capture enough statistics of the signal. 

:numref:`test_online_beamforming` shows a sample python script that runs online beamforming algorithms. Delay-and-sum (D&S), super-directive (SD), linear constrained minimum variance (LCMV),  generalized sidelobe canceller (GSC) recursive least squares (RLS) and GSC least mean-square (LMS) beamformers can fall into this class of algorithms. 

.. note::  In addition to array geometry information, the acoustic beamforming techniques will require prior knowledge: 1) the position of a target sound source or 2) speech activity indicator. In the examples below, such information is specified in a JSON configuration file. The methods to estimate such information will be described later. The use of prior knowledge indeed distinguishes acoustic beamforming from blind source separation techniques that can be unstable in the case that a limited amount of adaptation or training data is only available. 


.. _sec-ds-bf: 

Delay-and-sum (D&S) Beamforming
------------------------------------------------

The D&S beamformer is implemented as one of options in "`unit_test/test_online_beamforming`_".  To try D&S beamforming only, run that script in your Git repository as

.. sourcecode:: bash

   $ cd ${YOUR_GIT_REPOSITORY}/unit_test
   $ python test_online_beamforming.py \
          -c confs/ds.json \
          -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_ds.wav

This will generate a beamformed audio file as out/U1001_1M_ds.wav. 

.. _sec-sd-bf:

Super-Directive Beamforming
----------------------------------------

The super-directive beamforming [BS01]_ is optimum for the spherically isotropic (diffuse) noise field. It can be also viewed as a special case of minimum variance distortionless beamforming where the noise coherence matrix is fixed. 

SD beamforming can be done by giving the "`unit_test/confs/sd.json`_" JSON to the "`unit_test/test_online_beamforming`_" Python script as

.. sourcecode:: bash

   $ python test_online_beamforming.py \
          -c confs/sd.json \
          -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_sd.wav


Here, the JSON file for SD beamforming looks:

.. sourcecode:: bash

   $ cat confs/sd.json
   {"array_type":"linear",
    "microphone_positions":[[-113.0, 0.0, 2.0],
                         [  36.0, 0.0, 2.0],
                         [  76.0, 0.0, 2.0],
                         [ 113.0, 0.0, 2.0]],
    "target":{"positions":[[0.0, [-1.306379, null, null]]]},
    "beamformer":{"type":"super_directive", "diagonal_load":0.01}
   }


.. tip:: You can adjust white noise gain by changing an amount of diagonal loading, specified with ["beamformer"]["diagonal_load"] in the JSON file or at line 172 in :numref:`test_online_beamforming`. The smaller diagonal loading value indicates a sharper directivity but less white noise gain. 


LCMV beamforming
-----------------------------

Beamforming can also place null on a signal coming from a particular direction. It is sometimes called null-steering beamforming. The linear constrained minimum variance (LCMV) beamformer  can be viewed as a general form of delay-and-sum and null-steering beamformers. By specifying the direction of each sound source, the LCMV beamformer can strengthen or null out the source. 

The following command will do LCMV beamforming. 

.. sourcecode:: bash

   $ python test_online_beamforming.py \
         -c confs/lcmv_and_zelinski.json \
         -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_lcmvz.wav

Compare the input file with the output file to see that the LCMV beamformer can weaken the signal.


MVDR Beamforming
----------------------------

.. tip:: We would recommend you read Chapter 6 and 7 in [TRE02]_ if you don't do so.  Once you go through the book [TRE02]_, you will find many recent publications on adaptive beamforming and source separation techniques merely describe a variant of the methods explained in [TRE02]_. Many algorithms address how to estimate an optimal weight with a small amount of adaptation data based on the *second order statistics* (SOS).

Among hundreds of SOS-based beamforming, the BTK provides four representative types of adaptive beamforming with the second order statistics as follows.


.. _sec-gsc-rls-bf:

GSC recursive least squares (RLS) implementation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There was a great deal of attention paid to research on recursive weight update algorithms for adaptive filtering instead of batch update. The recursive implementation is suitable for real-time processing. 
The `BTK`_ provides a generalized sidelobe canceller recursive least squares (GSC-RLS) beamformer with quadratic constraints. 

You can test GSC-RLS beamforming by running "`unit_test/test_online_beamforming`_" with "`unit_test/confs/gscrls.json`_":

..  sourcecode:: bash

   $ python test_online_beamforming.py \
         -c confs/gscrls.json \
         -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_gscrls.wav


The GSC-RLS beamformer actually has hyper-parameters to be tuned for stabilizing active weight estimation. :numref:`hparam4gscrls` lists the hyper-parameters for GSC-RLS beamforming. 

.. table:: Hyperparameters for GSC-RLS beamforming class
   :widths: auto
   :name: hparam4gscrls

   +-----------------------+----------------------------------------------------------+
   | Hyperparameter        | What is this?                                            |
   +=======================+==========================================================+
   | beta                  | Forgetting factor to Smooth an power estimate            |
   +-----------------------+----------------------------------------------------------+
   | gamma                 | Step size factor to control a speed of weight estimation |
   +-----------------------+----------------------------------------------------------+
   | mu                    | Forgetting factor to forget observations in the past     |
   +-----------------------+----------------------------------------------------------+
   | init_diagonal_load    | Initial value for the power estimate                     |
   +-----------------------+----------------------------------------------------------+
   | regularization_param  | Parameter to regularize the large weight vector value    |
   +-----------------------+----------------------------------------------------------+
   | sil_thresh            | Silence power threshold used for determining whether the |
   |                       | weight vector is updated or not                          |
   +-----------------------+----------------------------------------------------------+
   | constraint_option     | Constraint option for weight estimation:                 |
   |                       | 1. Quadratic constraint,                                 |
   |                       | 2. Weight vector norm normalization                      |
   |                       | 3. Hybrid of two methods                                 |
   +-----------------------+----------------------------------------------------------+
   | alpha2                | Quadratic constraint so as to keep \|wa\|^2 <= alpha2    |
   +-----------------------+----------------------------------------------------------+
   | max_wa_l2norm         | Norm normalization parameter to limit the vector norm:   |
   |                       | \|wa\|^2 == max_wa_l2norm                                |
   +-----------------------+----------------------------------------------------------+
   | min_frames            | Start updating weights after "min_frames" observations   |
   +-----------------------+----------------------------------------------------------+
   | slowdown_after        | Decrease a step size after "slowdown_after" frames       |
   +-----------------------+----------------------------------------------------------+


.. _sec-gsc-lms-bf:

GSC least mean square (LMS) implementation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The least mean square (LMS) is a stochastic version of the steepest algorithm. The computational cost of the LMS beamformers would be less expensive than that of the RLS implementation due to no need of Kalman gain computation for the inverse of a sample spectral matrix. However, the convergence speed of the LMS beamformers will depend on the eigenvalue spread. The `BTK`_ implements the power normalized LMS (PN-LMS) beamformer configured in GSC. 

In the same way as described above, run "`unit_test/test_online_beamforming`_" but with a JSON configuration file "`unit_test/confs/gsclms.json`_":

.. sourcecode:: bash

   $ python test_online_beamforming.py \
        -c confs/gsclms.json \
         -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
        -o out/U1001_1M_gsclms.wav


The convergence performance of the LMS beamformer is also very sensitive to a rapid acoustic change. :numref:`hparam4gsclms` shows the hyper-parameters to control an adaptation speed, leakage degree and regularization term. 

.. table:: Hyperparameters for GSC-LMS beamforming class
   :widths: auto
   :name: hparam4gsclms

   +-----------------------+----------------------------------------------------------+
   | Hyperparameter        | What is this?                                            |
   +=======================+==========================================================+
   | beta                  | Forgetting factor to Smooth an power estimate            |
   +-----------------------+----------------------------------------------------------+
   | gamma                 | Step size factor to control a speed of weight estimation |
   +-----------------------+----------------------------------------------------------+
   | init_diagonal_load    | Initial value for the power estimate                     |
   +-----------------------+----------------------------------------------------------+
   | regularization_param  | Leak amount to avoid a large weight change               |
   +-----------------------+----------------------------------------------------------+
   | energy_floor          | Value to floor the small signal energy                   |
   +-----------------------+----------------------------------------------------------+
   | sil_thresh            | Silence power threshold used for determining whether the |
   |                       | weight vector is updated or not                          |
   +-----------------------+----------------------------------------------------------+
   | max_wa_l2norm         | Norm normalization parameter to limit the vector norm:   |
   |                       | \|wa\|^2 == max_wa_l2norm                                |
   +-----------------------+----------------------------------------------------------+
   | min_frames            | Start updating weights after "min_frames" observations   |
   +-----------------------+----------------------------------------------------------+
   | slowdown_after        | Decrease a step size after "slowdown_after" frames       |
   +-----------------------+----------------------------------------------------------+


Sample matrix inversion (SMI) implementation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Generalized Eigenvector  (GEV) implementation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[WH07]_

.. warning:: The best case scenario for conventional adaptive beamformers is the *static* acoustic environment where both target and noise source positions are fixed and located at different positions. Adaptive beamforming may degrade speech recognition accuracy in complex acoustic environments in the case that either a speaker or noise source moves or sound presence timing varies because of steering errors [SU96]_. 


Maximum Super-Gaussianity (SG) Beamforming
---------------------------------------------------------

In practice, these adaptive beamformers with the second-order statistics could suppress the desired signal as well as noise. 
The key technique for avoiding the *signal cancellation effect* would be 

* Using a different criterion for optimization [KMB12]_, 

* Locating a desired source position accurately or 

* Estimating the precise presence probability of a desired signal [HDH16]_. 

This section describes a beamforming technique with the maximum super-Gaussian criterion, which turned out to be robust against the reverberation and beam steering error.

Maximum Kurtosis Beamforming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[KMRG+08]_

Maximum Negentropy Beamforming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[KMRK+09]_

.. warning:: In order to achieve the best enhancement performance, the SMI-MVDR, GEV and maximum SG beamformers require a certain amount of data, around1 to 2 seconds [KMR11]_ [HKIK+18]_. 

.. literalinclude:: examples/test_online_beamforming.py
    :language: python
    :linenos:
    :caption: Script including online beamformers: unit_test/test_online_beamforming
    :name: test_online_beamforming

.. _sec-sphbf:

Spherical Beamforming
===========================
[ME04]_


Spherical Harmonics D&S Beamforming
---------------------------------------------------------

Spherical Harmonics SD Beamforming
---------------------------------------------------------


.. _BTK: https://distantspeechrecognition.sourceforge.io/index.htm
.. _unit_test/confs/ds.json: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/confs/ds.json
.. _unit_test/confs/sd.json: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/confs/sd.json
.. _unit_test/confs/lcmv_and_zelinski.json: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/confs/lcmv_and_zelinski.json
.. _unit_test/confs/gscrls.json: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/confs/gscrls.json
.. _unit_test/confs/gsclms.json: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/confs/gsclms.json
.. _unit_test/test_online_beamforming: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/test_online_beamforming.py

