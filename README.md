### Satori Getting Started Documentation

This repo contains the .rst sources for Satori user documentation.

#### Building the documentation

The official documentation is built automatically by [readthedocs.org](https://readthedocs.org). 

To build it locally for editing purposes or different output formats requires 

[Sphinx](http://sphinx-doc.org) and the [Sphinx RTD theme](https://github.com/snide/sphinx_rtd_theme). 
To install via `pip`:

```bash
pip install Sphinx sphinx_rtd_theme
```

`make html` in repo root directory will generates the documentation in `_build/html/`. 
The generated `_build/html/index.html` should open in any browser. 
