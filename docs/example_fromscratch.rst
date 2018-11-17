Creating a Spectrum from scratch
================================

.. include:: <isogrk3.txt>

This example shows the initialization of a pyspeckit object from numpy arrays.
The example creates file fromscratch.png

If no interactive plot appears after Step 3, run these python lines using command:

ipython --pylab

.. code-block:: python

   import numpy as np
   import pyspeckit
   
   # Step 1: Create x axis and gaussian intensities
   xaxis = np.linspace(-50,150,100)
   sigma = 10.
   center = 50.
   synth_data = np.exp(-(xaxis-center)**2/(sigma**2 * 2.))

   # Step 2: Add noise
   stddev = 0.1
   noise = np.random.randn(xaxis.size)*stddev
   error = stddev*np.ones_like(synth_data)
   data = noise+synth_data

   # this will give a "blank header" warning, which is fine
   sp = pyspeckit.Spectrum(data=data, error=error, xarr=xaxis,
                           xarrkwargs={'unit':'km/s'},
                           unit='erg/s/cm^2/AA')
			   
   # Step 3: Open Plot window
   sp.plotter()

   # Fit with automatic guesses
   sp.specfit(fittype='gaussian')

   # Step 4: Fit with input guesses
   # The guesses initialize the fitter
   # This approach uses the 0th, 1st, and 2nd moments
   amplitude_guess = data.max()
   center_guess = (data*xaxis).sum()/data.sum()
   width_guess = (data.sum() / amplitude_guess / (2*np.pi))**0.5
   guesses = [amplitude_guess, center_guess, width_guess]
   sp.specfit(fittype='gaussian', guesses=guesses)

   sp.plotter(errstyle='fill')
   # Step 5: Plot with fit over layed
   sp.specfit.plot_fit()

   # Step 6: Save output image
   sp.plotter.figure.savefig("fromscratch.png")
