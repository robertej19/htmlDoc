.. |br| raw:: html

   <br>


.. _runningGEMCBatch:

Batch mode
==========

Use the following command to open a bash session on the container (the first time you run this it will also download it). You can also replace bash with tcsh::

 docker run -it --rm jeffersonlab/clas12simulations:iprod bash

This will open a bash session in the /jlab/workdir directory.

|br|

.. _runningGEMCMount:

Mounting your directories to the container
==========================================

The container will always start with the "pristine" image. In other words every work the the container filesystem will be lost when you exit docker.
You can use the option::

 -v /host/directory:/container/directory

to mount your local OS directories to be visible in docker. For example, to mount the "maximilian" home directory in a /max dir in the container::

 docker run -it --rm  -v /home/max:/jlab/work/max jeffersonlab/clas12simulations:iprod bash

*/jlab/work//max* will now point to maximilian home dir. You can save work here.

|br|

.. _runningGEMCWithGraphicBrowser:

Graphic mode (**browser**)
==========================

Use the following command to pass the 6080 port to noVnc so the container can be opened on a brower::

 docker run -it --rm -p 6080:6080 jeffersonlab/clas12simulations:iprod

Using your web brower open the page::

 http://localhost:6080

After clicking connect the linux desktop is shown with a running shell.|br| |br|


|br| |br|

.. note::

 I suggest to set the noVNC settings as follows:

 - Scaling mode: remote
 - Share mode active (this will ensure if you open another browser session, it will show the same instance of the container)
 - On the docker preferences try to make available as much memory as possible.


|br| |br|

You can stop the docker container at any time with CTRL-C

|br|

.. _runningGEMCWithGraphicVNC:

Graphic mode (**vnc**)
======================

Use the following command to pass the 5901 and the 6080 ports necessary to open the container with a vnc client::

 docker run -it --rm  -p 6080:6080 -p 5901:5901 jeffersonlab/clas12simulations:iprod

You can now open localhost:5901 with your vnc client.

|br|

.. _runningGEMCWithGraphicInteractive:

**Native interactive** mode (no opengl)
=======================================

On a mac, if you allow access from localhost with::

 1. Activate the option ‘Allow connections from network clients’ in XQuartz settings
    (Restart XQuartz (to activate the setting)
 2. xhost +127.0.0.1

Then you can run docker and use the local X server with::

 docker run -it --rm -e DISPLAY=docker.for.mac.localhost:0 jeffersonlab/clas12simulations:iprod bash

You can run gemc in batch mode this way, but still enjoy the ability to open windows on the local host.

The interactive mode is slower than the browser or the vnc mode. I suggest you only use interactive when you want
to visualize a subset of the CLAS12 detectors, not all of them.

|br|


.. _runningGEMCGenerator:

Using Generators
================

Run the container in batch mode (substitute the max's path with your home dir path)

 docker run -it --rm  -v /home/max:/jlab/work/max jeffersonlab/clas12simulations:iprod bash

Find below the ways to generate events.

|br|

Use the internal generator
--------------------------

Use the clas12.gcard in /jlab/workdir to launch gemc. For example, to run 200 events in batch mode using 4 GeV electrons at theta=20 degrees and phi=5 degrees::

 gemc -USE_GUI=0 -INPUT_GEN_FILE="lund, dvcs.lund" -N=200 -BEAM_P="e-, 4*GeV, 20*deg, 5*deg"

|br|

Use a LUND generated file
-------------------------

Use the clas12.gcard in /jlab/workdir to launch gemc. For example, to run 200 events in batch mode using
generated events in a `lund file <https://gemc.jlab.org/gemc/html/documentation/generator/lund.html>`_ in the local directory /home/max (mounted in /jlab/work/max)::

 cd /jlab/work/max
 gemc -USE_GUI=0 -INPUT_GEN_FILE="lund, dvcs.lund" -N=200 /jlab/work/clas12.gcard

This will produce an output with 200 generated events in evio format.

|br|


Using the available generators inside the container
---------------------------------------------------

Please see the :ref:`generators <generators>` list and documentation on the command line options.

|br|


.. _runningevio2hipoExample:

Convert GEMC evio output to hipo
================================

Use evio2hipo to convert the gemc output into hipo.

Notice that the field maps scaling are -1 for standard gcard settings, but make sure you match the values if you are not using the provided gcard::

 evio2hipo -r 11 -t -1.0 -s -1.0 -i out.ev -o gemc.hipo

- Tours: -1 = inbending electrons
- Solenoid -1: = field points upstream
- Run geometry 11: = default simulation geometry run number


|br|

.. _runningCoatjaveExample:

Reconstruction
==============

Use the script "createCookClara" in your path to setup the input file, output directory and (optional) the number of threads::

 createClaraCook.csh gemc.hipo 4

This will create the setup file "cook.clara".

Then run clara-shell to launch reconstruction::

 clara-shell cook.clara
