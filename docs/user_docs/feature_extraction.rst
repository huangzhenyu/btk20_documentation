.. _sec-sfe:

Feature Extraction
===========================

This page shows the usage of the `BTK`_ for speech feature extraction. The example scripts below will also help you understand how to use the stream feature pointer object and iterator. 

Log Power Feature
--------------------------

Code shown in :numref:`log_power_extractor.py` performs log-power computation from an audio file. In that example, we first define the dependency between processors from line 17 to 25. At line 17, the script instantiates the audio sample feature pointer object, *SampleFeaturePtr*, for reading audio samples block-by-block. The *SampleFeaturePtr* instance, samplefe, is then cascaded into the *HammingFeaturePtr* object that performs Hamming windowing on a block of audio samples. After that, we pass the *HammingFeaturePtr* instance to the FFT processor to obtain a complex-valued DFT coefficient. Those frequency component will be transformed into the log domain through the   *SpectralPowerFeaturePtr* and  *LogFeaturePtr*. 

Actual execution is performed though the Python iterator at line 33; the iterator executes a chain of speech processing from Hamming windowing, FFT, power computation and log operation in order.

The final output, the log power vector, is stored in *log_vector* at line 33 and dumped into a file. 

.. literalinclude:: examples/log_power_extractor.py
    :language: python
    :linenos:
    :caption: unit_test/log_power_extractor.py
    :name: log_power_extractor.py


Mel-frequency cepstral coefficient (MFCC)
--------------------------------------------------------

In the same way as log power feature extraction, we can implement `MFCC`_ feature extraction. :numref:`mfcc_extractor.py` shows an example of MFCC computation. As it is clear in the code, we just need to insert more feature pointer objects to obtain the MFCC. 

.. literalinclude:: examples/mfcc_extractor.py
    :language: python
    :linenos:
    :caption: unit_test/mfcc_extractor.py
    :name: mfcc_extractor.py

Those scripts can be found in `btk20_src/unit_test`_ of the git repository.

.. _BTK: https://distantspeechrecognition.sourceforge.io/index.htm
.. _btk20_src/unit_test: https://github.com/kkumatani/distant_speech_recognition/tree/master/btk20_src/unit_test
.. _MFCC: https://en.wikipedia.org/wiki/Mel-frequency_cepstrum
