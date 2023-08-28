=========================
Fast Fourier Transforms
=========================

The Discrete Fourier Transform (DFT) plays an important role in many scientific and technical applications, 
including time-series and waveform analysis, solutions to linear partial
differential equations, convolution, digital signal processing, and image filtering. 
Many algorithms have been proposed to compute DFTs with a good efficiency 
and the most famous clas of efficient algorithms is generally referred to 
as fast Fourier transform (FFT) algorithms.


The 2DECOMP&FFT is providing an API to perform FFTs at scale using 1D and 2D Domain Decompositions 
on both CPUs and on GPUs. 
Although capable of doing actual FFT computations, 2DECOMP&FFT is mainly designed to performs data management 
and communications. The actual computations of 1D FFTs are delegated to a 3rd-party FFT library, 
assuming it is already fully optimised to run on a single CPU core. 
2DECOMP&FFT interfaces with several every popular FFT implementations so users have the freedom 
to choose their favourite packages.

Here is the list of FFT engines:

* **Generic** - This is 2DECOMP&FFT's own FFT implementation. 
  It is based on an algorithm that is attributed to Glassman (refer to Glassman's general N Fast Fourier Transform). 
  It is not particularly efficient, but serves two purposes:

  * It makes the package independent to external libraries, aiding portability.

  * It takes over the computation if the external FFT engine fails to work 
    (for example, if a user mistakenly passes in an input with length not supported by the underlying FFT engine).

  It is not recommended to use this FFT engine in production works as it may lose 2 digits 
  of accuracy when running in double-precision mode.

* **FFTW** - FFTW is the most popular open-source FFT implementation and it likely to work reasonably well 
  on all hardware due to its auto-tuning feather. 
  Version 3.x of FFTW API is used.
  Version 3.3 of FFTW contains a Fortran 2003 interface, as well as the old legacy Fortran interface. 
  A second FFTW implementation based on the new interface is also available in 2DECOMP&FFT, labelled FFTW_f03. 
  One benefit of the F2003 interface is the guaranteed memory alignment, 
  although this does not seem to make any major difference in term of performances.
  Many vendors support (such as CRAY) directly support FFTW on their systems. 

* **MKL** - The Intel Math Kernel Library implementation is to help port the code onto systems using Intel CPUs. 
  It is also possible to link the FFTW implementation above directly to MKL.

* **cuFFT** - This FFT backhand enable 2DECOMP&FFT to run on NVIDIA GPUs. It is available in conjunction with 
  the NVHPC compiler.  
 
