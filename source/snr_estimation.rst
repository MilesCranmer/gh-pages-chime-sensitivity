.. _snr_estimation::

Estimating the SNR of a pulsar
==============================

In :ref:`introduction`, the example of calculating
the max SNR of B0329 was given. Here,
we will walk through the code behind this and
see what is going on behind the scenes.
The example was:

.. code-block:: python

    snr_over_time = expected_snr_curve_pulsar(b0329) #expected SNR of B0329

This produces a curve of expected SNR over time (which,
by the default settings, is one-day at two-minute resolution).


