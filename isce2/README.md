## Install ISCE-2 development version(s) from source and MintPy

### 1. [Install conda](../README.md#1-install-conda)

### 2. Install MintPy and ISCE-2 to `isce2` environment

#### a. Download

```bash
# download source file
cd ~/tools
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/conda_envs.git

cd ~/tools
mkdir -p isce2; cd isce2
mkdir -p src build; cd src
git clone https://github.com/yunjunz/isce2.git
```

#### b. Create `isce2` environment and install pre-requisites

```bash
# create new environment
conda create --name isce2 --yes
conda activate isce2

# install pre-requisites
cd ~/tools
mamba install -c conda-forge --file conda_envs/isce2/requirements.txt --file MintPy/requirements.txt --yes
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
```

#### c. Build and install feature branch to `isce2/install_$version$`

```bash
#CHANGE THIS for each feature environment
export ISCE_VERSION='_pycuampcor'  #'_pycuampcor', '_dev'

# checkout feature / default main branch where your code of interest is, i.e. pycuampcor, alos2, main, etc.
cd ~/tools/isce2/src/isce2
git checkout pycuampcor

# create install directory
cd ~/tools/isce2
mkdir install${ISCE_VERSION}

# load CUDA module on kamb
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

Define environment variable `ISCE_VERSION` and create an alias `load_isce2${ISCE_VERSION}` in `~/.bash_profile` file for easy activattion, _e.g._:

```bash
alias load_isce2_dev='export ISCE_VERSION="_dev"; conda activate isce2; source ~/tools/conda_envs/isce2/config.rc'
```

#### e. Test the installation

```bash
topsApp.py -h
cuDenseOffsets.py -h
```
