.. role:: raw-html(raw)
    :format: html
 
.. |br| raw:: html

  <br/>

========================
Results for GPU: Cirrus 
========================

Cirrus is a national UK service provided by EPCC. The GPU partition of the cluster is 
composed by 2xIntel Xeon “Cascade Lake”, 2.4 Ghz, 20-core per node together with 
4xNvidia Tesla V100-SXM2-16GB GPU, 640 Tensor core, 5,120 CUDA core per node. 
The number of nodes available for the GPU partition is 36 for a total of 150 GPUs. 

Resuls are listed below

* Transpose real 3D array

  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_TrReal_Scal| |GPU_0512_TrReal_SpeedUp| 

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_TrReal_Scal| |GPU_1024_TrReal_SpeedUp|

* Transpose complex 3D array 
  
  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_TrClx_Scal| |GPU_0512_TrClx_SpeedUp| |GPU_0512_TrClx_SpeedUpGPU|

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_TrClx_Scal| |GPU_1024_TrClx_SpeedUp| |GPU_1024_TrClx_SpeedUpGPU|

* FFT transform of a 3D real array starting from ``X`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_R2CX_Scal| |GPU_0512_R2CX_SpeedUp| |GPU_0512_R2CX_SpeedUpGPU|

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_R2CX_Scal| |GPU_1024_R2CX_SpeedUp| |GPU_1024_R2CX_SpeedUpGPU|

* FFT transform of a 3D complex array starting from ``X`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_C2CX_Scal| |GPU_0512_C2CX_SpeedUp| |GPU_0512_C2CX_SpeedUpGPU|

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_C2CX_Scal| |GPU_1024_C2CX_SpeedUp| |GPU_1024_C2CX_SpeedUpGPU|

* FFT transform of a 3D real array starting from ``Z`` physical direction

  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_R2CZ_Scal| |GPU_0512_R2CZ_SpeedUp| |GPU_0512_R2CZ_SpeedUpGPU| 

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_R2CZ_Scal| |GPU_1024_R2CZ_SpeedUp| |GPU_1024_R2CZ_SpeedUpGPU|

* FFT transform of a 3D complex array starting from ``Z`` physical direction
  
  * Resolution ``NX=NY=NZ=512`` |br| |GPU_0512_C2CZ_Scal| |GPU_0512_C2CZ_SpeedUp| |GPU_0512_C2CZ_SpeedUpGPU| 

  * Resolution ``NX=NY=NZ=1024`` |br| |GPU_1024_C2CZ_Scal| |GPU_1024_C2CZ_SpeedUp| |GPU_1024_C2CZ_SpeedUpGPU|

Discussion on Cirrus results
_____________________________

This page present the first scalability tests of the GPU version of the version 2.0 of 2DECOMP&FFT library. 
All results have been obtained using the NVHPC compiler version 22.11 together with openMPI 4.1.4. 
The results for the GPU compilation tests both CUDA aware MPI and NVIDIA Colletive Communication Library (NCCL). 
The smallest resolution case ``NX=NY=NZ=512`` can also fit into a single GPU therefore results are reported 
also for a 1/4 (1 GPU) and for 1/2 (2 GPUs) of a node. Results with the pure MPI version instead use always 
at least a full node with the full 40 cores available. 
Speedup GPU/CPU is computed using as reference time for the CPU the case with 1 full node for the ``NX=NY=NZ=512``
resolution and 2 full nodes for ``NX=NY=NZ=1024`` since the largest case needs at least 8 GPUs to fit in memory. 

The CPU results, particularly the ones for the real and complex transposes, show an acceptable scalability but 
they are not comparable with the one presented for :doc:`Archer2 <benchmarks_archer2>` particularly for the coarses
mesh resolution. This could be mainly attributed to the network which is considerebly slower that the one available 
on Archer2. 

