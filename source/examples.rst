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


|br| |br| |br| |br|

.. _dockerConnectionExample:

Connecting to Docker
====================
This provides a refresher for using Docker. For a more in-depth tutorial, with
download instructions, please `look at this tutorial <https://clasweb.jlab.org/clas12/clas12SoftwarePage/html/tutorials/usingDocker/p1.html>`_.

Reminder: Running docker for the first time
-------------------------------------------

Use the following command to run docker:

.. code-block:: ruby

 docker run -it --rm jeffersonlab/clas12simulations:iprod bash

The image contains the necessary software to run simulation and reconstruction.

- gemc with the clas12 geometry
- CLARA
- Coatjava
- The CLAS12 magnetic fields

If this is your first time running docker, it will have to download the container before it can run.
Once the download is complete, it will run the container and it will look like a normal shell:

.. code-block:: text

 > Common Environment Version: <2.3>  (Wed October 3 2018)
 > Running as root on 2c742a6a8ba4
 > OS Release:    Linux_CentOS7.5.1804-x86_64-gcc4.8.5
 > JLAB_ROOT set to:     /jlab
 > JLAB_SOFTWARE set to: /jlab/2.3/Linux_CentOS7.5.1804-x86_64-gcc4.8.5

 > CCDB 	 version:  1.06.02
 > CLHEP 	 version:  2.4.0.4
 > GEANT4 	 version:  4.10.04.p02
 > QT   	 using system installation
 > XERCESC 	 version:  3.2.2
 > GEMC 	 custom location:  /jlab/clas12Tags/4.3.0/source
 > EVIO 	 version:  5.1
 > MLIBRARY 	 version:  1.3
 > SCONS 	 version:  1.7


|br|

Type `exit` to quit this docker image.

|br|


.. _usingDockerOptionsExample:

Options with Docker
===================

Here are some common and helpful commands for using Docker::

 docker run <container name> <command>                   #run a docker container and run a command
 -it                                                     #interactive, text terminal emulator
 --rm                                                    #remove container from memory after exiting
 -e DISPLAY=<address.of.display.>                        #set address for screen forwarding, e.g. X11 server
 --name <desired container name>                         #give the docker container a custom name
 -v /host/path/to/.bashrc:/root/.bashrc                  #Pass your own .bashrc file to the container to import your own settings
 -v /host/path/to/work_dir/:/docker/path/to/work_dir     #Mount a local directory into the docker container

 docker container ls -all                                #Lists all docker containers
 docker ps -a                                            #Identical to  `docker container ls -all`
 docker rm $(docker ps -aq)                              #Remove all docker containers

An example of using these flags is::

  docker run -it --rm -e DISPLAY=129.57.113.230:0.0 -v ~/mywork:/jlab/work/mywork jeffersonlab/clas12simulations:iprod bash

|br|

.. note::
  If you are using Docker on Windows and also using Windows Subsystem for Linux, Docker will mounting from /mnt. To successfully mount a directory,
  you must place any directories / files you want docker to connect to in the /mnt directory subdirectory.

|br|

.. note::
  If you want to load your own .bashrc file into the container, you must copy the container's .bashrc file into your customized one so that the
  environment loads properly. The container's .bashrc is found at /root/.bashrc. You should append the contents of that file to your custom .bashrc
  so things work smoothly.

|br|



Use a working directory
-----------------------

The docker image will always start with the "pristine" image. In other words every work the the container filesystem will be lost when you exit from a docker container.

Let's create a working directory on your computer so that any work can be saved for later use.
In this tutorial we'll use a "mywork" directory inside your home dir. On your computer type:

.. code-block:: ruby

 mkdir -p ~/mywork

Let's launch docker and mount this directory inside the jlab work dir. We do this by the **-v** option:

.. code-block:: ruby

 docker run -it --rm  -v ~/mywork:/jlab/work/mywork jeffersonlab/clas12simulations:iprod bash

Now /jlab/work/mywork will point to your mywork homedir. Inside docker, try this:

.. code-block:: ruby

 echo Hello World > /jlab/work/mywork/hello.txt
 exit

Check it out on your local computer:

.. code-block:: ruby

 cat mywork/hello.txt

Congratulations: you are now able to access a local directory inside docker.

|br|

Throughout this tutorial we'll use ~/mywork to store data files.



|br|

.. _simulationRunThroughExample:

Run Simulations in Docker
===================================

Running a generator
-------------------
Connect back to the docker container if you exited out of it::
  docker run -it --rm  -v ~/mywork:/jlab/work/mywork jeffersonlab/clas12simulations:iprod bash

The docker image contains three generator executables.Type::

 ls $CLAS12_BIN/

to list them.

- clasdis: `a SIDIS generatoron based on PEPSI LUND MC <https://github.com/JeffersonLab/clasdis-nocernlib/blob/master/README.md>`_.
- dvcsgen: `dvcs/pi0/eta generator using pdfs and gpds <https://github.com/JeffersonLab/dvcsgen/blob/master/README.md>`_.
- generate-dis: `inclusive DIS generator with radiative corrections <https://github.com/JeffersonLab/inclusive-dis-rad/blob/master/README.md>`_.

For information on using custom LUND files, see `here <file:///C:/Users/Bobby/Dropbox/Linux/work/CLAS12/htmlDoc/build/html/examples.html#use-custom-lund-file>`_.
For this example, let's create a "clasdis" directory inside mywork and run clasdis to get 100 events:

.. code-block:: ruby

 cd /jlab/work/mywork
 mkdir -p clasdis
 cd clasdis
 clasdis --trig 100 --docker --t 10 30
 ls -lrt

