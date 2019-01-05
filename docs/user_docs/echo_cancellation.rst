.. _sec-aec:

Acoustic Echo Cancellation
===========================


Kalman Filtering AEC
--------------------

Acoustic echo cancellation (AEC) techniques themselves are well-developed; see [21] [22] for an overview and practical algorithms. The only research theme would be how to efficiently combine AEC and multi-channel processing [23]. Generally speaking, performing AEC before beamforming provides better speech enhancement performance at the expense of computational complexity. The BTK provides the NLMS AEC [21] [22]. The BTK also implements the subband AEC with Kalman filters [24] [25] rather than the full band AEC [26].

To use the AEC, you first need to import the AEC Python module.  After that, you set those subband features to an AEC object. For example, you can use the AEC with information filtering as follows.
The sensitivity on recognition performance with respect to the information filter?s parameters was investigated in [23] [24].  As described above, you can reconstruct the echo-suppressed signal through the synthesis filter bank instantiated with the AEC object as

NLMS AEC
--------
