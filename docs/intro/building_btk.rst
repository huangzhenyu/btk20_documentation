Installing BTK2.0
===========================

Downloading BTK2.0
-----------------------------

The latest BTK is available on `github's BTK project`_.

You can download the BTK 2.0 source archive from

``https://github.com/kkumatani/distant_speech_recognition/archive/master.zip``

or clone it with `Git`_:

.. sourcecode:: bash

    $ git clone https://github.com/kkumatani/distant_speech_recognition.git

.. _Git: https://git-scm.com/

Building BTK2.0
----------------------
Assuming you download the BTK 2.0 in
${your_btk_git_repository_directory}, you will just need to build it
with `CMake`_.

.. sourcecode:: bash

    $ cd ${your_btk_git_repository_directory}/btk20_src
    $ mkdir build
    $ cd build
    $ cmake -DCMAKE_INSTALL_PREFIX=$HOME/local .. && make install

Importing BTK2.0
------------------------

.. sourcecode:: bash

    $ export PYTHONPATH=${HOME}/local/lib/python2.7/site-packages:$PYTHONPATH

.. note::
       The example above assumes that python 2.7 is used by default. In the
       case that you use a different version of python, you will need
       to export PYTHONPATH accordingly.


If everything goes well, you should be able to import the BTK20 module on the Python terminal as follows. 

.. sourcecode:: bash

    $ python
     Python 2.7.10 (default, Oct 23 2015, 19:19:21)
     [GCC 4.2.1 Compatible Apple LLVM 7.0.0 (clang-700.0.59.5)] on darwin
     Type "help", "copyright", "credits" or "license" for more information.
    >>> import btk20

.. _github's BTK project: https://github.com/kkumatani/distant_speech_recognition
.. _CMake: https://cmake.org/
