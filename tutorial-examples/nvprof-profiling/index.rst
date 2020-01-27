Profiling code with nvprof
--------------------------

Andrew Kirby ( ackirby@mit.edu )

Description
+++++++++++
The nvprof tool from NVidia can be used to create detailed profiles of where codes are spending time 
and what resources they are using. It can work for compiled CUDA code and for Python libraries.


Commands to run the examples
++++++++++++++++++++++++++++

To run the example we start in a shell on the login/front-end node, then run in a short interactive session. This
generates profiling information that can then be analysed. The commands needed for a simple illustrative cuda program 
are::

   git clone https://gist.github.com/de9e934a2315fd2551a794d40255d301.git
   cp de9e934a2315fd2551a794d40255d301/vector-add.cu .
   rm -fr de9e934a2315fd d2551a794d40255d301
   nvcc -o vector-add vector-add.cu
   bsub -gpu "num=1" -Is bash
   nvprof -s -o results.nvprof ./vector-add
   
The resulting profile can be examined visually on a laptop/desktop with the NVIDIA Visual Profiler 
installed ( see - https://developer.nvidia.com/nvidia-visual-profiler ). The profile file ``results.nvprof``
can be downloaded on a desktop/laptop with the profiler installed. 

Unfortunately for recent MacOS systems NVidia have declared

 
    ``Note that CUDA 10.2 is the last release to support macOS and future releases of CUDA may not support macOS``
    

( see- https://docs.nvidia.com/cuda/cuda-installation-guide-mac-os-x/index.html#system-requirements ) and it appears
that OSX 10.15.2 (the most recent Catalina) is not supported. 


Useful references
+++++++++++++++++
