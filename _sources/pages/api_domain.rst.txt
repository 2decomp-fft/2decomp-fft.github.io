===========================
2D Pencil Decomposition API
===========================

This page explains the key public interfaces of the 2D decomposition library. After reading this section, users should be able to easily build applications using this domain decomposition strategy. The library interface is designed to be very simple. One can refer to the sample applications for a quick start.

The 2D Pencil Decomposition API is defined in one Fortran module which should be used by applications:

      ``use decomp_2d``
      
**Global Variables**

Following is the list of global variables defined by the library that can be used in applications. Obviously these names should not be redefined in applications to avoid conflict. Also note that some variables contain duplicate or redundant information just to simplify the programming.

* ``mytype`` - Use this variable to define the KIND of floating-point data, e.g. real(mytype) :: var or complex(mytype) :: cvar. This makes it easy to switch between single precision and double precision (more details).

* ``real_type, complex_type`` - These are the proper MPI datatypes to be used (for real and complex numbers, respectively) if applications need to call MPI library routines directly.

* ``nx_global, ny_global, nz_global`` - size of the global data.

* ``nproc`` - the total number of MPI processes.

* ``nrank`` - the rank of the current MPI process.

* ``xsize(i), ysize(i), zsize(i), i=1,2,3`` - sizes of the sub-domains held by the current process. The first letter refers to the pencil orientation and the three 1D array elements contain the sub-domain sizes in X, Y and Z directions, respectively. In a 2D pencil decomposition, there is always one dimension which completely resides in local memory. So by definition xsize(1)==nx_global, ysize(2)==ny_global and zsize(3)==nz_global.

* ``xstart(i), ystart(i), zstart(i), xend(i), yend(i), zend(i), i=1,2,3`` - the starting and ending indices for each sub-domain, as in the global coordinate system. Obviously, it can be seen that xsize(i)=xend(i)-xstart(i)+1. It may be convenient for certain applications to use global coordinate (for example when extracting a 2D plane from a 3D domain, it is easier to know which process owns the plane if global index is used).
