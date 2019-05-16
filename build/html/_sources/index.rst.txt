.. clas12softwarePage documentation master file, created by
   sphinx-quickstart on Thu Jul 26 10:01:44 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. |br| raw:: html

   <br>

|br|

The CLAS12 software packages are distributed using docker images.


Quickstart: Docker
==================

Use the following command to run the clas12 software image::

 docker run -it --rm jeffersonlab/clas12simulations:iprod bash

|br|


Quickstart: Submit Jobs to OSG
==============================

Login to scosg16 and copy the steering card template somewhere you can edit::

 ssh scosg16.jlab.org
 cp /group/clas12/offsite-simulations/scard.txt .
 EDIT FILE

Run submit.py with -s option to submit jobs to OSG::

 /group/clas12/offsite-simulations/submit.py -s scard.txt


|br|



Container content
=================

The clas12simulations docker image contains:

- various :ref:`generators <generators>` 
- gemc with the clas12 geometry
- CLARA
- Coatjava
- the *CLAS12_BIN*, *CLAS12_LIB*, *CLAS12_INC* dirs and environment variables


For the packages version check the :ref:`tags <tags>` page.


|br|


HowTos
======

.. toctree::
	:maxdepth: 1

	examples


Tags
====

.. toctree::
	:maxdepth: 1

	tags/tags




Troubleshooting
---------------

- `Linux: Solving Docker permission denied while trying to connect to the Docker daemon socket <https://techoverflow.net/2017/03/01/solving-docker-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket/>`_

|



