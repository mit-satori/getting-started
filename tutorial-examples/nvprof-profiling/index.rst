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
generates profiling information that can then be analysed. The commands needed are::

   git clone https://gist.github.com/de9e934a2315fd2551a794d40255d301.git
   cp de9e934a2315fd2551a794d40255d301/vector-add.cu .
   rm -fr de9e934a2315fd d2551a794d40255d301
   nvcc -o vector-add vector-add.cu
   bsub -gpu "num=1" -Is bash
   nvprof -s -o results.nvprof ./vector-add
   
The resulting profile can be examined visually on a laptop/desktop with the NVIDIA Visual Profiler 
installed ( see - https://developer.nvidia.com/nvidia-visual-profiler ). The profile 


Useful references
+++++++++++++++++
