.. _sec-dereverb:

Dereverberation
===============

Several papers reported that reverberation effects would degrade far-field speech recognition accuracy [KDGH+16]_ [YN12]_. Dereverberation can be achieved through super-Gaussian beamforming [KMB12]_ or weighted prediction error (WPE) algorithm [YN12]_. The `BTK`_ implements the single channel and multi-channel WPE algorithm in the subband domain.

It is worth noting that there is another pure Python implementation of the WPE algorithm, `Nara WPE`_.  The main differences between `Nara WPE`_ and `BTK2.0`_ are:

* Easy use of a different multi-rate filter bank system in addition to the naive overlap/overadd method based on short term FFT, and 

* C++ implementation of the core WPE process that makes processing time faster than the pure Python implementation. 

We will go through the example code :numref:`test_subband_dereverberator.py` step-by-step.

Subband Weighted Prediction Error (WPE) Algorithm
--------------------------------------------------

The `"dereverberation" module`_ in the `BTK`_ provides the single channel and multi-channel WPE dereverberators. Notice that the filter estimation process is separated from the filtering process in the `BTK2.0`_. So, you can apply the dereverberation filter estimated with a small portion of data to the rest of a recording.

:numref:`test_subband_dereverberator.py` shows how to use the `"dereverberation" module`_ to run the WPE dereverberator. Depending the number of input audio files, the script switches the single channel dereverberator function *single_channel_wpe()* defined at line 27 to the *multi-channel versionmulti_channel_wpe()* declared at line 183.

Single channel WPE dereverberator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To taste the single channel WPE, run a `unit_test/test_subband_dereverberator.py`_:

.. sourcecode:: bash

   $ cd ${your_btk_git_repository}/unit_test
   $ python test_subband_dereverberator.py  \
     -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
     -o out/U1001_1M_16k_b16_c1.wav

This will generate the dereverbed output as out/U1001_1M_16k_b16_c1.wav.

You can configure several WPE parameters by feeding the JSON file: 

.. code-block:: python

	{"lower_num":0,
 	"upper_num":32,
 	"iterations_num":2, 
 	"load_db": -18.0,
 	"band_width":0.0,
 	"diagonal_bias":0.0001
    }

into the script as

.. sourcecode:: bash

   $ python test_subband_dereverberator.py  \
     -c your_wpe_pram.json \
     -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
     -o out/U1001_1M_16k_b16_c1.wav

.. tip:: 
 The reverberation suppression performance can be improved by increasing the WPE parameter, 'upper_num' (specified at line 279 in :numref:`test_subband_dereverberator.py`). This results in a longer filter length. Note that the use of more filter coefficients leads to more computation complexity. 
 
To build the single channel WPE processor,  `unit_test/test_subband_dereverberator.py`_ creates the feature pointer object graph: 

* *SampleFeaturePtr()*,

* *OverSampledDFTAnalysisBankPtr()*,

* *SingleChannelWPEDereverberationFeaturePtr()*, and

* *OverSampledDFTSynthesisBankPtr()*.

First, the script runs dereverberation filter estimation by calling *SingleChannelWPEDereverberationFeaturePtr.estimate_filter()* at line 73 in :numref:`test_subband_dereverberator.py`. After filter estimation, the python iterator is called to reconstruct the time signal (at line 84). The iterator runs the *next()* method of each feature pointer object back from *OverSampledDFTSynthesisBankPtr()* to *SampleFeaturePtr()*.
 
Multi-channel WPE dereverberator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The multi-channel WPE version can be tested by typing the following command:

.. sourcecode:: bash

   $ cd ${your_btk_git_repository}/unit_test
   $ python test_subband_dereverberator.py  \
     -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
        data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
        data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
        data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
     -o out/U1001_1M_16k_b16_c1.wav \
         out/U1001_1M_16k_b16_c2.wav \
         out/U1001_1M_16k_b16_c3.wav \
         out/U1001_1M_16k_b16_c4.wav \
      -b 26000 \
      -e 64000 

.. tip:: Notice that the command above will estimate the filter coefficients with the speech signal present from the 26000th frame to the 64000th frame in the input files. It is better not to use  silence audio data for the sake of computational efficiency. The silence data or weak signals will not lead to a good filter solution.   

.. warning:: Do not feed identical multiple audio files into the multi-channel WPE estimator. It will cause numerical instability. If the input signals are very similar, consider performing the single channel WPE algorithm on each channel independently. That will also speed up filter estimation. 

The multi-channel WPE dereverberator  will consist of 

* *Multiple SampleFeaturePtr()* instances,

* *Multiple OverSampledDFTAnalysisBankPtr()* instances,

* A *MultiChannelWPEDereverberationPtr()* instance,

* Multiple *MultiChannelWPEDereverberationFeaturePtr()* instances, and

* Multiple *OverSampledDFTSynthesisBankPtr()* instances.

In order to generate multiple output, the chain of the iterators are called for each channel at line 73. 

.. literalinclude:: examples/test_subband_dereverberator.py
    :language: python
    :linenos:
    :caption: unit_test/test_subband_dereverberator.py
    :name: test_subband_dereverberator.py


.. _BTK: https://distantspeechrecognition.sourceforge.io/index.htm

.. _BTK2.0: https://github.com/kkumatani/distant_speech_recognition

.. _Nara WPE: https://github.com/fgnt/nara_wpe

.. _"dereverberation" module: https://github.com/kkumatani/distant_speech_recognition/tree/master/btk20_src/dereverberation

.. _unit_test/test_subband_dereverberator.py: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/test_subband_dereverberator.py

.. _unit_test: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test
