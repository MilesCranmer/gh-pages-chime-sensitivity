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

In the following code, the following code
has arguments: ``pulsar``, the dictionary input,
``number_frequency_samples`` with default ``5``,
``beams`` with default as all of them,
``beam_centers`` with default ``get_beam_centers()``,
``start_date`` with default ``datetime.datetime(year=2018, month=2, day=10, hour=0)``,
``end_date`` with default ``datetime.datetime(year=2018, month=2, day=11, hour=0)``,
and ``resolution_minutes`` with default as ``2``.


We first generate an array of frequencies from 400
to 800 MHz with the ``number_frequency_samples`` samples.

.. code-block:: python

    start_frequency = 400
    end_frequency = 800
    frequencies = np.linspace(
            start_frequency,
            end_frequency,
            number_frequency_samples)*u.MHz


Note that this array is multiplied by ``u.MHz``.
This ``u`` is short for ``astropy.units``,
which allows us to add a units attribute to any
numpy array. This is a dummy-proof way of multiplying
values with intrinsic unit dimensionality without
missing orders of magnitude - that part of the multiplication
is handled by the units package. Functions/attributes
of this package used include: ``.si``,
which converts the array into simplified SI units,
and ``.value``, which returns a vanilla numpy array
in the current units. One can also use, e.g.,
``.to('cm')`` to convert to specific units.

Next, let us calculate the system temperature
using various system temperature frequency-dependencies.
This calculates it at each frequency and gives
it in the same-shape array with units of Kelvin:

.. code-block:: python

    Tsys = get_system_temperature(frequencies)



.. code-block:: python

    dec_injection = pulsar['dec']
    ra_injection = pulsar['ra']/24.*360.
    period = pulsar['p0'] *u.s
    width = pulsar['w50']*u.ms
    fifty_percentile_width = pulsar['w50']*u.ms
    duty_cycle = (fifty_percentile_width/period).si

    #1.*u.mJy/duty_cycle
    fluxes = 1.*u.mJy*linear_interpolate(
        x=frequencies,
        x0=400*u.MHz,
        x1=1400*u.MHz,
        y0=float(pulsar['s400']),
        y1=float(pulsar['s1400']))/duty_cycle

    #TODO: Use great circle angle in the projection
    projection = np.cos((49.5-dec_injection)/180.*np.pi)
    snr_multiplier = (CHIME_GAIN*fluxes*(2.*width*CHIME_BANDWIDTH)**(0.5)).si*projection
    width = pulsar['w50']*u.ms #The 50% level

    lightcurve = exposure_lightcurve(
        ra_injection,
        dec_injection,
        start_date,
        end_date,
        resolution,
        beams,
        frequencies,
        beam_centers)
    
    deg_to_rad = lambda deg: deg/180.*np.pi
    Tsky = u.K*get_temp(deg_to_rad(ra_injection), deg_to_rad(dec_injection), (frequencies/(1*u.MHz)).si)
    snr_lightcurve = (lightcurve*snr_multiplier[None, :]/(Tsky+Tsys)).si


    # Calculate SNR expected over time
    total_lightcurve = np.sqrt(np.average(np.square(snr_lightcurve), axis=1))

    # Return lightcurve SNR
    return total_lightcurve
