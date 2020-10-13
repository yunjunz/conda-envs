# conda_envs

Setup InSAR data processing codes on Linux / macOS.

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup conda
# for Linux, use Miniconda3-latest-Linux-x86_64.sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
chmod +x Miniconda3-latest-MacOSX-x86_64.sh
./Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

### 2. Install ISCE-2, ARIA-tools, MintPy and PyAPS to `insar` environment

Setup environment variables by:

+ adjusting the `use_isce_conda` value in `conda_envs/insar/config.rc` according to the installation option.
+ sourcing the [`conda_envs/insar/config.rc`](./insar/config.rc) file, e.g. in [`~/.bash_profile`](./bash_profile.md) file.

Run the following to download the source code and to install their dependencies.

```bash
# install the latest git, if the current git version < 2.0
conda install git

# download source code
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/isce-framework/isce2.git

# use PyCuAmpcor branch rebased from Lijun's bugfixed version (temporary)
cd isce2
git checkout pycuampcor

cd ~/tools
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/pyaps3.git $PYAPS_HOME/pyaps3

git clone https://github.com/yunjunz/conda_envs.git
source ~/tools/conda_envs/insar/config.rc

# create new environment "insar"
# OR skip to install into "base" environment
conda create --name insar
conda activate insar

# install pre-requisites
# notes on compilers: https://docs.conda.io/projects/conda-build/en/latest/resources/compiler-tools.html
# for macOS, use gfortran_osx-64 clang_osx-64 clangxx_osx-64
# for Linux, use gfortran_linux-64 gcc_linux-64 gxx_linux-64
conda config --add channels conda-forge

# opt 1: install isce-2 with conda (for macOS and Linux)
# set "use_isce_conda=1" in conda_envs/insar/config.rc file
conda install gfortran_linux-64 isce2 nbdime --file conda_envs/insar/requirements4aria.txt --file MintPy/docs/conda.txt

# opt 2: install isce-2 with conda and from source (for Linux on kamb only)
# set "use_isce_conda=0" in conda_envs/insar/config.rc file
conda install --file conda_envs/isce/requirements.txt --file conda_envs/insar/requirements4aria.txt --file MintPy/docs/conda.txt

$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3

##############################
# build and install isce2 (for opt 2 ONLY)
# run cmake
# before re-run, delete existing contents in build folder
# use GCC-7.3.1 installed by Lijun (the old CUDA-10.1 version does not like GCC-7.5; GCC-7.3.0 also does not work, do not know why)
# use "-DCMAKE_BUILD_TYPE=Release" flag, otherwise it is Debug mode by default and will dump intermediate results and slow down (11 mins vs. 24 secs)
cd ~/tools/isce2/build
export GCC_BIN=/net/kraken/home1/geomod/apps/rhel7/gcc7/bin
CC=${GCC_BIN}/gcc CXX=${GCC_BIN}/g++ FC=${GCC_BIN}/gfortran cmake -DCMAKE_INSTALL_PREFIX=~/tools/isce2/install -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release ~/tools/isce2/src/isce2

# compile and install
# then under the $ISCE_ROOT/install, there should be `bin` and `packages` folder
make -j 16 # use multiple threads to accelerate
make install
```

Run the following to test the installation:

```bash
topsApp.py -h
cuDenseOffsets.py -h   #for opt 2 only
ariaDownload.py -h
smallbaselineApp.py -v
```

### 3. Install FRInGE to the `fringe` environment

Modify the install directory in **conda_envs/fringe/config.rc** file.

```bash
# create new environment
conda create --name fringe
conda activate fringe

# install pre-requisites
conda install --yes --file tools/conda_envs/fringe/requirements.txt

# install from source code
source conda_envs/fringe/config.rc
mkdir FRInGE; cd FRInGE
mkdir install build src

cd src
git clone https://github.com/isce-framework/fringe.git

cd ../build
cmake -DCMAKE_INSTALL_PREFIX=../install ../src/fringe
make -j 8 all
make install
```

Test installation:

```bash
sequential.py -h
```