You should see the newly LUND file: **sidis.dat**

The LUND format is described `here <https://gemc.jlab.org/gemc/html/documentation/generator/lund.html>`_.

|br|

Running GEMC
------------

For information on using specific gcards, see `here <https://clasweb.jlab.org/clas12/clas12SoftwarePage/html/tutorials/advancedDocker/p2.html>`_.
For information on reconfiguring a gcard or compiling GEMC from source, see below.

Before running gemc, let's take a look at the gcard::

 cat /jlab/work/clas12.gcard

It contains the first experiment detectors, options for magnetic fields, etc.

Let's run gemc on 200 of those generated events inside sidis.dat:

.. code-block:: ruby

 gemc -USE_GUI=0 -N=100 -INPUT_GEN_FILE="lund, sidis.dat"  /jlab/work/clas12.gcard

The above command take up to a minute to load the magnetic field and setup the necessary physics processes.
After the physics tables are setup each event will take between 500 to 2000 ms, depending on the speed of your computer.
While gemc is running you will notice a lot of these warnings. They will be fixed / silenced eventually.

Upon exit gemc will inform you on the the processing time. For example::

 > Total gemc time: 203.73 seconds.  Events only time: 138.181 seconds.


The file out.ev is now ready to be analyzed. If you're curious, you can use evio2xml to peek inside::

 evio2xml out.ev | more

|br|

Decoding and Reconstruction
---------------------------

To decode this output and prepare to run CLARA using 2 cores (use the createClaraCook.csh second option to change that):

.. code-block:: ruby

 evio2hipo -r 11 -t -1.0 -s -1.0 -i out.ev -o gemc.hipo
 createClaraCook.csh gemc.hipo 2

Then run reconstruction using clara:

.. code-block:: ruby

 clara-shell cook.clara

|br|

The output file is called **out_gemc.hipo**.
Since we're running in interactive mode, it contains all the banks (so it's quite big) so we can enjoy the complete information from simulation and reconstruction.

Run hipo-utils to check the bank information on the reconstructed file::

 hipo-utils -stats out_gemc.hipo

The CLARA log is created within the log directory, take a look.

|br|

.. _customLUNDExample:

|br|

Use Custom LUND File
====================

You can use events that you generate yourself in docker by putting your LUND files in an online repository, e.g.
 `https://userweb.jlab.org/~ungaro/lund/ <https://userweb.jlab.org/~ungaro/lund/>`_.

We can download the LUND files in docker and then pass to GEMC as follows::

 docker run -it --rm  -v ~/mywork:/jlab/work/mywork jeffersonlab/clas12simulations:iprod bash
 cd /jlab/work/mywork
 mkdir -p custom_lund
 cd custom_lund
 wget https://userweb.jlab.org/~ungaro/lund/dis_1.txt
 gemc -USE_GUI=0 -N=100 -INPUT_GEN_FILE="lund, dis_1.txt"  /jlab/work/clas12.gcard

Putting LUND files online is also how you will pass custom files to run on offsite farms.
This is also how custom gcards can be passed to offsite farms (`e.g. <https://userweb.jlab.org/~ungaro/gcards/>`_.
This will be described in the coming weeks.
|br|

.. _modifiedGCARDExample:

|br|

Modify gcard for GEMC
=====================

The gcard in the container can also be edited as desired. As a simple example, consider removing
the magnets passive materials and the beamline:

.. code-block:: ruby

 cd /jlab/work/mywork
 mkdir -p no_material
 cd no_material
 cp /jlab/work/clas12.gcard ./no_mat.gcard


Now edit the no_mat.gcard with your favorite text editor::

 nano no_mat.gcard

1. remove these lines::

   <!-- magnets -->
   <detector name="experiments/clas12/magnets/solenoid" factory="TEXT" variation="original"/>
   <detector name="experiments/clas12/magnets/cad/"     factory="CAD" />
   <detector name="experiments/clas12/beamline/beamline"     factory="TEXT" variation="FTOn"/>

|br|

We're ready to run. Let's generate radiated dis events (saved by default in **dis-rad.dat**) and run gemc with the new gcard:

.. code-block:: ruby

 generate-dis  --trig 100 --docker --t 25 35
 gemc -USE_GUI=0 -N=100 -INPUT_GEN_FILE="lund, dis-rad.dat"  no_mat.gcard

Now let's run the decoder:

.. code-block:: ruby

 evio2hipo -r 11 -t 0.5 -s -1.0 -i out.ev -o gemc.hipo

|br|

Let's run createClaraCook.csh:

.. code-block:: ruby

 createClaraCook.csh gemc.hipo 2

|br|

And now run reconstruction within CLARA:

.. code-block:: ruby

 clara-shell cook.clara

|br|

.. _compileGEMCExample:

Compile GEMC
============

Let's use the docker libraries to compile a custom version of gemc.

Create a mygemc directory inside mywork and clone the gemc source code repository:

.. code-block:: ruby


 cd /jlab/work/mywork
 git clone https://github.com/gemc/source.git mygemc
 cd mygemc


|br|

Let's modify gemc.cc so the version used will be "99.99". Use your favorite text editor to do this, its line 32. You can compile gemc by typing:

.. code-block:: ruby

 scons -j4 OPT=1


The -j4 means that you will use 4 cores to speed up the compilation. If you have more cores you should increase this number.

|br|

To verify that your changes have been implememted run the "./gemc" executable, you should see your version in the screen log.

.. code-block:: ruby

 ./gemc -USE_GUI=0




|
