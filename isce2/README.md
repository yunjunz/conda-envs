## Install isce2 (development version) and mintpy (development version)

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install ISCE-2 and MintPy to `isce2` environment

#### a. [Download source code](../README.md#a-download-source-code)

#### b. Install dependencies to `isce2` environment

```bash
# create new environment
conda create --name isce2 --yes
conda activate isce2

# install dependencies
cd ~/tools
mamba install --file conda_envs/insar/requirements.txt --file MintPy/requirements.txt --file conda_envs/isce2/requirements.txt --yes
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3

# install mintpy
python -m pip install -e MintPy
```

#### c. Install ISCE-2 to `isce2/install_*` folder

```bash
# CHANGE THIS for each feature branch
export ISCE_VERSION='_pycuampcor'  # '_pycuampcor', '_dev', '_py310', etc.

# [optional] checkout feature / default branch where your code of interest is, i.e. pycuampcor, alos2, main, etc.
cd ~/tools/isce2/src/isce2
git checkout pycuampcor

# create install directory
cd ~/tools/isce2
mkdir install${ISCE_VERSION}

# load CUDA module [on kamb]
module load cuda/11.2

# run cmake
# before re-run, delete existing contents in build folder
# more notes on https://github.com/lijun99/isce2-install
cd ~/tools/isce2/build
cmake ~/tools/isce2/src/isce2 -DCMAKE_INSTALL_PREFIX=~/tools/isce2/install${ISCE_VERSION} -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release

# compile and install
# then under the ~/tools/isce2/install${ISCE_VERSION}, there should be `bin` and `packages` folder
# add `make VERBOSE=1` to see details if run into errors.
make -j 16 # use multiple threads to accelerate
make install
```

#### d. Setup

Define environment variable `ISCE_VERSION` and create an alias `load_isce2${ISCE_VERSION}` in `~/.bash_profile` file for easy activattion:

```bash
alias load_isce2_dev='export ISCE_VERSION="_dev"; conda activate isce2; source ~/tools/conda_envs/isce2/config.rc'
```

#### e. Test the installation

```bash
topsApp.py -h
cuDenseOffsets.py -h
smallbaselineApp.py -h
```
