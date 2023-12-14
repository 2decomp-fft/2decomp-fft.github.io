===========================
2D Pencil Decomposition API
===========================

This page explains the key public interfaces of the 2D decomposition library. After reading this section, users should be able to easily build applications using this domain decomposition strategy. 
The library interface is designed to be very simple. One can refer to the 
`example applications <https://github.com/2decomp-fft/2decomp-fft/tree/main/examples>`_ 
for a quick start.

The 2D Pencil Decomposition API is defined in three Fortran module which should be used by applications as:

::
  
  use decomp_2d_constants
  use decomp_2d_mpi
  use decomp_2d

The ``use decomp_2d_constants`` defines all the parameters, ``use decomp_2d_mpi`` introduces all the MPI
related interfaces and ``use decomp_2d`` cointains the main decomposition and transposition APIs. 
      
Module **decomp_2d_constant**: Global Variables
_______________________________________________

The ``decomp_2d_constants`` cointains global parameters that used to define the KIND of floating 
point data (e.g. single or double precision). 
These are used to consistently define the precision of the data type for the viariables 
and for MPI operations. 

* ``mytype`` - Use this variable to define the KIND of floating-point data, 
  e.g. ``real(mytype) :: var`` or ``complex(mytype) :: cvar``. 
  Depending on configuring options this type will point to single or double. 

* ``real_type, complex_type`` - These are the proper MPI datatypes to be used 
  (for real and complex numbers, respectively) if applications need to call MPI library routines directly.
  These types will point to single of double depending on the configuring options. 

* ``real2_type`` - This type double the precision of the baseline ``real_type``. 

* ``mytype_single, real_type_single`` - These two types are used to define the data type fpr the IO operations.

The module contains additional parameters to control : 

* the log on output, 

* the log on debug, 

* activate the degugger (caliper)

* activate the different FFT backends (generic, FFTW, MKL, cuFFT)

* define the release major and minor version

Module **decomp_2d_mpi**: MPI communication
___________________________________________

The ``decomp_2d_mpi`` cointains global parameters that are used for MPI operation:  
 
* ``nproc`` - the total number of MPI processes. [INT].

* ``nrank`` - the rank of the current MPI process. [INT].

* ``decomp_2d_comm`` - global MPI communicator [INT].

* ``decomp_2d_about`` - interface to display error message and call MPI_ABORT function.

* ``decomp_2d_warning`` - interface to display error message together with line number and function.

Module **decomp_2d**: decompostion module
_________________________________________

The ``decomp_2d`` module cointains the variables and the routines to perform the global transpostion operations. 
The important variables are

* ``nx_global, ny_global, nz_global`` - size of the global data.

* ``xsize(i), ysize(i), zsize(i), i=1,2,3`` - sizes of the sub-domains held by the current process. 
  The first letter refers to the pencil orientation and the three 1D array elements contain 
  the sub-domain sizes in X, Y and Z directions, respectively. 
  In a 2D pencil decomposition, there is always one dimension which completely resides in local memory. 
  So by definition ``xsize(1)==nx_global``, ``ysize(2)==ny_global`` and ``zsize(3)==nz_global``.

* ``xstart(i), ystart(i), zstart(i), xend(i), yend(i), zend(i), i=1,2,3`` - the starting and ending indices 
  for each sub-domain, as in the global coordinate system. 
  Obviously, it can be seen that ``xsize(i)=xend(i)-xstart(i)+1``. 
  It may be convenient for certain applications to use global coordinate 
  (for example when extracting a 2D plane from a 3D domain, 
  it is easier to know which process owns the plane if global index is used).

Decomposition informations are also available using the data type ``DECOMP_INFO`` which provides the 
following derived types: 

* ``xst(i), yst(i), zst(i), i=1,2,3`` - the starting indices for each sub-domain, as in the global coordinate system. 

* ``xen(i), yen(i), zen(i), i=1,2,3`` - the end indices for each sub-domain, as in the global coordinate system. 

* ``xsz(i), ysz(i), zsz(i), i=1,2,3`` - the size for each sub-domain, as in the global coordinate system. 


Arrays can also be stored on smaller mesh sizes where points are skipped: 

* ``iskipS, jskipS, kskipS`` - points skipped in the x, y and z direction for a generic scalar field

* ``iskipV, jskipV, kskipV`` - points skipped in the x, y and z direction for the velocity field

* ``iskipP, jskipP, kskipP`` - points skipped in the x, y and z direction for the pressure field

* ``xszS, yszS, zszS, xstS, ystS, zstS, xenS, yenS, zenS`` - size, starting and final indexes for the 
  reduce size mesh for a generic scalar

* ``xszV, yszV, zszV, xstV, ystV, zstV, xenV, yenV, zenV`` - size, starting and final indexes for the 
  reduce size mesh for the velocity field

* ``xszP, yszP, zszP, xstP, ystP, zstP, xenP, yenP, zenP`` - size, starting and final indexes for the 
  reduce size mesh for the pressure field

The module provides memory allocations API that are recomended to correctly define its major data structures 
within the main program. It is recomended that all major arrays are defined as ``allocable``.

* ``alloc_x(var, decomp, global)`` - allocation using a x-pencil decomposition

* ``alloc_y(var, decomp, global)`` - allocation using a y-pencil decomposition

* ``alloc_y(var, decomp, global)`` - allocation using a z-pencil decomposition

