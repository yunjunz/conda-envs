## Code installation for the [2023 EarthScope short course (ISCE+)](https://www.earthscope.org/event/2023-insar-isce-short-course/)

### 1. Install conda

```bash
mkdir -p ~/tools; cd ~/tools
# for macOS, use Mambaforge-MacOSX-x86_64.sh, and optionally use `curl -L -O https://...` syntax to download
wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh
bash Mambaforge-Linux-x86_64.sh -b -p ~/tools/mambaforge
~/tools/mambaforge/bin/mamba init bash
```

Close and restart the shell for changes to take effect. Then install the following utilities:

```bash
mamba install wget git tree --yes
```

### 2. Install ISCE-2, ARIA-tools and MintPy to the `earthscope` environment

#### a. Download source code

```bash
cd ~/tools
git clone https://github.com/isce-framework/isce2.git isce2/src/isce2
git clone https://github.com/aria-tools/ARIA-tools.git
git clone https://www.unavco.org/gitlab/unavco_public/ssara_client.git utils/SSARA
git clone https://github.com/yunjunz/conda_envs.git
```

#### b. Install dependencies

```bash
# create new environment
mamba create --name earthscope --yes
mamba activate earthscope

# install dependencies
cd ~/tools
mamba install --file conda_envs/earthscope/requirements.txt --file ARIA-tools/requirements.txt --yes

# install dependencies not available on conda-forge
ln -s ${CONDA_PREFIX}/bin/cython ${CONDA_PREFIX}/bin/cython3
python -m pip install ipynb    # import functions from *.ipynb files
```

#### c. Setup

Create an alias `load_earthscope` in `~/.bash_profile` file for easy activation, _e.g._:

```bash
alias load_earthscope='conda activate earthscope; source ~/tools/conda_envs/earthscope/config.rc'
```

#### d. Test the installation

```bash
load_earthscope            # wram up the conda environment
topsApp.py -h              # test ISCE-2
ariaDownload.py -h         # test ARIA-tools
smallbaselineApp.py -h     # test MintPy
```

## ISCE2/topsStack demonstration

### 1. Installation

```bash
# load the conda environment
conda activate unavco

# setup path for topsStack
export PATH=${PATH}:${ISCE_STACK}/topsStack

# test
stackSentinel.py -h
```

### 2. Setup auxiliary directory

```bash
cd ~/bak
mkdir aux; cd aux
mkdir aux_cal aux_poeorb
```

### 3. Download S1 data from ASF using the web browser or SSARA

SNWE: 32.7,35,-117,-114.5

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
mkdir SLC; cd SLC
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --print
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --kml
~/tools/utils/SSARA/ssara_federated_query.py -p SENTINEL-1A,SENTINEL-1B -r 173 -b '32.7,35,-117,-114.5' --download --parallel=4
```

### 4. DEM

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
mkdir DEM; cd DEM
sardem --bbox -118 31 -113 36 --data COP -isce
# sardem --bbox -118 31 -113 36 --data COP -isce -o copernicus.dem   # pre-stage
# equivalent to $ISCE_HOME/applications/dem.py
```

### 5. Run stackSentinel.py

NOTE: same SNWE/AOI/bbox should be used for "3. data search/download" and "5. run"

```bash
cd ~/data-marmot/test/SaltonSeaSenDT173
stackSentinel.py -s ./SLC -o ~/bak/aux/aux_poeorb -a ~/bak/aux/aux_cal --dem ./DEM/copernicus.dem --coregistration geometry -n '1 2 3' --bbox '32.7 35 -117 -114.5' -W interferogram -c 3 --azimuth_looks 5 --range_looks 15 -f 0.5 -u snaphu
```

Show the `run_files` and `configs` folder, and how to execute the run files.

Show the final results in `~/data-marmot/offset4motion/SaltonSeaSenDT173/merged`. This point is equivalent to the ariaDownload/TSsetup result.

Show the mintpy input example on readthedocs website.
