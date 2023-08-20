.. role:: raw-html(raw)
    :format: html
 
.. |br| raw:: html

  <br/>

==========
Benchmarks
==========

This page report the result of some scalability tests which are available in the under 
`example <https://github.com/2decomp-fft/2decomp-fft/tree/main/examples>`_
The test being performed are the following: 

#. `Transpose of a real <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/test2d/timing2d_real.f90>`_ 
   3D array.

#. `Transpose of a complex <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/test2d/timing2d_complex.f90>`_ 3D array.

#. 3D FFT trasform 
   `(fft_r2c_x) <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/fft_physical_x/fft_r2c_x.f90>`_ 
   of a **real** 3D array starting from `X` physical direction. 
   The trasform has both forward and backward to retrieve the inout array.

#. 3D FFT trasform 
   `(fft_c2c_x) <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/fft_physical_x/fft_c2c_x.f90>`_ 
   of a **complex** 3D array starting from `X` physical direction. 
   The trasform has both forward and backward to retrieve the inout array.

#. 3D FFT trasform 
   `(fft_r2c_z) <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/fft_physical_z/fft_r2c_z.f90>`_ 
   of a **real** 3D array starting from `Z` physical direction. 
   The trasform has both forward and backward to retrieve the inout array.

#. 3D_FFT_trasform 
   `(fft_c2c_z) <https://github.com/2decomp-fft/2decomp-fft/blob/main/examples/fft_physical_z/fft_c2c_z.f90>`_ 
   of a **complex** 3D array starting from `Z` physical direction. 
   The trasform has both forward and backward to retrieve the inout array.

All timing are collected averaging 50 repetitions of the test with the 0 iteration being discarded. 
Two resolutions have been tested: 

* ``NX=NY=NZ=512`` which corresponds to rougly 130 million points.

* ``NX=NY=NZ=1024`` which corresponds to rougly 1 billion points.

A **2D** label for the results indicates a 2D (i.e. pencils) decomposition using the optimal automatic configuration
(that generally corresponds to the closest decomposition to ``NR=NC``).
A **1D** label for the results indicates a 1D (i.e. slabs) decomposition. With 2DECOMP&FFT this is obatained
forcing one of the two decomposition direction to 1. If ``N_ROW=1`` an initial ``Z`` slabs 
(i.e. local memory data are in the ``XY`` plane) is obatained, 
conversely ``N_COL=1`` start from a ``X`` slabs configuration
(i.e. local memmory data are in the ``YZ`` plane).
Generally only one set of slabs data are plotted since performances are relatively similar.


The library has been benchmark on the following systems: 

* The UK National Supercomputer service `Archer2 <https://www.archer2.ac.uk>`_ 

* The GPU partition of the EPCC `Cirrus <https://github.com/2decomp-fft/2decomp-fft/tree/main/examples>`_ 
  service


.. toctree::
   :maxdepth: 1
   :caption: Here the detailed list of the results:

   benchmarks_archer2.rst

