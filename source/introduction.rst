Introduction
============

This project is described in my undergrad thesis
which is found at
`github.com/CHIMEFRB/Sensitivity/blob/master/thesis.pdf
<https://github.com/CHIMEFRB/Sensitivity/blob/master/thesis.pdf>`_.

This repo offers various functionality to estimate CHIME/FRB's
sensitivity to fast radio transients.

Pulsar SNR
----------

For the first example, let us estimate the maximum SNR
of a pulsar, and change the settings to add a bandpass attenuation.
Create a new file in the same folder as the sensitivity code.
Now, let's download a pulsar as a Python dictionary:

.. highlighting:: python
    
    from common_pulsars import get_pulsar
    
    b0329 = get_pulsar('B0329+54')


This stores a dict containing B0329's parameters. You
can write ``print b0329`` to see what is contained. If
at any point you would like to update the catalog or
add more values, add it to the pickled pandas DataFrame
in ``clean_atnf_cat.pkl``. Now, let's estimate the
max SNR for this source.

.. highlighting:: python

    from snr_estimator import expected_snr_curve_pulsar

    snr_over_time = expected_snr_curve_pulsar(b0329) #expected SNR of B0329


This function takes the pulsar parameter as well as settings
(which have some defaults) to estimate the expected SNR of
pulses over time.

We can see what the max SNR of the pulsar is
over its transit with the following command:

.. highlighting:: python

    max_snr = np.max(snr_over_time)
    print max_snr


