.. _sec-apf:

Array Post-Filtering
===========================

MVDR beamforming does not provide the optimal solution in a sense of the minimum mean square error (MMSE). The MMSE can be achieved with the MVDR beamformer followed by array post-filtering [SBM01]_. In `BTK`_, it can be done by inserting the post-filter feature pointer object object between beamformer and synthesis filter bank objects in :numref:`fig-bod`. The `BTK`_ provides the following array post-filters. 


Zelinski Post-Filtering
-----------------------

The effect of the Zelinski post-filters is thoroughly investigated in [SBM01]_. SD beamforming and Zelinski post-filtering can be done with `unit_test/test_online_beamforming`_ in the `BTK`_ Git repository. 

.. sourcecode:: bash

   $ cd ${YOUR_GIT_REPOSITORY}/unit_test
   $ python test_online_beamforming.py \
          -c confs/sd_and_zelinski.json \
          -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
             data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_sdz.wav


This processes 4-channel input and store output as "out/U1001_1M_sdz.wav". 

McCowan  Post-Filtering
-----------------------

McCowan post-filtering can provide a more optimum solution for the diffuse noise field [MB03]_. You can run McCowan post-filtering as follows. 

.. sourcecode:: bash

   $ cd ${YOUR_GIT_REPOSITORY}/unit_test
   $ python test_online_beamforming.py \
         -c confs/sd_and_mccowan.json \
         -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_sdm.wav


Lefkimmiatis Post-Filtering
----------------------------

According to [LM07]_, McCowan's method is sub-optimum in the diffuse noise field. You can try out Lefkimmiatis post-filtering as follows. 

.. sourcecode:: bash

   $ cd ${YOUR_GIT_REPOSITORY}/unit_test
   $ python test_online_beamforming.py \
         -c confs/sd_and_lefkimmiatis.json \
         -i data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c1.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c2.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c3.wav \
            data/CMU/R1/M1005/KINECT/RAW/segmented/U1001_1M_16k_b16_c4.wav \
         -o out/U1001_1M_sdl.wav

.. tip:: As reported in [SKMC12]_, those post-filters may not be effective under a mismatched noise field assumption. 

.. _BTK: https://distantspeechrecognition.sourceforge.io/index.htm
.. _unit_test/test_online_beamforming: https://github.com/kkumatani/distant_speech_recognition/blob/master/btk20_src/unit_test/test_online_beamforming.py
