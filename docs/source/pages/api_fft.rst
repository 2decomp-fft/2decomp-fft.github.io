==============================
API for Three-dimensional FFTs
==============================

To use the FFT programming interface, first of all, one additional Fortran module has to be used:

:: 

  use decomp_2d_fft

Initialisation of the FFT module
________________________________

The FFT interface is built on top of the 2D decomposition library, which, naturally, 
needs to be initialised first:

:: 

  call decomp_2d_init(nx, ny, nz, p_row, p_col)

where :math:`nx\times ny\times nz` is the 3D domain size and :math:`p\_row \times p\_col` 
is the 2D processor grid. 
Then one needs to initialise the FFT interface by:

::

  call decomp_2d_fft_init

The initialisation routine handles planing for the underlying FFT engine (if supported) 
and defines global data structures (such as temporary work spaces) for the computations. 
By default, it assumes that physical-space data is distributed in X-pencil format ``PHYSICAL_IN_X``. 
The corresponding spectral-space data is stored in transposed Z-pencil format after the FFT. 
To give applications more flexibility, the library also supports the opposite direction Z-pensil, 
passing the optional parameter ``PHYSICAL_IN_Z``:

:: 
 
  call decomp_2d_fft_init(PHYSICAL_IN_Z)

Physical-space data in Y-pencil is not supported since it requires additional expensive transpositions 
which does not make economical sense. 
There is a third and the most flexible form of the initialisation routine:

::

  call decomp_2d_fft_init(pencil, n1, n2, n3)

where ``pencil=PHYSICAL_IN_X`` or ``PHYSICAL_IN_Z`` and ``n1, n2, n3`` is an arbitrary problem size
different from :math:`nx\times ny\times nz`.
The result of the ``decomp_2d_fft_init`` operation is to create two new objects of type ``DECOMP_INFO``: 

#. ``ph`` - structure with default size :math:`nx\times ny\times nz` or size :math:`n1\times n2\times n3`
   in case of arbitrary defined problem

#. ``sh`` - structure for the ``r2c/c2r`` transform which with dimensions: 

   * ``PHYSICAL_IN_X`` - :math:`nx/2+1\times ny\times nz` (default) or :math:`n1/2+1\times n2\times n3` (customized)
   
   * ``PHYSICAL_IN_Z`` - :math:`nx\times ny\times nz/2+1` (default) or :math:`n1\times n2\times n3/2+1` (customized)

Complex-to-complex Transforms
_____________________________

The library supports three-dimensional FFTs whose data is distributed as 2D pencils and stored in ordinary ijk-ordered 3D arrays across processors. 
For complex-to-complex (c2c) FFTs, the user interface is:

:: 

  call decomp_2d_fft_3d(input, output, direction)

where ``direction`` can be either ``DECOMP_2D_FFT_FORWARD == -1`` for forward transforms, or ``DECOMP_2D_FFT_BACKWARD == 1`` for backward transforms. 
The input array ``input`` and ``output`` array out are both complex and 
have to be either a X-pencil/Z-pencil combination or vice versa, depending on the direction of FFT and 
how the FFT interface is initialised (``PHYSICAL_IN_X``, the default, or ``PHYSICAL_IN_Z`` the optional).

Real-to-complex & Complex-to-Real Transforms
____________________________________________

The interface for the the real-to-complex and complex-to-real transform is 

:: 

  call decomp_2d_fft_3d(input, output)

If the ``input`` data are real type a forward transform is assumed obtaining a complex ``output``. 
Similarly a backward FFT is computed if ``input`` is a complex array and ``output`` a real array.
When real input is involved, the corresponding complex output satisfies so-called *Hermitian redundancy* - 
i.e. some output values are complex conjugates of others. 
Taking advantage of this, FFT algorithms can normally compute r2c and c2r transforms twice as fast as c2c transforms 
while only using about half of the memory. 
Unfortunately, the price to pay is that application's data structures have to become slightly more complex. 
For a 3D real input data set of size :math:`nx\times ny\times nz` in a X-pencil deposition, 
the complex output can be held in an array of size :math:`nx/2+1\times ny\times nz`, with the first dimension being cut roughly in half. 
This change in size is reflected in the dimension assigned to the ``sp`` structure previously described
The size of the ``sp`` can also be recovered using the following routine:

:: 

  call decomp_2d_fft_get_size(start,end,size)

Here all three arguments are 1D array of three elements, returning to the caller the starting index, 
ending index and size of the sub-domain held by the current processor - 
information very similar to the ``start/end/size`` variables defined in the main decomposition library.

Please note that the complex output arrays obtained from X-pencil and Z-pencil input do not contain identical information. 
However, if *Hermitian redundancy* is taken into account, no physical information is lost and the real input can be fully recovered 
through the corresponding inverse FFT from either complex array.

Please also note that 2DECOMP&FFT does not scale the transforms. So a forward transform followed by a backward transform 
will not recover the input unless applications normalise the result by the size of the transforms.

Deallocation of the FFT module
______________________________

Finally, to release the memory used by the FFT interface:

::
  
  call decomp_2d_fft_finalize

It is possible to re-initialise the FFT interface in the same application at the later stage after it has been finalised, if this becomes necessary.
