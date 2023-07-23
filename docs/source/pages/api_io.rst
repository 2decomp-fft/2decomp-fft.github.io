============
Parallel I/O
============

For large-scale applications using thousands of processors, doing proper I/O is often as important
as having efficient parallel algorithms. 2DECOMP&FFT provides a parallel I/O module to help
applications handle large data set in parallel. This module takes advantage of the decomposition
information available in the library kernel and uses either the MPI-IO or ADIOS2 backends (selected
at compile time) to implement some most frequently used I/O functions for applications based on 3D
Cartesian data structures.

All the I/O functions have been packed in a Fortran module:

::
   
      use decomp_2d_io

To write a single three-dimensional array to a file
---------------------------------------------------

::
   
      call decomp_2d_write_one(ipencil,var,directory,filename,icoarse,io_name)

where ``ipencil`` describes how the data is distributed (valid values are: 1 for X-pencil; 2 for
Y-pencil and 3 for Z-pencil); ``var`` is the reference to the data array, which can be either real or
complex; ``directory`` is the path to where I/O should be written; ``filename`` is the name of the
file to be written; ``icoarse`` indicates whether the I/O should be coarsend (valid values are: 0
for no; 1 for the ``nstat`` and 2 for the ``nvisu`` coarsenings); ``io_name`` is the name of the I/O
group to be used. A more general form of the subroutine is:

::
   
      call decomp_2d_write_one(ipencil,var,directory,filename,icoarse,io_name,opt_decomp,reduce_prec,opt_deferred_writes)

where the global size of the data array is described by the decomposition object ``opt_decomp`` (as
discussed in the Advanced 2D Decomposition API), allowing distributed array of arbitrary size to be
written. The file written would contain the 3D array in its natural ijk-order so that it can be
easily post-processed (for example by a serial code). A corresponding read routine is also
available. The optional ``logical`` arguments ``reduce_prec`` and ``opt_deferred_writes`` enable
reduced precision writing (when enabled at compile time, MPI-IO only; default ``.true.``) and
enabling ADIOS2's deferred writer mode (default ``.true.``).

To write multiple three-dimensional variables into a file
.........................................................

The 2DECOMP&FFT library supports two ways of writing multiple variables to a single file which may
be used for check-pointing purposes, for example. The newer interface is described first and allows
codes to use the ADIOS2 and MPI-IO backends, the older interface is supported for backwards
compatibility.

When ``decomp_2d_write_one`` is called, the ``directory`` and ``io_name`` are combined to check
whether a particular output location is already opened, if not then a new file will be opened and
written to - this is the "standard" use.  If, however, a file is opened first then the call to
``decomp_2d_write_one`` will append to the current file, resulting in a single file with multiple
fields.  Once the check-pointing is complete the file can then be closed.

The following describes the original interface for writing multiple variables to a file and is only
supported by the MPI-IO backend. This function is very useful when creating check-point files or
result files. It is in the form of:

::
   
      call decomp_2d_write_var(fh,disp,ipencil,var)

where ``fh`` is a MPI-IO file handle provided by the application (file opened using MPI_FILE_OPEN);
``ipencil`` describes the distribution of the input data (valid values are: 1 for X-pencil; 2 for
Y-pencil and 3 for Z-pencil); ``disp`` (meaning displacement) is a variable of kind MPI_OFFSET_KIND
and of intent INOUT - it is like a pointer or file cursor tracking the location where the next chunk
of data would be written. It is assumed that the data array is in default size, otherwise the
function also takes a second and more general form:

::
   
      call decomp_2d_write_var(fh,disp,ipencil,var,opt_decomp)

where the decomposition object ``opt_decomp`` describes the arbitrary size of the global array.

To create a restart/checkpointing file, it is often necessary to save key scalar variables as
well. This can be done using:

::
   
      call decomp_2d_write_scalar(fh,disp,n,var)

where ``var`` is a 1D array containing n scalars of the same data type. The supported data types
are: real, complex and integer.

These subroutines have corresponding read routines with exactly the same set of
parameters. Applications are responsible for closing the file after everything is written (using
MPI_FILE_CLOSE). Again, each chunk of data in the file contains one variable stored in its natural
order. One major benefit is that it becomes very easy to read the data back into the application
using a different number of MPI processes.

To write a 2D slice of data from a 3D variable
----------------------------------------------

::
   
      call decomp_2d_write_plane(ipencil,var,iplane,n,filename,io_name,opt_decomp)

where ``ipencil`` describes the distribution of the 3D variable ``var``; ``iplane`` defines the
direction of the desired 2D slice (1 for X-plane; 2 for Y-plane and 3 for Z-plane); ``n`` specifies
which plane to write out (in global coordinate system) when positive, a value ``n<=1`` will output a
plane of the average along the ``iplane`` axis; ``filename`` is the name of the file to be
written; and ``io_name`` is the I/O group performing the write. As before, ``opt_decomp`` is an
optional parameter that can be used when ``var`` is of non-default size.

