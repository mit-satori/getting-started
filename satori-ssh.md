## Login to Satori cluster

- Login to Satori cluster is done via SSHv2 as follow:

```bash
ssh your_username@satori-login-001.mit.edu
```

```bash
ssh your_username@satori-login-002.mit.edu
```
- To copy files from your workstation to Satori you can use SCP, [WinSCP](https://winscp.net/eng/download.php), [Cyberduck](https://cyberduck.io/download/) etc

```bash
scp <local-file.py> your_username@satori-login-001.mit.edu:
```

- For example:
```bash
scp ResNet50-Keras.py florin@satori-login-002.mit.edu:
ssh florin@satori-login-002.mit.edu
conda activate wmlce-1.6.2
python ResNet50-Keras.py
```

As general rules:
- satori-login-001.mit.edu - shuld be used for submiting training jobs and related activities
- satori-login-002.mit.edu - shuld be used for transfering large files/datasets and compiling software requireing nvcc, gcc, XL compiler etc
- if one login-node will not be available try the second one
- dont't run trainings of login nodes
