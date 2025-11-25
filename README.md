COLMAP
======

About
-----

COLMAP is a general-purpose Structure-from-Motion (SfM) and Multi-View Stereo
(MVS) pipeline with a graphical and command-line interface. It offers a wide
range of features for reconstruction of ordered and unordered image collections.
The software is licensed under the new BSD license. If you use this project for
your research, please cite:

The latest source code is available at https://github.com/colmap/colmap. COLMAP
builds on top of existing works and when using specific algorithms within
COLMAP, please also cite the original authors, as specified in the source code,
and consider citing relevant third-party dependencies (most notably
ceres-solver, poselib, sift-gpu, vlfeat).


Download
--------

* Binaries for **Windows** and other resources can be downloaded
  from https://github.com/colmap/colmap/releases.
* Binaries for **Linux/Unix/BSD** are available at
  https://repology.org/metapackage/colmap/versions.
* Pre-built **Docker** images are available at
  https://hub.docker.com/r/colmap/colmap.
* Conda packages are available at https://anaconda.org/conda-forge/colmap and
  can be installed with `conda install colmap`
* **Python bindings** are available at https://pypi.org/project/pycolmap.
  CUDA-enabled wheels are available at https://pypi.org/project/pycolmap-cuda12.
* To **build from source**, please see https://colmap.github.io/install.html.



Documentation
-------------

The documentation is available at https://colmap.github.io/.

To build and update the documentation at the documentation website, follow these steps:

1. Build the documentation locally following [these instructions](https://colmap.github.io/install.html#documentation).
2. Clone the website repository [colmap/colmap.github.io](https://github.com/colmap/colmap.github.io).
3. Copy the contents of the generated files at `_build/html` to the cloned respository root.
4. Create a pull request to the [colmap/colmap.github.io](https://github.com/colmap/colmap.github.io) repository with the updated files.
5. (optional if main release) Copy the previous release as legacy to the "legacy" folder, under a folder with the release number [as seen here](https://github.com/colmap/colmap.github.io/tree/master/legacy)

Support
-------

Please, use GitHub Discussions at https://github.com/colmap/colmap/discussions
for questions and the GitHub issue tracker at https://github.com/colmap/colmap
for bug reports, feature requests/additions, etc.


Acknowledgments
---------------

COLMAP was originally written by [Johannes Schönberger](https://demuc.de/) with
funding provided by his PhD advisors Jan-Michael Frahm and Marc Pollefeys.
The team of core project maintainers currently includes
[Johannes Schönberger](https://github.com/ahojnnes),
[Paul-Edouard Sarlin](https://github.com/sarlinpe), and
[Shaohui Liu](https://github.com/B1ueber2y).

The Python bindings in PyCOLMAP were originally added by
[Mihai Dusmanu](https://github.com/mihaidusmanu),
[Philipp Lindenberger](https://github.com/Phil26AT), and
[Paul-Edouard Sarlin](https://github.com/sarlinpe).

The project has also benefitted from countless community contributions, including
bug fixes, improvements, new features, third-party tooling, and community
support (special credits to [Torsten Sattler](https://tsattler.github.io)).