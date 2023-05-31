.. 2decomp_doc documentation master file, created by
   sphinx-quickstart on Wed Feb 15 14:31:32 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the documentation for the 2decomp&fft library!
=========================================================

.. note::
    This website is still **under development**!

The 2DECOMP&FFT library is a software framework written in modern Fortran to build large-scale parallel applications. It is designed for applications using three-dimensional structured mesh and spatially implicit numerical algorithms. At the foundation it implements a general-purpose 2D pencil decomposition for data distribution. On top it provides a highly scalable and efficient interface to perform three-dimensional distributed Fast Fourier Transforms. The library was originaly designed for CPUs only in the 2010's. The new version has be designed to work on CPUs and (NVIDIA) GPUs. We will aim to expend the capabilities of the library in the future (and to fix bugs!).

**Features**
=============

Here is a list of 2DECOMP&FFT's main features:

-General-purpose 2D pencil decomposition module to support building large-scale parallel applications on supercomputers.

-Highly scalable and efficient distributed Fast Fourier Transform module, supporting three dimensional FFTs (both complex-to-complex and real-to-complex/complex-to-real).

-Halo-cell support allowing explicit message passing between neighbouring blocks.

-Parallel I/O module to support the handling of large data sets.

-Interface with most popular external FFT libraries.

**2DECOMP&FFT is designed to be:**

**Scalable** - The library and applications built upon it are known to scale well on thousands of CPU cores.

**Flexible** - Software framework to support building higher-level libraries and many types of applications.

**User-friendly** - Black-box implementation and very clean application programming interface hiding most communication details from applications.

**Portable** - Code tested on major hardware architectures, with different compilers.

**Publication**
===============

N. Li and S. Laizet, "2DECOMP&FFT – A highly scalable 2D decomposition library and FFT interface", Cray User Group 2010 conference, Edinburgh, 2010

**a new paper is in preparation to introduce the new capabilities of the library**

**Acknowledgements**
====================
The library was initially designed thanks to several projects funded under the HECToR Distributed Computational Science and Engineering (CSE) Service operated by NAG Ltd. The new library has been designed thanks to the support of EPSRC via the `CCP Turbulence <https://www.ukturbulence.co.uk/ccp-turbulence.html>`_ (EP/T026170/1).

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   pages/installation.rst
   pages/domaindecomposition.rst
   pages/fft.rst
   pages/api.rst
   pages/benchmarks.rst
