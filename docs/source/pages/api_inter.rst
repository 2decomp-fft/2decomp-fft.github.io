===========================
Interpolation API
===========================

This page explains the key public interfaces of the interpolation capability in 2D decomposition library.
The library interface is designed to be very simple. One can refer to the 
`interpolation examples <https://github.com/2decomp-fft/2decomp-fft/tree/dev/examples>`_ 
for a quick start.

The interpolation API is defined in one Fortran module which should be used by applications as:

::
  
  use decomp_2d_interp


Module **decomp_2d_interp**: interpolation module
_________________________________________

The ``decomp_2d_interp`` module cointains the routine to perform the interpolation operations.
The interpolation routine can interpolate a real array from one grid to another.
The default grid in the library is defined in the object ``decomp_main`` available in the modules ``decomp_2d`` and ``m_info``.

One can interpolate a 3D array from the default grid to another one using

::

 call decomp_2d_interp_var3d(ipencil, varin, varout, decompout, interp)

where ``ipencil`` is the integer describing the pencil orientation of the arrays, ``varin`` and ``varout`` are the input and the output arrays, respectively, ``decompout`` is the ``decomp_info`` object describing the output grid and ``interp`` is the interpolation strategy.

One can interpolate a 3D array to the default grid using

::                                                                                         

 call decomp_2d_interp_var3d(ipencil, varin, decompin, varout, interp)          

where ``ipencil`` is the integer describing the pencil orientation of the arrays, ``varin`` and ``varout`` are the input and the output arrays, respectively, ``decompin`` is the ``decomp_info`` object describing the input grid and ``interp`` is the interpolation strategy.

One can interpolate a 3D array from the one grid to another one using

::                                                                                         

 call decomp_2d_interp_var3d(ipencil, varin, decompin, varout, decompout, interp)          

where ``ipencil`` is the integer describing the pencil orientation of the arrays, ``varin`` and ``varout`` are the input and the output arrays, respectively, ``decompin`` and ``decompout`` are the ``decomp_info`` objects describing the input and the output grid, respectively, and ``interp`` is the interpolation strategy.

Currently, the only interpolation strategy available is ``DECOMP_2D_INTERP_BASIC``, defined in the module ``decomp_2d_constants``. It corresponds to a closest neighbor interpolation without halo or transpose operations. The associated accuracy is limited. It is a lightweight and robust strategy to downsample the array. This can be convenient to monitor variables on a smaller grid.

The IO section describes the interface allowing one to perform read / write an array defined on a specific grid.
