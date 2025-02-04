Installation
============

Requirements
------------
PyCUTEst requires the following software to be installed:

* Python 2.7 or Python 3 (http://www.python.org/)
* CUTEst (see below)

**Please Note:** Currently PyCUTEst only supports Mac and Linux. For Windows 10 (or later), PyCUTEst can be used through the `Windows Subsystem for Linux <https://docs.microsoft.com/en-us/windows/wsl/>`_, following the Linux installation instructions.

Installing CUTEst
-----------------
Note that CUTEst must be installed in order for PyCUTEst to work.

Installing CUTEst on Linux
^^^^^^^^^^^^^^^^^^^^^^^^^^
These instructions do not include installation of the MATLAB interface. You will need to install four packages: `archdefs <https://github.com/ralna/ARCHDefs>`_, `SIFDecode <https://github.com/ralna/SIFDecode>`_, `CUTEst <https://github.com/ralna/CUTEst>`_ and `MASTSIF <https://bitbucket.org/optrove/sif>`_. To keep things simple, install all four packages in the same directory:

 .. code-block:: bash

    $ mkdir cutest
    $ cd cutest
    $ git clone https://github.com/ralna/ARCHDefs ./archdefs
    $ git clone https://github.com/ralna/SIFDecode ./sifdecode
    $ git clone https://github.com/ralna/CUTEst ./cutest
    $ git clone https://bitbucket.org/optrove/sif ./mastsif

Note that :code:`mastsif` contains all the test problem definitions and
is therefore quite large. If you're short on space you may want to copy
only the ``*.SIF`` files for the problems you wish to test on.

Next set the following environment variables in your :code:`~/.bashrc` to point to the installation directories used above:

 .. code-block:: bash

    # CUTEst
    export ARCHDEFS=/path/to/cutest/archdefs/
    export SIFDECODE=/path/to/cutest/sifdecode/
    export MASTSIF=/path/to/cutest/mastsif/
    export CUTEST=/path/to/cutest/cutest/
    export MYARCH="pc64.lnx.gfo"

Now we are ready to install CUTEst in double precision (requires :code:`gfortran` and :code:`gcc`):

 .. code-block:: bash

    $ source ~/.bashrc # load above environment variables
    $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/jfowkes/pycutest/master/.install_cutest.sh)"

And CUTEst should run from here. To test that the installation works, issue the commands:

 .. code-block:: bash

    $ cd $SIFDECODE/src ; make -f $SIFDECODE/makefiles/$MYARCH test
    $ cd $CUTEST/src ; make -f $CUTEST/makefiles/$MYARCH test

**Please Note:** *currently PyCUTEst only supports gfortran and uses the default version on your path (as returned by* :code:`gfortran -v` *). Please ensure this is the same version that you install CUTEst with above otherwise you may experience segmentation faults.*

Installing CUTEst on Mac
^^^^^^^^^^^^^^^^^^^^^^^^
Install CUTEst using Homebrew as detailed below (installing CUTEst manually on Mac is not supported). First it is important to ensure that you have the latest version of Xcode Command Line Tools installed (or the latest version of Xcode), please ensure this is the case by following `this guide <https://mac.install.guide/commandlinetools/index.html>`_. Now install the Homebrew package manager:

 .. code-block:: bash

    $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

Then you can easily install CUTEst:

 .. code-block:: bash

    $ brew tap optimizers/cutest
    $ brew install cutest --without-single --with-matlab # if using Matlab interface
    $ brew install mastsif  # if you want all the test problems
    $ for f in "archdefs" "mastsif" "sifdecode" "cutest"; do \
    $   echo ". $(brew --prefix $f)/$f.bashrc" >> ~/.bashrc; \
    $ done

**Anaconda Users:** *please ensure that* :code:`~/.bashrc` *is sourced in your conda environment (you can do this with the command* :code:`source ~/.bashrc` *) otherwise you may encounter errors using PyCUTEst.*

**Please Note:** *you may see warnings such as* :code:`ld: warning: object file (RANGE.o) was built for newer macOS version (11.5) than being linked (10.15)` *. To suppress these warnings please set the environment variable* :code:`MACOSX_DEPLOYMENT_TARGET` *to your current macOS version (e.g.* :code:`export MACOSX_DEPLOYMENT_TARGET=11.5` *in this example, you can make this permanent by adding it to your* :code:`~/.bashrc` *file).*

CUTEst Docker Container
^^^^^^^^^^^^^^^^^^^^^^^
CUTEst can be installed into its own Docker container. Create a file named :code:`Dockerfile` with the following contents:

 .. code-block:: docker

    FROM continuumio/miniconda3

    WORKDIR /cutest
    RUN apt update
    RUN apt install -y build-essential git gfortran
    RUN git clone https://github.com/ralna/ARCHDefs ./archdefs
    RUN git clone https://github.com/ralna/SIFDecode ./sifdecode
    RUN git clone https://github.com/ralna/CUTEst ./cutest
    RUN git clone https://bitbucket.org/optrove/sif ./mastsif

    ENV ARCHDEFS /cutest/archdefs/
    ENV SIFDECODE /cutest/sifdecode/
    ENV MASTSIF /cutest/mastsif/
    ENV CUTEST /cutest/cutest/
    ENV MYARCH "pc64.lnx.gfo"

    RUN wget https://raw.githubusercontent.com/jfowkes/pycutest/master/.install_cutest.sh
    RUN chmod +x .install_cutest.sh
    RUN ./.install_cutest.sh

    ENTRYPOINT tail -f /dev/null

You can then build, launch, and login to the container in the usual way:

 .. code-block:: bash

    $ docker build -t cutest .               # build the container
    $ docker run --name mycutest -dt cutest  # launch the container
    $ docker exec -it mycutest /bin/bash     # login to the container

Please see the `docker docs <https://docs.docker.com/get-started/>`_ for more details on using docker containers.

Installing PyCUTEst
-------------------
**Please Note:** *CUTEst must be installed in order for PyCUTEst to work (see* `Installing CUTEst`_ *).*

For easy installation, use `pip <http://www.pip-installer.org/>`_:

 .. code-block:: bash

    $ pip install pycutest
    $ pip install --upgrade pycutest  # if existing installation on system

With this, PyCUTEst will create a cache folder inside your current working directory.
This cache directory is used for compiled problems. You can set a custom cache directory
with these commands:

 .. code-block:: bash

    $ mkdir pycutest_cache
    $ export PYCUTEST_CACHE="/path/to/pycutest_cache"

Note that you can uninstall PyCUTEst as follows:

 .. code-block:: bash

    $ pip uninstall pycutest