where ``var`` is the allocable array name,
``decomp[optional]`` the relative ``DECOMP_INFO`` data type and 
``global[optional]`` is a logical [True/False] flag to indicate if the array is allocated in the global coordinate system. 
The allocation for a x pencil decomposition would be equivalent to the statement:

::

 allocate(var(decomp%xsz(1), decomp%xsz(2), decomp%xsz(3)))   ! if global==.false.
 allocate(var(decomp%xst(1):decomp%xen(1), decomp%xst(2):decomp%xen(2), &
                   decomp%xst(3):decomp%xen(3)))               ! if global==.true.

Allocated arrays can be simply released with an ``deallocate(var)`` statement. 

Basic 2D Decomposition API
^^^^^^^^^^^^^^^^^^^^^^^^^^

All the global variables described above, the defualt common type ``decomp`` and the MPI initialization is done 
using the following call

::

 call decomp_2d_init(nx, ny, nz, p_row, p_col)

where ``nx``, ``ny`` and ``nz`` are the size of 3D global data to be distributed over 
a 2D processor grid :math:`p_row \times p_col`. 
Note that none of the dimensions need to be divisible by ``p_row`` or ``p_col``, i.e. the library can handle non-evenly distributed data.
In case of ``p_row=p_col=0`` an automatic decomposition is selected among all possible combination available. 
The algorithm will choose the closest combination such as 

.. math::

  n\_proc=n\_col=\sqrt{nproc}

In case the root is not exact the closest combitation to have :math:`n\_proc \approx n\_col` with 
`n\_proc <  n\_col` is used.
If a 1D slab decomposition is needed instead of a 2D pencil one, it is recommended to set ``p_row`` to unity and ``p_col`` to ``nproc``.

An optional parameter may be passed to this initialisation routine:

::

 call decomp_2d_init(nx, ny, nz, p_row, p_col, periodic_bc)
  
Here ``periodic_bc`` is a 1D array containing 3 logical values that specify whether periodic boundary condition 
should apply in certain dimensions. Note this is only applicable if halo-cell communication is to be used.

Another optional parameter may be passed at the initialization stage:

::

 call decomp_2d_init(nx, ny, nz, p_row, p_col, periodic_bc, comm)
  
Here ``comm`` is the MPI communicator that the library will use. By default, MPI_COMM_WORLD is used.

A key element of this library is a set of communication routines that actually perform the data transpositions. 
As mentioned, one needs to perform 4 global transpositions to go through all 3 pencil orientations. 
Correspondingly, the library provides 4 communication subroutines:
  
::

 call transpose_x_to_y(var_in,var_out)
 call transpose_y_to_z(var_in,var_out)
 call transpose_z_to_y(var_in,var_out)
 call transpose_y_to_x(var_in,var_out)

The input array ``var_in`` and ``var_output`` array out should have been defined 
and contain distributed data for the correct pencil orientations.

Note that the library is written using Fortran's generic interface so different data types are supported 
without user intervention. That means in and out above can be either real arrays or complex arrays, 
the latter being useful for FFT-type of applications.

As seen, the communication details are packed within a black box. From a user's perspective, 
it is not necessary to understand the internal logic of these transposition routines. 
From the developer's perspective, he has the freedom to change the implementation without breaking user codes.

It is however noted that the communication routines are expensive, 
especially when running on large number of processors. 
So applications should try to minimize the number of calls to them by adjusting the algorithms in use, 
even sometimes by duplicating computations.

Finally, before exit, applications should clean up the memory by:

:: 

  call decomp_2d_finalize

Advanced 2D Decomposition API
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

While the basic decomposition API is very user-friendly, there may be situations in which 
applications need to handle more complex data structures. There are quite a few examples:

*  While using real-to-complex FFTs, applications need to store both the real input 
   (say, of global size nx*ny*nz) 
   and the corresponding complex output (of smaller global size - such as (nx/2+1)*ny*nz - 
   where roughly half the output is dropped due to conjugate symmetry).

*  Many CFD applications use a staggered mesh system which requires different storage for global quantities 
   (e.g. cell-centred vs. cell-interface storage).

*  In applications using spectral method, for anti-aliasing purpose, 
   it is a common practice to enlarge the spatial domain before applying the Fourier transforms.

In all these examples, there are multiple global sizes and applications need to be able to distributed 
different data sets as 2D pencils. 
2DECOMP&FFT provides a powerful and flexible programming interface to handle this:

:: 

  TYPE(DECOMP_INFO) :: new_decomp
  call decomp_info_init(n1, n2, n3, new_decomp)

Here decomp is an instance of Fortran derived data type DECOMP_INFO encapsulating 
the 2D decomposition information associated with one particular global size :math:`n1\times n2 \times n3`. 
The decomposition object can be initialised using the ``decomp_info_init`` routine as:

:: 
  
  call decomp_info_init(n1,n2,n3, new_decomp)

This object then can be passed to the communication routines defined in the basic interface as a third parameter. 
For example:

:: 
 
  call transpose_x_to_y(var_in, var_out, new_decomp)

The input and output arrays can be allocated as:

::
 
  call alloc_x(var_in, new_decomp, .true.)
  call alloc_y(var_out, new_decomp, .true.)

Finally the defined type needs also to be nullified using: 

::

  call decomp_info_finalize(new_decomp)



