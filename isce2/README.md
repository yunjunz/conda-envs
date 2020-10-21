## Install ISCE-2 dev version to an independent environment

### 1. [Install conda](../master/README.md#1-install-conda)

### 2. Install MintPy and ISCE-2 to `isce2_{suffix}` environment

Below we use `alos2` feature branch as an example:
+ install the code in `~/tools/isce2_alos2` dir
+ setup the environment in `~/tools/conda_envs/isce2/config_alos2.rc` file
+ create an alias `load_isce2_alos2` in `~/.bash_profile` file for easy activattion

#### Setup

+ copy & rename the config file via `cd ~/tools/conda_envs/isce2; cp config.rc config_alos2.rc`
+ change `ISCE_ROOT` to `~/tools/isce2_alos2` in the `config_alos2.rc` file.
+ add the following to `~/.bash_profile`, so that one could run `load_isce2_alos2` to load the environment.

```bash
alias load_isce2_alos2='conda activate isce2_alos2; source ~/tools/conda_envs/isce2/config_alos2.rc'
```

#### Build and install

```bash
export SUFFIX=alos2      #CHANGE THIS for each feature environment

# download source file
cd ~/tools
mkdir isce2_${SUFFIX}; cd isce2_${SUFFIX}
mkdir build install src; cd src
git clone https://github.com/yunjunz/isce2.git; cd isce2

# checkout feature branch where your code of interest is, i.e. pycuampcor, alos2, etc.
git checkout ${SUFFIX}

cd ~/tools
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/conda_envs.git
source ~/tools/conda_envs/isce2/config_${SUFFIX}.rc

# create new environment
conda create --name isce2_${SUFFIX}
conda activate isce2_${SUFFIX}

# install pre-requisites
conda install -c conda-forge --file conda_envs/isce2/requirements.txt --file MintPy/docs/conda.txt
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git

# run cmake
# before re-run, delete existing contents in build folder
# use GCC-7.3.1 installed by Lijun (the old CUDA-10.1 version does not like GCC-7.5; GCC-7.3.0 also does not work, do not know why)
# use -DCMAKE_BUILD_TYPE=Release flag, otherwise it is Debug mode by default and will dump intermediate results and slow down (11 mins vs. 24 secs)
cd ~/tools/isce2_${SUFFIX}/build
export GCC_BIN=/net/kraken/home1/geomod/apps/rhel7/gcc7/bin
CC=${GCC_BIN}/gcc CXX=${GCC_BIN}/g++ FC=${GCC_BIN}/gfortran cmake -DCMAKE_INSTALL_PREFIX=~/tools/isce2_${SUFFIX}/install -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release ~/tools/isce2_${SUFFIX}/src/isce2

# compile and install
# then under the $ISCE_ROOT/install, there should be `bin` and `packages` folder
make -j 16 # use multiple threads to accelerate
make install
```
