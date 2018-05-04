.. chime-sensitivity documentation master file, created by
   sphinx-quickstart on Thu May  3 18:22:36 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to chime-sensitivity's documentation!
=============================================


Function python reference:
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. toctree::
   :maxdepth: 1
   :numbered:
   :caption: Primary modules

   snr_estimator
   common_pulsars
   exposure


.. toctree::
   :maxdepth: 1
   :numbered:
   :caption: Supporting modules

   beam_model
   generate_atnf_readable_cat
   get_temp_at_point
   known_source_viewing
   load_beam_centers
   reduce_atnf_cat

Quick-start installation requirements:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Python packages:**

- ``datetime``
- ``multiprocessing``
- ``numpy``
- ``scipy``
- ``pandas``
- ``matplotlib``
- ``astropy``
- ``ephem``
- ``healpy``
- ``h5py``
- ``pygsm``

Quick-start example queries:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Say you want to calculate the max SNR of B0329
over a single day period. To do this with default options:

.. highlight:: python

    from common_pulsars import get_pulsar
    from snr_estimator import expected_snr_curve_pulsar

    b0329 = get_pulsar('B0329+54') #Get a dict of pulsar parameters
    snr_over_time = expected_snr_curve_pulsar(b0329) #expected SNR of B0329
    max_snr = np.max(snr_over_time)
    print max_snr


Or, say you want to be more specific about settings
and instead calculate the observed number of events.
To do this:

.. highlight:: python

    from astropy import units as u
    from datetime import datetime
    from common_pulsars import get_pulsar
    from snr_estimator import expected_snr_curve_pulsar, estimate_n_events

    b0329 = get_pulsar('B0329+54')
    start_date=datetime(year=2018, month=2, day=10, hour=0)
    end_date=datetime(year=2018, month=2, day=11, hour=0)    

    snr_over_time = expected_snr_curve_pulsar(
        b0329, number_frequency_samples=10,
        beams=[139], resolution_minutes=0.5,
        start_date=start_date, end_date=end_date)
      
    n_events = estimate_n_events(
        snr_over_time, resolution_minutes=0.5,
        period=b0329['p0']*u.s, snr_threshold=10)

    print n_events
    


Quick-start tutorial with docker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Make sure you have docker installed. This tutorial assumes a familiarity with python, numpy, and matplotlib.

In your terminal, navigate to the repo with the sensitivity repo installed. Now, install the sensitivity code pre-reqs using Docker, with the following command::

    docker build -t chime_sensitivity .

This will create a "docker image" which is essentially a mini virtual box with a stripped linux distribution. The docker image allows you to run the sensitivity code without the headache of installing the required libraries yourself.

After the docker image has been created (under the name of ``chime_sensitivity``), go to the directory with the Sensitivity repo.
Run the following command::

    docker run -it
        --name minion
        --volume $(pwd):/workspace
        --publish 8888:8888
        chime_sensitivity /bin/bash

Let's break this down. We have just used the docker image called
``chime_sensitivity`` to create a new "docker container" that
we have called ``minion``. This is essentially a new virtual box
that comes with the sensitivity required libraries already installed.
After we eventually exit the container, we can just relaunch ``minion``
which saves whatever other packages we wish to install later.
In addition to this, we link the container's to the current directory
we are in using the ``$(pwd)``, and connect this to a directory
called ``/workspace`` inside the container. Edits we make inside
``/workspace`` will be to the host directory as well. Finally,
we connect the port ``8888`` inside the container to the same one
outside so that we can connect to a jupyter notebook from inside the docker.

Launching this container should move your terminal input
into a bash terminal inside of the container. You can now
use the sensitivity code with everything installed.

Now, inside this, navigate to the ``/workspace`` directory. Then, run::
    
    jupyter notebook --ip=* --port=8888 --allow-root

Create a new notebook in this directory. Paste in commands from ATNF_Catalog_viewing.py
to generate predictions for pulsars.


If you wish to exit the container, simply type ``exit``. 
If you wish to relaunch the same docker container (called ``minion``), run::

    docker start minion
    docker exec -it minion /bin/bash


You can also enter the container from another terminal window
(and so have two sessions at once) by running the::

    docker exec -it minion /bin/bash

command.

Additional things to try
------------------------

Here are some things you can try to do. 

Initially, I think you should play around with the ``pandas.DataFrame``
object, which is essentially a numpy array on steroids. Many objects
in the sensitivity code are dataframes. 

From here, you should try the following to learn the code:

- Try to predict n_events, plot against vs predicted. Try a cutoff at 20 SNR and see how that changes it.
- Try to change the beam model and see how it changes the output.
- Try to change the beam centers (maybe divide by two) and see how it changes output.
- View the SNR distributions
- Annotate the plot with pulsar names
- New: try to alter max SNR predicted using N-events - increase predicted SNR.
  Use sqrt(np.log(n**2/(2*np.pi*np.log(n**2/2/np.pi))))*(1+gamma/np.log(n))
  Or... 
- Increase the system temperature and see how it changes the output.
- Try to generate the plots for chime-sensitivity (from Arun's injection code), using the ipython notebook (use the same docker)





Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
