.. _sec-aec:

Acoustic Echo Cancellation
===========================

Acoustic echo cancellation (AEC) techniques themselves are well-developed; see [HAY02]_ [TAS09]_ [HS04]_ for an overview and practical algorithms. However, there are still research activities such as an efficient combination method of AEC and multi-channel processing [HNK05]_  [TAS09]_ and residual echo suppression due to the non-linear distortion [FF18]_ [CSVH18]_. Generally speaking, the use of the AEC before beamforming provides better speech enhancement performance at the expense of computational complexity. The `BTK`_ provides the NLMS AEC [HAY02]_ [TAS09]_ [HS04]_. The `BTK`_ also implements the subband AEC with Kalman filters [MCKR+11]_ [MKR11]_ rather than the full band AEC [EV06]_ [FF18]_.
    
:numref:`test_oversampled_dft_filter.py` shows an example AEC script, `unit_test/test_subband_aec.py`_ in the Git repository. As shown in :numref:`test_oversampled_dft_filter.py`, we first need to import the AEC Python module. We then set the analysis filterbank feature pointer object to an AEC object. The AEC feature instance is finally given to the  synthesis filter bank in order to reconstruct the echo-suppressed signal.

Kalman Filtering AEC
--------------------

By default, the sample script described in :numref:`test_oversampled_dft_filter.py` performs Kalman filtering-based AEC in the subband domain. To try it, run `unit_test/test_subband_aec.py`_ in the Git repository as 

.. sourcecode:: bash

   $ cd ${your_btk_git_repository}/unit_test
   $ python test_subband_aec.py  \
     -i data/speech_and_reverb_lt.wav \
     -p data/lt.wav \
     -o out/kf_aec_output.wav

Notice that the script uses the Nyquist(M) filter bank with M=256, m=4 and r=1, by default. You can see how well the Kalman filtering-based AEC can suppress the interfering music signal by comparing the input file (data/speech_and_reverb_lt.wav) with the output file (out/kf_aec_output.wav).

As specified from line 167 to to 177 in :numref:`test_oversampled_dft_filter.py`, Kalman filtering-based AEC has a couple of hyper-parameters. The sensitivity of the the Kalman filtering AEC on recognition performance was investigated in [MCKR+11]_ [MKR11]_.  

NLMS AEC
--------

The BTK also provides the simplest AEC method based on the normalized least mean square (NMLS) algorithm as a reference. To do it, we just need to specify the NLMS option in a JSON file as

.. code-block:: python

 	{ "type":"nlms", 
           "energy_threshold":100,
           "delta":100,
           "epsilon":10E-4}
                            
Then, we feed the JSON file to `unit_test/test_subband_aec.py`_:

.. sourcecode:: bash

   $ cd ${your_btk_git_repository}/unit_test
   $ python test_subband_aec.py  \
     -c nlms.json \ 
     -i data/speech_and_reverb_lt.wav \
     -p data/lt.wav \
     -o out/kf_aec_output.wav

In practice, the NLMS-based AEC methods need additional components to avoid diverging a filter estimate such as accurate double-talk detection or meticulous step size parameter setting. Kalman filters will not require such heuristic methods since the step size parameter can be adjusted in a statistical framework; see Chapter 10 in [HAY02]_ for the details of the difference.

.. literalinclude:: examples/test_subband_aec.py
    :language: python
    :linenos:
    :caption: unit_test/test_subband_aec.py
    :name: test_subband_aec.py
    
.. _BTK: https://distantspeechrecognition.sourceforge.io/index.htm
.. _unit_test/test_subband_aec.py: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/test_subband_aec.py
