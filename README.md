# conda_envs

Setup InSAR data processing codes on Linux / macOS.

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools

# download, install and setup (mini/ana)conda
# for Linux, use Miniconda3-latest-Linux-x86_64.sh
# for macOS, opt 2: curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -o Miniconda3-latest-MacOSX-x86_64.sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
chmod +x Miniconda3-latest-MacOSX-x86_64.sh
./Miniconda3-latest-MacOSX-x86_64.sh -b -p ~/tools/miniconda3
~/tools/miniconda3/bin/conda init bash
```

Close and restart the shell for changes to take effect.

```
conda install wget git --yes
conda config --add channels conda-forge
```

### 2. Install ISCE-2, ARIA-tools and MintPy to `insar` environment

#### Download source code

```bash
cd ~/tools
mkdir isce2; cd isce2
mkdir build install src; cd src
git clone https://github.com/isce-framework/isce2.git

cd ~/tools
git clone https://github.com/insarlab/MintPy.git
git clone https://github.com/insarlab/PySolid.git
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://github.com/bakerunavco/SSARA.git ~/tools/utils/SSARA
git clone https://github.com/yunjunz/PyAPS.git
git clone https://github.com/yunjunz/conda_envs.git
```

#### Create `insar` environment and install pre-requisites

```bash
# create new environment
conda create --name insar
conda activate insar

# opt 1: install isce-2 with conda (for macOS and Linux)
# set "use_isce_conda=1" in conda_envs/insar/config.rc file
conda install --yes --file conda_envs/insar/requirements.txt --file MintPy/docs/conda.txt isce2 

# opt 2: install isce-2 from source (for Linux on kamb only)
# set "use_isce_conda=0" in conda_envs/insar/config.rc file
conda install --yes --file conda_envs/insar/requirements.txt --file MintPy/docs/conda.txt --file conda_envs/isce2/requirements.txt

# install dependencies not available from conda
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
$CONDA_PREFIX/bin/pip install git+https://github.com/tylere/pykml.git
$CONDA_PREFIX/bin/pip install scalene      # CPU, GPU and memory profiler
$CONDA_PREFIX/bin/pip install ipynb        # import functions from ipynb files

# compile PySolid
cd ~/tools/PySolid/pysolid
f2py -c -m solid solid.for
```

#### Build and install ISCE-2 from source

For opt 2 (building and installing the development version of ISCE-2 from source) ONLY.

```bash
# load CUDA and compilers module on kamb
# only gcc=7.3.1 works, but not available from conda-forge: https://anaconda.org/conda-forge/gcc_linux-64/files?type=conda
module load cuda/10.1
module load /home/geomod/apps/rhel7/modules/gcc/7.3.1

# generate build system
# before re-run, delete existing contents in build folder
# use GCC-7.3.1 installed by Lijun (the old CUDA-10.1 version does not like GCC-7.5; GCC-7.3.0 also does not work, do not know why)
# more notes on https://github.com/lijun99/isce2-install
cd ~/tools/isce2/build
export GCC_BIN=/net/kraken/home1/geomod/apps/rhel7/gcc7/bin
cmake ~/tools/isce2/src/isce2 -DCMAKE_INSTALL_PREFIX=~/tools/isce2/install -DCMAKE_CUDA_FLAGS="-arch=sm_60" -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=${GCC_BIN}/gcc -DCMAKE_CXX_COMPILER=${GCC_BIN}/g++ -DCMAKE_Fortran_COMPILER=${GCC_BIN}/gfortran

# compile and install
# then under the $ISCE_ROOT/install, there should be `bin` and `packages` folder
make -j 16 # use multiple threads to accelerate
make install
```

#### Setup

For opt 2 (building and installing the development version of ISCE-2 from source): 

+ set `use_isce_conda=0` in `conda_envs/insar/config.rc` file.

Create an alias `load_insar` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_insar='conda activate insar; source ~/tools/conda_envs/insar/config.rc'
```

#### Test the installation

Run the following to test the installation:

```bash
load_insar
topsApp.py -h
cuDenseOffsets.py -h   #for opt 2 only
ariaDownload.py -h
smallbaselineApp.py -v
```

Run the following for CPU and memory profiler via [`scalene`](https://github.com/emeryberger/scalene) of python script, e.g. `ifgram_inversion.py`:

```bash
scalene --reduced-profile ~/tools/MintPy/mintpy/ifgram_inversion.py ~/data/test/FernandinaSenDT128/mintpy/inputs/ifgramStack.h5 -w no
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
load_fringe
sequential.py -h
```
