COLMAP
======

### cudss + Ceres+ COLMAP installation
# cuDSS

### Choose the latest Linux x86_64 cuDSS archive that matches CUDA 12.x from NVIDIA.

```bash
cd /home/patrikas_vanagas/0_orthophoto_code/sfm
wget https://developer.download.nvidia.com/compute/cudss/redist/libcudss/linux-x86_64/libcudss-linux-x86_64-0.3.0.9_cuda12-archive.tar.xz
tar -xf libcudss-linux-x86_64-0.3.0.9_cuda12-archive.tar.xz
sudo mkdir -p /opt/cudss-0.3.0.9
sudo rsync -a libcudss-linux-x86_64-0.3.0.9_cuda12-archive/ /opt/cudss-0.3.0.9/
```

### Make cuDSS discoverable at configure & runtime

```bash
echo "/opt/cudss-0.3.0.9/lib" | sudo tee /etc/ld.so.conf.d/cudss.conf
sudo ldconfig

// When configuring Ceres, point CMake at its package config: -Dcudss_DIR=/opt/cudss-0.3.0.9/lib/cmake/cudss

Add the missing version file so CMake’s find_package(cudss 0.3.0) succeeds:

sudo tee /opt/cudss-0.3.0.9/lib/cmake/cudss/cudss-config-version.cmake >/dev/null <<'EOF'
set(PACKAGE_VERSION "0.3.0.9")
if(DEFINED PACKAGE_FIND_VERSION)
if(PACKAGE_FIND_VERSION VERSION_LESS_EQUAL PACKAGE_VERSION)
set(PACKAGE_VERSION_COMPATIBLE TRUE)
if(PACKAGE_FIND_VERSION STREQUAL PACKAGE_VERSION)
set(PACKAGE_VERSION_EXACT TRUE)
endif()
else()
set(PACKAGE_VERSION_COMPATIBLE FALSE)
endif()
else()
set(PACKAGE_VERSION_COMPATIBLE TRUE)
endif()
EOF
```

# Ceres

### Deps

```bash
sudo apt-get update
sudo apt-get install -y git cmake ninja-build build-essential \
libeigen3-dev libsuitesparse-dev
```

Avoid Conda interference:

```bash
conda deactivate
```

### Source

```bash
git clone https://ceres-solver.googlesource.com/ceres-solver ~/src/ceres-solver
cd ~/src/ceres-solver
git fetch origin
git checkout master
git submodule update --init --recursive third_party/abseil-cpp third_party/googletest
```

### Configure (note the backslash before -DCMAKE_IGNORE_PATH)

```bash
cmake -S ~/src/ceres-solver -B ~/src/ceres-build -G Ninja \
-DCMAKE_BUILD_TYPE=Release \
-DUSE_CUDA=ON \
-DBUILD_TESTING=OFF \
-Dcudss_DIR=/opt/cudss-0.3.0.9/lib/cmake/cudss \
-DCMAKE_IGNORE_PATH="$HOME/miniconda3;/usr/local/lib/cmake/absl"
```

Build:

```bash
cmake --build ~/src/ceres-build -j"$(nproc)"
```

Install:

```bash
sudo cmake --install ~/src/ceres-build --prefix /usr/local
```

Header check (cuDSS should NOT be disabled)

```bash
grep -n "CERES_NO_CUDSS" ~/src/ceres-build/include/ceres/internal/config.h
```

# Colmap

```bash
rm -rf /home/patrikas_vanagas/0_orthophoto_code/sfm/colmap-sfm/build
```

```bash
cd /home/patrikas_vanagas/0_orthophoto_code/sfm/colmap-sfm
```

// change DCMAKE_BUILD_TYPE to Release in production

```bash
cmake -S . -B build -G Ninja \
    -DCMAKE_BUILD_TYPE=RelWithDebInfo \
    -DCUDA_ENABLED=ON \
    -DCMAKE_CUDA_ARCHITECTURES=89 \
    -DCeres_DIR=/usr/local/lib/cmake/Ceres \
    -DBLA_VENDOR=Intel10_64lp \
    -DGUI_ENABLED=OFF \
    -DCMAKE_IGNORE_PATH=/home/patrikas_vanagas/miniconda3
```

`DCeres_DIR=/usr/local/lib/cmake/Ceres` forces CMake to use the Ceres you just installed.

I set `-DCMAKE_CUDA_ARCHITECTURES=89` (RTX 4090) to avoid long multi-arch compilations.

Build:

```bash
cmake --build build -j"$(nproc)"
```

Install:

```bash
sudo cmake --install build --prefix /usr/local
```

You do not need to point COLMAP at cuDSS directly; COLMAP only needs to find your Ceres that was built with cuDSS.


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