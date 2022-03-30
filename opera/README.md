## Install the development environment for OPERA project

This recipe works only on Linux (with pre-installed GPU on Caltech/Kamb). isce3 installation on macOS failed for me.

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install `isce3` and `opera-adt` packages

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
git clone https://gitlab.com/s1-etad/s1-etad.git
```

#### b. Install dependencies to `opera` enrivonment

```bash
# create a new conda conda environment
conda create --name opera --yes
conda activate opera

# install dependencies
cd ~/tools
mamba install -c avalentino --file conda_envs/opera/requirements.txt --file s1-reader/requirements.txt --file COMPASS/requirements.txt
```

Close and restart the shell for changes to take effect.

#### c. Build and install `isce3` from source

```bash
# load CUDA and compilers module on kamb
module load cuda/11.2

# run cmake
# before re-run, delete existing contents in build folder
cd ~/tools/isce3/build
CUDAHOSTCXX=$CXX CUDACXX=/usr/local/cuda/bin/nvcc cmake -DCMAKE_INSTALL_PREFIX=../install ../src/isce3/ -DWITH_CUDA=ON

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

```bash
insar.py --help    # isce3
```
