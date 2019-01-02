Prerequisites for Installation
============================


Dependencies
-------------------------

To build the BTK, we need to install the following software packages:

* `CMake`_: Cross-platform family of tools designed to build, test and package software

* `SWIG`_: Simplified wrapper and interface generator

* `Python Dev.`_: Python development with header and objects

* `NumPy Dev.`_: Matlab like extension to python

* `GSL`_: GNU Scientific Library

* `Libsndfile`_: C library for audio file IO

.. _CMake: https://cmake.org/
.. _SWIG: http://www.swig.org/
.. _Python Dev.: https://www.python.org/
.. _Numpy Dev.: http://www.numpy.org/
.. _GSL: https://www.gnu.org/software/gsl/
.. _libsndfile: http://www.mega-nerd.com/libsndfile/

Optional Software
---------------------------

The following software package are optional.

* `CUDA`_: GPU-accelerated libraries

* `libsamplerate`_: Sample rate converter for audio

* `FFTW3, FFTW-float, FFTW-thread`_: optimized FFT computation

* `ATLAS`_: Automatically tuned algebra software with an efficient BLAS implementation.

.. _CUDA: https://developer.nvidia.com/cuda-toolkit
.. _libsamplerate: http://www.mega-nerd.com/SRC/
.. _FFTW3, FFTW-float, FFTW-thread: http://www.fftw.org/
.. _ATLAS: http://math-atlas.sourceforge.net/

Installing on OS X
------------------

Installing packages with Homebrew
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We'd recommend using `Homebrew`_ for making package installation easy.  To install `Homebrew`_, run the following command on the terminal. 

.. sourcecode:: bash

    $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Once it is installed successfully, intall the packages necessary for BTK2.0 as follows. 

.. sourcecode:: bash

    $ brew install cmake
    $ brew install swig
    $ brew install python@2
    $ brew install numpy
    $ brew install gsl
    $ brew install libsndfile

Now, you are ready to install BTK2.0. 

Installing on Linux
-------------------


.. _Homebrew: https://brew.sh/index
