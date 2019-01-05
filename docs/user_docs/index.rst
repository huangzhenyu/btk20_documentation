User Guides
======

These guides will help walk you through the usage of beamforming
toolkit 2.0 (BTK2.0).

In the BTK, there are two important design concepts: 

* *Feature pointer object* that makes easy to implement a chain of signal processors, and

* *Iterator* of the feature pointer object for real-time processing. 

In many cases, we would need to propagate the output of one audio processor to another. In BTK, a chain of speech processing can be readily implemented by cascading the *feature pointer object* to another. In the following sections, we will see example scripts. 

Moreover, in many speech applications, a low system latency is preferred. For example, a processing delay should not be noticeable in a video conference system. Otherwise, users will find a strange lag between talker's lip movement and voice. The low latency is typically achieved by using block-wise processing: processing a small chunk of the incoming signal in real time. For that, the BTK provides the *iterator* method, the *next()* method at C++  level and *Python iterator*.

.. toctree::

    feature_extraction
    subband_processing
    binaural_processing
    beamforming
    array_post_filtering
    dereverberation
    echo_cancellation
    speaker_tracking
