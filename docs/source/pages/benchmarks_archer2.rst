.. role:: raw-html(raw)
    :format: html
 
.. |br| raw:: html

  <br/>

========================
Results for CPU: Archer2 
========================

Archer2 is the UK National Supercomputer service capable of 28 Pflops/s at peak performance. 
The systems has 5,860 compute nodes, each with dual AMD EPYCTM 7742 64-core processors at 2.25GHz, 
giving 750,080 cores in total. 

Resuls are listed below

* Transpose real 3D array

  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_TrReal_Scal| |CPU_0512_TrReal_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_TrReal_Scal| |CPU_1024_TrReal_SpeedUp|

* Transpose complex 3D array 
  
  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_TrReal_Scal| |CPU_0512_TrReal_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_TrClx_Scal| |CPU_1024_TrClx_SpeedUp|

* FFT transform of a 3D real array starting from ``X`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_R2CX_Scal| |CPU_0512_R2CX_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_R2CX_Scal| |CPU_1024_R2CX_SpeedUp|

* FFT transform of a 3D complex array starting from ``X`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_C2CX_Scal| |CPU_0512_C2CX_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_C2CX_Scal| |CPU_1024_C2CX_SpeedUp|

* FFT transform of a 3D real array starting from ``Z`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_R2CZ_Scal| |CPU_0512_R2CZ_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_R2CZ_Scal| |CPU_1024_R2CZ_SpeedUp|

* FFT transform of a 3D complex array starting from ``Z`` physical direction
  
  * Resolution ``NX=NY=NZ=512`` |br| |CPU_0512_C2CZ_Scal| |CPU_0512_C2CZ_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |CPU_1024_C2CZ_Scal| |CPU_1024_C2CZ_SpeedUp|

Discussion on Archer2 results
_____________________________

The results above show that the the version 2.0 of 2DECOMP&FFT library keeps on having extremely good 
scalability performances.
The transpose tests show no difference between compilers since the tests mainly focus on MPI communication 
and for all executable CRAY MPICH (Version 8.1.23) has been used. 
It is interesting to notice that a 1D decomposition, when possible, can give up to a 80% speedup in comparison 
with the optimal 2D decomposition. This is because of the new feature of the library where a simple copy, 
avoiding completely MPI communication, is performed when data are all co-located in the local memory.
This was not the case with the previous version of the library. 
CRAY and GNU compilers performances using the *generic* FFT tends to differ for a low core count with the GNU
performing a bit better in some cases (up to 50% performace increase), however results tends to converge with the
increase of the numbers of nodes. 
This gives some superlinear behaviour when looking at the speedup. 

The **FFTW** has been tested only with the CRAY compiler and it gives a speed up of about 3 for a low core count 
decreasing to something in between 1.5 and 2 for the larger number of nodes.
The speed up with the FFTW is generally very close to the ideal lineat behaviour. 

.. 
   _Figures for Archer 2

.. |CPU_0512_TrReal_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrReal_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability transpose real test: Resolution 512^3 
.. |CPU_0512_TrReal_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrReal_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp transpose real test: Resolution 512^3 
.. |CPU_1024_TrReal_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrReal_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability transpose real test: Resolution 1024^3 
.. |CPU_1024_TrReal_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrReal_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp transpose real test: Resolution 1024^3 


.. |CPU_0512_TrClx_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrClx_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability transpose complex test: Resolution 512^3 
.. |CPU_0512_TrClx_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrClx_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp transpose complex test: Resolution 512^3 
.. |CPU_1024_TrClx_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrClx_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability transpose complex test: Resolution 1024^3 
.. |CPU_1024_TrClx_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrClx_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp transpose complex test: Resolution 1024^3 


.. |CPU_0512_R2CX_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CX_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CX test: Resolution 0512^3 
.. |CPU_0512_R2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CX_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CX test: Resolution 0512^3 
.. |CPU_1024_R2CX_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CX_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CX test: Resolution 1024^3 
.. |CPU_1024_R2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CX_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CX test: Resolution 1024^3 


.. |CPU_0512_C2CX_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CX_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CX test: Resolution 0512^3 
.. |CPU_0512_C2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CX_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CX test: Resolution 0512^3 
.. |CPU_1024_C2CX_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CX_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CX test: Resolution 1024^3 
.. |CPU_1024_C2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CX_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CX test: Resolution 1024^3 


.. |CPU_0512_R2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CZ_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CZ test: Resolution 0512^3 
.. |CPU_0512_R2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CZ_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CZ test: Resolution 0512^3 
.. |CPU_1024_R2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CZ_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CZ test: Resolution 1024^3 
.. |CPU_1024_R2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CZ_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CZ test: Resolution 1024^3 


.. |CPU_0512_C2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CZ_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CZ test: Resolution 0512^3 
.. |CPU_0512_C2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CZ_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CZ test: Resolution 0512^3 
.. |CPU_1024_C2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CZ_CPU_Archer2_ScalabilityTsec.pdf
   :width: 35%
   :alt: Archer2 Scalability R2CZ test: Resolution 1024^3 
.. |CPU_1024_C2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CZ_CPU_Archer2_SpeedUp.pdf
   :width: 35%
   :alt: Archer2 SpeedUp R2CZ test: Resolution 1024^3 