To write out a 3D variable in lower resolution to a file
--------------------------------------------------------

Applications using the 2D decomposition often handle quite large data sets. It may not be practical
to write out everything for post-processing. The following subroutine provide a convenient way to
write only a subset of the data for analysis.

::
   
      call decomp_2d_write_every(ipencil,var,iskip,jskip,kskip,filename,from1)

where once again ``ipencil`` describes the distribution of the 3D variable var and ``filename``
defines the name of the file to be written. Every ``iskip``-th points of the data in X-direction,
``jskip``-th in Y-direction and every ``kskip``-th in Z-direction are to be written. Finally
``from1`` is a boolean flag. Assuming every n-th data points are to be written out, the data points
have indices of:

* 1,n+1,2n+1... if from1 is ``.true.``
* n,2n,3n... ff from1 is ``.false.``

Quick I/O Reference
-------------------

The following table summarises the supported I/O types and data types of the subroutines:

+--------------+------+-------+------+-------+---------+-------------+
| IO functions | I/O type     | Data type              | decomp [*]_ |
+              +------+-------+------+-------+---------+             +
|              | Read | Write | Real | Cmplx | Int     |             |
+==============+======+=======+======+=======+=========+=============+
| _one         | X    | X     | X    | X     |         | X           |
+--------------+------+-------+------+-------+---------+-------------+
| _var         | X    | X     | X    | X     |         | X           |
+--------------+------+-------+------+-------+---------+-------------+
| _scalar      | X    | X     | X    | X     | X       | N/A         |
+--------------+------+-------+------+-------+---------+-------------+
| _plane       |      | X     | X    | X     |         | X           |
+--------------+------+-------+------+-------+---------+-------------+
| _every       |      | X     | X    | X     |         |             |
+--------------+------+-------+------+-------+---------+-------------+

.. [*] decomp refers to a decomposition object that describes an arbitrary-size global data set.

ADIOS2 backend for I/O
---------------------------------------

By default 2DECOMP&FFT will build with the MPI-IO backend for I/O. The alternative ADIOS2 backend
can be selected at compile time, by either specifying ``-DIO_BACKEND=adios2`` during configure or by
modifying the build configuration via ``ccmake``. Due to the way ADIOS2 works, there are a few
changes necessary to allow codes to work with either backend interchangeably.

Registering variables for I/O
.............................

Registering a variable for I/O informs ADIOS2 about the variables size and type, with MPI-IO this
call becomes a ``no-op``.

::

   subroutine decomp_2d_register_variable(io_name,varname,ipencil,icoarse,iplane,type,opt_decomp,opt_nplanes)

The variable is associated with an I/O group through ``io_name``; given a name ``varname``;
``ipencil``, ``icoarse`` and ``iplane`` determine the orientation and size of the data for I/O (see
previous descriptions, use ``iplane=0`` for 3D data); and ``type`` specifies the ``kind`` of the
data, only ``real`` data is currently supported in ADIOS2, i.e. ``real(kind=type)``. The optional
arguments ``opt_decomp`` and ``opt_nplanes`` are a decomposition object for non-standard sizes (see
previous descriptions) and ``opt_nplanes`` allows controlling how many planes are written in planar
output (default 1).

Opening a file for reading or writing
.....................................

The mode of operation for ADIOS2 is to open a file for I/O and keep this open, buffering multiple
fields before performing the I/O.

::

   subroutine decomp_2d_open_io(io_name, io_dir, mode)

The output destination ``io_dir`` is where all data will be written to from the group ``io_name``,
the ``mode`` can take the values ``decomp_2d_write_mode``, ``decomp_2d_read_mode`` or
``decomp_2d_append_mode`` to write, read or append to a file, respectively. This is required by all
ADIOS2 I/O, when using the MPI-IO backend I/O operations will open the file on-demand, in which case
I/O is file-per-field, or if the file is explicitly opened then subsequent I/O call will be into the
same file.

Beginning an I/O step
.....................

ADIOS2 performs I/O in "steps", this subroutine marks the beginning of a step to queue up I/O
operations.

::
   
   subroutine decomp_2d_start_io(io_name,io_dir)

The arguments ``io_name`` and ``io_dir`` are as described above. This is a ``no-op`` in the MPI-IO
backend.
   
Ending an I/O step
..................

::
   
   subroutine decomp_2d_end_io(io_name, io_dir)

This subroutine marks the end of an I/O step and ADIOS2 can begin performing I/O operations. This is
a ``no-op`` in the MPI-IO backend.
  
Closing a file
..............

::

   subroutine decomp_2d_close_io(io_name, io_dir)

Closes the I/O destination, must be matched with corresponding call to ``decomp_2d_open_io``. By
closing the I/O ADIOS2 is forced to perform a ``flush`` operation.
