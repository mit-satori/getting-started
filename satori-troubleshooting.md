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

4. WMLE installation when running for the first time

``` bash
conda install powerai
```

is posible to get the following error:

```bash
CondaHTTPError: HTTP 500 INTERNAL SERVER ERROR for url <https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/linux-ppc64le/current_repodata.json>
Elapsed: 00:00.515998

A remote server error occurred when trying to retrieve this URL.

A 500-type error (e.g. 500, 501, 502, 503, etc.) indicates the server failed to
fulfill a valid request.  The problem may be spurious, and will resolve itself if you
try your request again.  If the problem persists, consider notifying the maintainer
of the remote server.
```

to overcome this issue please re-enter your conda command.
