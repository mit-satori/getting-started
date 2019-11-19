## Troubleshooting 


1. The NVIDIA CUDA Toolkit provides a development environment for creating high performance GPU-accelerated applications. With the CUDA Toolkit, you can develop, optimize and deploy your applications on GPU-accelerated embedded systems, desktop workstations, enterprise data centers, cloud-based platforms and HPC supercomputers. The toolkit includes GPU-accelerated libraries, debugging and optimization tools, a C/C++ compiler and a runtime library to deploy your application. This package consists of a post-install script that downloads and installs the full cuda toolkit(compiler, libraries, with the exception of cuda drivers).
- Install CUDA-Development Toolkit with:
```bash
cudatoolkit-dev
```

2. [Troubleshooting DDL](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_ddltips.html)

3. Anaconda tips:
- Upgrade to latest packages from specific WMLCE version (1.6.0, 1.6.1, 1.6.2, 1.7.x)
```bash
echo "powerai-release ==1.6.1" >> $CONDA_DIR/conda-meta/pinned
conda update --all
```
- Upgrade previously-pinned env to latest WMLCE
```bash
conda update powerai --no-pin
conda update --all
```
