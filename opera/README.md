## Install the development environment for OPERA project

It installs packages used by the OPERA projects, including `isce3`, `mintpy`, `s1-reader`, `compass` etc.

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install OPERA related packages

#### a. Download source code

```bash
cd ~/tools
mkdir -p isce3; cd isce3
mkdir -p build install src; cd src
git clone https://github.com/isce-framework/isce3.git

cd ~/tools
git clone https://github.com/yunjunz/conda_envs.git
git clone https://github.com/opera-adt/COMPASS.git
git clone https://github.com/opera-adt/s1-reader.git
git clone https://github.com/opera-adt/sentinel1-burst-id.git

git clone https://gitlab.com/s1-etad/s1-etad.git ~/tools/utils/s1-etad
git clone https://github.com/insarlab/MintPy.git
```

#### b. Install dependencies to `opera` enrivonment

```bash
# create a new conda conda environment
conda create --name opera --yes
conda activate opera

# option 1: install isce3 with conda
# set "isce_install_method='conda'" in conda_envs/opera/config.rc file
# remove mintpy dependencies as it leads to the error below:
# ImportError: dlopen(/Users/yunjunz/tools/miniconda3/envs/opera/lib/python3.9/site-packages/pybind_isce3.cpython-39-darwin.so, 2): Library not loaded: @rpath/libhdf5_cpp.103.dylib
cd ~/tools
mamba install -c avalentino --file conda_envs/opera/requirements.txt isce3

# option 2: install isce3 from source (for Linux only)
# set "isce_install_method='source'" in conda_envs/opera/config.rc file [default]
cd ~/tools
mamba install -c avalentino --file conda_envs/opera/requirements.txt --file MintPy/requirements.txt --file conda_envs/isce3/requirements.txt
```

Close and restart the shell for changes to take effect.

#### c. Build and install `isce3` from source

For option 2 (install the development version of isce3 from source) ONLY.

```bash
conda activate opera

# load CUDA module on kamb
module load cuda/11.2
CUDACXX=/usr/local/cuda-11.2/bin/nvcc
CUDAHOSTCXX=${CXX}

# run cmake
# before re-run, delete existing contents in build folder
# add "-DWITH_CUDA=OFF" to turn off the default CUDA compilation
cd ~/tools/isce3/build
cmake -DCMAKE_INSTALL_PREFIX=../install ../src/isce3/

# compile, install and test
# add `make VERBOSE=1` to see details if run into errors.
make all -j 16 # use multiple threads to accelerate
make install
ctest
```

#### d. Setup

Create an alias in `~/.bash_profile` file for easy activation.

```bash
alias load_opera='conda activate opera; source ~/tools/conda_envs/opera/config.rc'
```

#### e. Test the installation

For option 2 (install the development version of isce3 from source) ONLY.

```bash
insar.py --help    # isce3
```
