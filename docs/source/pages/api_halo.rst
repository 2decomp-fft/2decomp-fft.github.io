===================
Halo-cell Support
===================

While most of the communications using the 2D decomposition are via the global transposition calls, it may become necessary for neighbouring blocks to exchange data explicitly. One such scenario is in CFD applications performing large-eddy simulations (LES). While most spatial derivatives are computed using the implicit formulation to achieve a high order of accuracy, some derivatives may be evaluated quickly using local stencils and explicit formulae, such as those used by sub-grid scale models (a model by definition does not require higher-order of accuracy).

The halo-cell support API provides data structures and nearest-neighbour communication routines that support explicit message passing between neighbouring pencils. As with the rest of the 2DECOMP&FFT library, the API is designed to be very user-friendly:

``call update_halo(var, var_halo, level)``

Here the first parameter var, a 3D input array, contains the normal pencil-distributed data as defined by the decomposition library. After the subroutine call, the second parameter var_halo, an output, returns all original data plus halo data from the neighbouring processes. One can imagine that the pencils are now fatter and overlap with the neighbouring pencils. The third parameter level defines how many layers of overlapping is required. var_halo should be defined from the calling routine as either a 3D allocatable array or pointer. Its memory space will be calculated and allocated by the library and when the subroutine returns it can be indexed by the calling program using the normal i,j,k indices.

As with the rest of the 2DECOMP&FFT library, a more general form of the routine is available (implemented using Fortran optional parameters):

``call update_halo(var, var_halo, level, opt_decomp, opt_global, opt_pencil)``

This supports halo-cell communications among pencils with arbitrary global sizes, as described by
`opt_decomp`, the decomposition object.
The optional parameter `opt_global` is required (to be set to .true.) if global coordinate is used
to define the pencils, i.e. the input array var is defined using the start/end variables rather than
size variables.
This ensures the coordinate systems used by `var` and `var_halo` are consistent.
The optional parameter `opt_pencil` allows the user to specify which pencil they are operating in
(`1=X,2=Y,3=Z`), our recommendation is to do so, the current interface makes this parameter optional
to continue supporting the previous implementation which would try to determin the orientation
automatically, however this may not be reliable.

To demonstrate the use of this API, here is an example that computes spatial derivatives:

::

     ! to calculate dv/dy, assume that variables are stored in X-pencil
      
     real, allocatable, dimension(:,:,:) :: v, v_halo, dvdy
      
     allocate(v(xsize(1), xsize(2), xsize(3)))
     allocate(dvdy(xsize(1), xsize(2), xsize(3)))
      
     call update_halo(v,v_halo,level=1)
      
     ! compute derivatives
     do k=1,xsize(3)
        do j=1,xsize(2)
           do i=1,xsize(1)
              dvdy(i,j,k) = (v_halo(i,j+1,k)-v_halo(i,j-1,k)) / dy
           end do
        end do
     end do
      
    
As seen, the variables are stored in X-pencil and derivatives are to be evaluated over distributed data along Y direction using central difference. This is the perfect situation to use the halo-cell support API. Using global transpositions would be unnecessarily too expensive for this type of explicit calculations. After the call to `update_halo`, it is safe to refer to the j+1 and j-1 indices on array `v_halo` in order to compute the derivatives.

Please note that for the pencils bordering the computational domain, it is up to the application to handle the physical boundary conditions. The library does support periodic condition, i.e. for processes near the boundary of the computational domain, a call to the update_halo routine will fill the halo cells of one side with values from the other side of the domain, when periodic condition is required. To specify periodic condition, one need to initialise the decomposition library with additional information:

``call decomp_2d_init(nx, ny, nz, P_row, P_col, periodic_bc)``

The extra parameter periodic_bc is a 1D array containing 3 logical values that specify which dimension should be periodic. This parameter is *optional* and should only be used with the halo-cell API. The domain decomposition should otherwise behaves exactly as normal.

Like the rest of 2DECOMP&FFT, the halo-cell support API is implemented in a black-box fashion. The library internally handles the communications between neighbouring blocks using standard MPI non-blocking point-to-point communications.

-----------------------
Halo-exchange interface
-----------------------

The high-level halo interface described above makes it easy to add halo support to existing codes.
However, by handling the memory allocation for you, it is difficult to adopt in a code that is
already written to handle its halos when porting to the 2DECOMP&FFT library.
As described above, the `update_halo` interface operates in two phases:
1) a new array, extended with halo entries is allocated
2) local data is copied into the local region of this array and used to communicate halo values to
neighbouring processes

To facilitate the use of 2DECOMP&FFT by existing stencil codes we have exposed the data exchange
(step 2) as a new interface `exchange_halo`.
There are two behaviours of the interface based on the optional parameters `opt_depth` and
`opt_levels`

::
  call halo_exchange(arr, ipencil, opt_depth, opt_levels)

where `arr` is the working array, `ipencil=1,2,3` indicates the pencil orientation and the optional
values `opt_depth` and `opt_levels` take scalar and array integer values to indicate the halo
depths.
The former sets the halo depth in the two perpendicular directions to the current pencil orientation
and the latter allows an arbitrary depth of halo to be set
in each axis of the current orientation.
The working array is expected to be prepopulated with local data and of size
`(nx+2*ih,ny+2*jh,nz+2*kh)`, where `ih,jh,kh` are the halo depths in each direction.
To illustrate the use of `exchange_halo` the two calls

::
  call halo_exchange(vh, 1, opt_depth=1)
  call halo_exchange(wh, 1, opt_levels=[0, 1, 1])

are equivalent and will exchange halo data to depth 1 in the Y and Z axes for data in the X pencil.

To aid users in creating suitably-sized arrays the allocation subroutines have been extended with
optional halo parameters `opt_depth` and `opt_levels` whose meaning matches the above.
When supplied with non-zero values 2DECOMP&FFT will allocate a halo-padded array to the requested
depth.

When a code is heavily dependent on halo exchanges, it may be repetitive to specify the halo depths
at each exchange.
In such cases, the user can specify a default depth for a given decomposition by setting

::
   decomp_info%xlevel = [ihx, jhx, khx]
   decomp_info%ylevel = [ihy, jhy, khy]
   decomp_info%zlevel = [ihz, jhz, khz]

if `opt_depth/opt_levels` is not specified in the allocation, or halo exchange routines, then this
value will be used (note that by default these are all set to `[0,0,0]`).

We would like to acknowledge the work of S.Owens in the ARCHER2 eCSE05-03 project
(https://www.archer2.ac.uk/ecse/reports/eCSE05-03/) who initially developed these interfaces.
