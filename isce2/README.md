## Install ISCE-2 dev version to an independent environment

### 1. [Install conda](../master/README.md#1-install-conda)

### 2. Install MintPy and ISCE-2 to `isce2${SUFFIX}` environment

#### Build and install

Run the following to build and install ISCE-2 in `~/tools/isce2/install${SUFFIX}` dir.

```bash
#CHANGE THIS for each feature environment
export SUFFIX=''  #'_pycuampcor'

# download source file
cd ~/tools
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/conda_envs.git

cd ~/tools
mkdir -p isce2; cd isce2
mkdir -p src build install${SUFFIX}; cd src
git clone https://github.com/yunjunz/isce2.git; cd isce2
# [optional] checkout feature branch where your code of interest is, i.e. pycuampcor, alos2, etc.
git checkout pycuampcor

# create new environment
conda create --name isce2${SUFFIX} --yes
conda activate isce2${SUFFIX}

# install pre-requisites
cd ~/tools
conda install -c conda-forge --file conda_envs/isce2/requirements.txt --file MintPy/docs/conda.txt --yes
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git
module load cuda/10.1

# run cmake
# before re-run, delete existing contents in build folder
# use -DCMAKE_BUILD_TYPE=Release flag, otherwise it is Debug mode by default and will dump intermediate results and slow down (11 mins vs. 24 secs)
# more notes on https://github.com/lijun99/isce2-install
cd ~/tools/isce2/build
cmake ~/tools/isce2/src/isce2 -DCMAKE_INSTALL_PREFIX=~/tools/isce2/install${SUFFIX} -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=${GCC} -DCMAKE_CXX_COMPILER=${GXX} -DCMAKE_Fortran_COMPILER=${GFORTRAN}

# compile and install
# then under the ~/tools/isce2/install${SUFFIX}, there should be `bin` and `packages` folder
# add `make VERBOSE=1` to see details if run into errors.
make -j 16 # use multiple threads to accelerate
make install
```

#### Setup

If ${SUFFIX} is not empty:
1. copy and rename the config file via `cd ~/tools/conda_envs/isce2; cp config.rc config_pycuampcor.rc`
2. update `SUFFIX` in `config_pycuampcor.rc` file.

Create an alias `load_isce2${SUFFIX}` in `~/.bash_profile` file for easy activattion. Below is an example for `SUFFIX='_pycuampcor'`:

```bash
alias load_isce2_pycuampcor='conda activate isce2_pycuampcor; source ~/tools/conda_envs/isce2/config_pycuampcor.rc'
```

#### Test the installation

```bash
topsApp.py -h
cuDenseOffsets.py -h
```
