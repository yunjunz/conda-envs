# conda_envs

Setup InSAR data processing codes on Linux / macOS.

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup conda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
chmod +x Miniconda3-latest-MacOSX-x86_64.sh
./Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

### 2. Install ISCE-2, ARIA-tools, MintPy and PyAPS to `insar` environment

Setup environment variables by sourcing the [`conda_envs/insar/config.rc`](./insar/config.rc) file, e.g. in [`~/.bash_profile`](./bash_profile.md) file.

Run the following to download the source code and to install their dependencies.

```bash
# install the latest git, if the current git version < 2.0
$CONDA_PREFIX/bin/conda install git

# download source code
cd ~/tools
git clone https://github.com/isce-framework/isce2.git
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/yunjunz/pyaps3.git $PYAPS_HOME/pyaps3

git clone https://github.com/yunjunz/conda_envs.git
source ~/tools/conda_envs/insar/config.rc

# create new environment "insar"
# OR skip to install into "base" environment
$CONDA_PREFIX/bin/conda create --name insar
$CONDA_PREFIX/bin/conda activate insar

# install pre-requisites
# gfortran_osx/linux-64 for solid earth tide in MintPy
$CONDA_PREFIX/bin/conda config --add channels conda-forge
$CONDA_PREFIX/bin/conda install gfortran_linux-64 --file conda_envs/insar/requirements.txt --file MintPy/docs/conda.txt --file ARIA-tools/requirements.txt
$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git
```

Run the following to test the installation:

```bash
topsApp.py -h
ariaDownload.py -h
smallbaselineApp.py -v
```

### 3. Install ISCE-2 with GPU support to `isce_gpu` environment

```bash
# download source file
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/yunjunz/isce2.git

# use PyCuAmpcor branch rebased from Lijun's bugfixed version (temporary)
cd isce2
git checkout pycuampcor

cd ~/tools
git clone https://github.com/yunjunz/conda_envs.git
source ~/tools/conda_envs/isce_gpu/config.rc

# create new environment
$CONDA_PREFIX/bin/conda create --name isce_gpu
$CONDA_PREFIX/bin/conda activate isce_gpu

# install pre-requisites
$CONDA_PREFIX/bin/conda install -c conda-forge --file conda_envs/isce_gpu/requirements.txt
# create a symlink for cython
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3

# run cmake
# before re-run, delete existing contents in build folder
# use GCC-7.3.1 installed by Lijun (the old CUDA-10.1 version does not like GCC-7.5; GCC-7.3.0 also does not work, do not know why)
# use -DCMAKE_BUILD_TYPE=Release flag, otherwise it is Debug mode by default and will dump intermediate results and slow down (11 mins vs. 24 secs)
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
cuDenseOffsets.py -h
```

### 4. Install FRInGE to the `fringe` environment

Modify the install directory in **conda_envs/fringe/config.rc** file.

```bash
# create new environment
conda create --name fringe
conda activate fringe

# install pre-requisites
$CONDA_PREFIX/bin/conda install --yes --file tools/conda_envs/fringe/requirements.txt

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