Communication greatly improves when using GPUs with both CUDA aware MPI and particularly with NCCL. 
For the GPU cases the slabs decomposition tends also to give better and more consistent performances with 
NCCL generally 50% or above faster than the CUDA aware MPI. 
For the low resolution case it is very noticeable a drop in performances when moving above 
1 node, that can be attributed to the already mentioned to the relatively slow interconnect. 
For the larger case, where at least 2 nodes are necessary to fir the case in the GPUs memory the interconnect 
issue is less visible. 
The speedup between between GPU acceleration and CPU is from a factor of 5 or above dependinng on the 
case and the resolution. 

.. 
   _Figures for Cirrus

.. |GPU_0512_TrReal_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrReal_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability transpose real test: Resolution 512^3 
.. |GPU_0512_TrReal_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrReal_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp transpose real test: Resolution 512^3 
.. |GPU_1024_TrReal_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrReal_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability transpose real test: Resolution 1024^3 
.. |GPU_1024_TrReal_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrReal_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp transpose real test: Resolution 1024^3 


.. |GPU_0512_TrClx_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrClx_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability transpose complex test: Resolution 512^3 
.. |GPU_0512_TrClx_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrClx_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp transpose complex test: Resolution 512^3 
.. |GPU_0512_TrClx_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_TrClx_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU transpose complex test: Resolution 512^3 
.. |GPU_1024_TrClx_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrClx_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability transpose complex test: Resolution 1024^3 
.. |GPU_1024_TrClx_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrClx_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp transpose complex test: Resolution 1024^3 
.. |GPU_1024_TrClx_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_TrClx_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU transpose complex test: Resolution 1024^3 


.. |GPU_0512_R2CX_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CX_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CX test: Resolution 0512^3 
.. |GPU_0512_R2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CX_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CX test: Resolution 0512^3 
.. |GPU_0512_R2CX_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CX_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CX test: Resolution 0512^3 
.. |GPU_1024_R2CX_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CX_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CX test: Resolution 1024^3 
.. |GPU_1024_R2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CX_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CX test: Resolution 1024^3 
.. |GPU_1024_R2CX_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CX_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CX test: Resolution 1024^3 


.. |GPU_0512_C2CX_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CX_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CX test: Resolution 0512^3 
.. |GPU_0512_C2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CX_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CX test: Resolution 0512^3 
.. |GPU_0512_C2CX_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CX_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CX test: Resolution 0512^3 
.. |GPU_1024_C2CX_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CX_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CX test: Resolution 1024^3 
.. |GPU_1024_C2CX_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CX_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CX test: Resolution 1024^3 
.. |GPU_1024_C2CX_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CX_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CX test: Resolution 1024^3 


.. |GPU_0512_R2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CZ_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CZ test: Resolution 0512^3 
.. |GPU_0512_R2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CZ_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CZ test: Resolution 0512^3 
.. |GPU_0512_R2CZ_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_R2CZ_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CZ test: Resolution 0512^3 
.. |GPU_1024_R2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CZ_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CZ test: Resolution 1024^3 
.. |GPU_1024_R2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CZ_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CZ test: Resolution 1024^3 
.. |GPU_1024_R2CZ_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_R2CZ_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CZ test: Resolution 1024^3 


.. |GPU_0512_C2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CZ_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CZ test: Resolution 0512^3 
.. |GPU_0512_C2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CZ_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CZ test: Resolution 0512^3 
.. |GPU_0512_C2CZ_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res0512x0512x0512_C2CZ_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CZ test: Resolution 0512^3 
.. |GPU_1024_C2CZ_Scal| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CZ_GPU_Cirrus_ScalabilityTsec.pdf
   :width: 32%
   :alt: Cirrus Scalability R2CZ test: Resolution 1024^3 
.. |GPU_1024_C2CZ_SpeedUp| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CZ_GPU_Cirrus_SpeedUp.pdf
   :width: 32%
   :alt: Cirrus SpeedUp R2CZ test: Resolution 1024^3 
.. |GPU_1024_C2CZ_SpeedUpGPU| image:: benchmarks_figs/2023_08_01_Res1024x1024x1024_C2CZ_GPU_Cirrus_SpeedUpGPUoverCPU.pdf
   :width: 32%
   :alt: Cirrus SpeedUp GPU/CPU R2CZ test: Resolution 1024^3 




