# GuideLog07
This is a maintained log for solving common errors while installing & running the softwares.

## Installing OpenMolcas in paramshivay iit bhu

 ``` Open the terminal and type in the commands
module load ohpc
module load intel/2020.2.254
module load conda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/apps/bio_tools/conda/lib
module load hdf5-1.12.0
./home/apps/spack/share/spack/setup-env.sh
spack load intel-oneapi-mkl@2021.3.0
module load gcc/10.2.0
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/$USER/slurm-library:/usr/lib64
export FC=ifort
module load cmake_3.14.1

git clone https://gitlab.com/Molcas/OpenMolcas.git 
cd OpenMolcas 
mkdir build
cd build
cmake -DLINALG=MKL -D HDF5=OFF -D CMAKE_INSTALL_PREFIX=/path/to/installation/ ../
make
make install
cd /path/to/installation/
cp pymolcas bin/
```
* set the path of MOLCAS upto build directory.
```
vi ~/.bashrc
export MOLCAS=/path/to/installation/
source ~/.bashrc
 ```

## Installing Sharc 3.x.x in paramshivay iit bhu

 ``` Open the terminal and type in the commands
module load ohpc
module load intel/2020.2.254
module load conda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/apps/bio_tools/conda/lib
module load hdf5-1.12.0
./home/apps/spack/share/spack/setup-env.sh
spack load intel-oneapi-mkl@2021.3.0
module load gcc/10.2.0
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/$USER/slurm-library:/usr/lib64
export FC=ifort
```
* Download the software from https://github.com/sharc-md/sharc/releases/ and follow the following steps
  * First go to source directory and open Makefile
  * keep ```USE_PYSHARC := false, USE_COMPILER := gnu, USE_LIBS := mkl,  ANACONDA := /home/apps/bio_tools/conda ``` and run run ```make install``` in source/ as shown below.
  ```
  USE_PYSHARC := false

   #intel, gnu
   USE_COMPILER := gnu

   #mkl,gnu
   USE_LIBS := mkl

   #Static libraries
   COMP_STATIC := false

   #needed for PYSHARC
   ANACONDA := /home/apps/bio_tools/conda
  # =======================================```
 * After this run ``` make clean``` in source, set USE_PYSHARC to true and run ```make install``` in pysharc/.
 * go to bin and check whether all the binaries and python scripts are present or not
 * set the path to bin in your .bashrc and source it to use.
     
## Installing BAGEL on a Compute Server (28/05/2025)
### 1. Load Intel MPI and Compilers
Before starting, load the Intel environment by sourcing the appropriate script: 

```
. /opt/compilers/intel/setvars.sh
```
### 2. Install Boost Libraries
Download and install Boost (version 1.88.0):
```
wget https://archives.boost.io/release/1.88.0/source/boost_1_88_0.tar.gz
```
Installation Steps:
```
tar -xzf boost_1_88_0.tar.gz
cd boost_1_88_0
./bootstrap.sh
./b2 install --prefix=/opt/SOFTWARES/bagel/boost_1_88_0/boost_installed
```
Add the following lines to your .bashrc or execute them in the terminal:
```
export BOOST_ROOT=/opt/SOFTWARES/bagel/boost_1_88_0/boost_installed
export LD_LIBRARY_PATH=$BOOST_ROOT/lib:$LD_LIBRARY_PATH
```
### 3. Download BAGEL Source Code
Clone the BAGEL GitHub repository:
```
git clone https://github.com/qsimulate-open/bagel.git
cd bagel
```
### 4.Set Environment Variables for Compilation
Before proceeding, set the required compiler and MPI-related environment variables:

```
export CC=mpicc
export CXX=mpicxx
export FC=mpiifort
export I_MPI_FABRICS=ofi
export FI_PROVIDER=shm
export I_MPI_PIN=0
export I_MPI_DEBUG=0
```
### 5.Build and Install BAGEL
```
./autogen.sh
mkdir obj
mkdir bagel_installed
cd obj

../configure \
  --prefix=/opt/SOFTWARES/bagel/bagel/bagel_installed \
  CXXFLAGS="-DNDEBUG -O3 -mavx" \
  --enable-mkl \
  --with-boost=$BOOST_ROOT \
  --with-mpi=intel

make -j4
make install
```
## 🧪 SHARC 4.0 Installation Guide (Compute Server) (28/05/2025)

### 1. Load Intel Compilers and Set Environment Variables
```
# Load Intel compiler environment
source /opt/compilers/intel/setvars.sh

# Set MPI compiler variables
export CC=mpicc
export CXX=mpicxx
export FC=mpiifort
export I_MPI_FABRICS=ofi
export FI_PROVIDER=shm
export I_MPI_PIN=0
export I_MPI_DEBUG=0

```
### 2. Create and Set Up the Conda Environment
```
# Create a conda environment with required packages
conda create -n sharc4.0 -c conda-forge python=3.12 numpy scipy h5py matplotlib \
pyparsing netcdf4 gfortran_linux-64 pyscf openmm numba sympy pyyaml pytorch pytest ase \
opt_einsum threadpoolctl

# Activate the environment
conda activate sharc4.0

# Install additional dependencies
conda install -n sharc4.0 libzip openssl

```
### 3. Download and Build SHARC 4.0

```
# Download the SHARC 4.0 source code
wget https://github.com/sharc-md/sharc4/archive/refs/tags/v4.0.tar.gz

# Extract the archive
tar -xvf v4.0.tar.gz
cd sharc4-4.0

```
### 4. Compile pysharc and Fortran Sources
```
# Install pysharc Python bindings
cd pysharc
make install

# Compile core SHARC source code
cd ../source
make install

# Compile the wfoverlap module
cd ../wfoverlap/source
make

```
### ✅ Installation Complete — SHARC 4.0 is now ready for use in your compute server

## 🤖 Machine Learning Interfaces for SHARC 4.0 

SHARC 4.0 supports two machine learning-based interfaces — SPaiNN and SchNarc — for nonadiabatic dynamics using neural network potentials. These two interfaces are mutually exclusive because they depend on incompatible versions of the schnetpack library.
#### Option 1: Installing the SPaiNN Interface

Activate the sharc4.0 environment and install the SPaiNN repository
```
conda activate sharc4.0

git clone https://github.com/CompPhotoChem/SPaiNN.git
cd SPaiNN && pip install .
```
#### Installing the SchNarc Interface

To use the SchNarc interface, install schnetpack 1, clone the repository, and install it:

```
pip install schnetpack==1.0.1
git clone https://github.com/schnarc/SchNarc.git
cd SchNarc && pip install .
```

#### Note that the SPaiNN and SchNarc interfaces are mutually exclusive, since they require different versions of SchNetPack!
#### ⚠️ Important:
You cannot use SPaiNN and SchNarc simultaneously in the same environment due to conflicting schnetpack versions. Choose the one that fits your workflow and install accordingly.
