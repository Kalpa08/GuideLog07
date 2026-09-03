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

## 🤖 Machine Learning Interfaces for SHARC 4.0 (28/05/2025)

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

# Installing SHARC 3.0.2 on Param Rudra

This document provides a complete, reproducible procedure for installing **SHARC 3.0.2 with PySHARC** on the **Param Rudra** supercomputer.

The installation is performed in the user's home directory and does not require administrator privileges.

The procedure uses:

- GNU compiler for compiling SHARC
- Intel oneAPI MKL for BLAS/LAPACK libraries
- Miniconda3 for the PySHARC Python environment
- Python 3.9 for PySHARC
- Conda-forge packages for the required Python, NetCDF, and HDF5 dependencies

---

## Table of Contents

1. [Installation Directory Structure](#1-installation-directory-structure)
2. [Load the Required Modules](#2-load-the-required-modules)
3. [Install Miniconda3](#3-install-miniconda3)
4. [Create the PySHARC Conda Environment](#4-create-the-pysharc-conda-environment)
5. [Download SHARC 3.0.2](#5-download-sharc-302)
6. [Compile the Standard SHARC Binaries](#6-compile-the-standard-sharc-binaries)
7. [Enable PySHARC](#7-enable-pysharc)
8. [Compile and Install PySHARC](#8-compile-and-install-pysharc)
9. [Configure the SHARC Environment](#9-configure-the-sharc-environment)
10. [Verify the Installation](#10-verify-the-installation)
11. [Complete Command Sequence](#11-complete-command-sequence)
12. [Why These Modules and Versions Are Used](#12-why-these-modules-and-versions-are-used)
13. [Why GCC Is Used](#13-why-gcc-is-used)
14. [Why a Separate Conda Environment Is Used](#14-why-a-separate-conda-environment-is-used)
15. [Installing Multiple SHARC Versions](#15-installing-multiple-sharc-versions)
16. [Using SHARC After Installation](#16-using-sharc-after-installation)
17. [Troubleshooting](#17-troubleshooting)
18. [Optional SHARC Tests](#18-optional-sharc-tests)

---

# 1. Installation Directory Structure

All SHARC versions should be installed under:

```text
~/SOFTWARES/SHARC/
```

For example, after installing SHARC 3.0.2:

```text
~/SOFTWARES/
├── miniconda3/
│
└── SHARC/
    └── sharc-3.0.2/
        ├── bin/
        ├── lib/
        ├── pysharc/
        ├── source/
        └── ...
```

If another version of SHARC is installed later, it should be placed in its own directory:

```text
~/SOFTWARES/SHARC/
├── sharc-3.0.2/
├── sharc-4.0.0/
└── ...
```

This allows different SHARC versions to coexist without overwriting one another.

Separate Conda environments can also be created for different SHARC versions:

```text
pysharc_3.0.2
pysharc_4.0.0
```

---

# 2. Load the Required Modules

Before compiling SHARC, load the required Param Rudra modules:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0
```

Set GCC as the C compiler:

```bash
export CC=gcc
```

It is useful to check the compiler:

```bash
which gcc
gcc --version

which gfortran
gfortran --version
```

> **Important:** `compiler/oneapi2024/mkl/2024.0` should only be loaded once.
>
> TBB is loaded before MKL because the MKL module depends on TBB in the Param Rudra environment.

---

# 3. Install Miniconda3

PySHARC requires Python and several scientific Python libraries. A separate Miniconda installation is therefore used.

Go to the software directory:

```bash
cd ~/SOFTWARES
```

Download Miniconda3:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

Run the installer:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

Follow the instructions displayed by the installer.

After the installation is complete, reload the shell configuration:

```bash
source ~/.bashrc
```

Disable automatic activation of the Conda `base` environment:

```bash
conda config --set auto_activate_base false
```

Verify the Conda installation:

```bash
conda --version
```

---

# 4. Create the PySHARC Conda Environment

Create a dedicated Conda environment for SHARC 3.0.2:

```bash
conda create -n pysharc_3.0.2 -c conda-forge \
python=3.9 numpy scipy h5py six matplotlib \
python-dateutil pyyaml pyparsing kiwisolver cycler \
netcdf4 hdf5 h5utils gfortran_linux-64
```

Activate the environment:

```bash
conda activate pysharc_3.0.2
```

Verify the Python version and environment location:

```bash
python --version
echo $CONDA_PREFIX
```

The Python version should be:

```text
Python 3.9.x
```

and `$CONDA_PREFIX` should point to the newly created environment.

For example:

```text
/home/<username>/SOFTWARES/miniconda3/envs/pysharc_3.0.2
```

## Why are these packages installed?

The environment contains the Python and scientific libraries required by PySHARC, including:

- `numpy`
- `scipy`
- `h5py`
- `matplotlib`
- `netcdf4`
- `hdf5`
- `hdf5-related utilities`
- `python-dateutil`
- `pyyaml`
- `pyparsing`
- `kiwisolver`
- `cycler`
- `six`

The Conda environment also provides the NetCDF/HDF5 dependencies needed for the PySHARC build, so an additional system-wide NetCDF installation is not required for this installation.

---

# 5. Download SHARC 3.0.2

Create the SHARC installation directory:

```bash
mkdir -p ~/SOFTWARES/SHARC
```

Move to the directory:

```bash
cd ~/SOFTWARES/SHARC
```

Download the SHARC 3.0.2 source:

```bash
wget https://github.com/sharc-md/sharc/archive/refs/tags/v3.0.2.tar.gz
```

Extract the source:

```bash
tar -xzf v3.0.2.tar.gz
```

The extracted directory should be:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/
```

The downloaded archive can be removed after extraction:

```bash
rm v3.0.2.tar.gz
```

Check the directory:

```bash
ls ~/SOFTWARES/SHARC/
```

Expected:

```text
sharc-3.0.2
```

---

# 6. Compile the Standard SHARC Binaries

Go to the SHARC source directory:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
```

Open the Makefile:

```bash
nano Makefile
```

Set the following options:

```make
USE_PYSHARC := false
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

### Meaning of the settings

| Variable | Value | Purpose |
|---|---|---|
| `USE_PYSHARC` | `false` | Build the standard SHARC binaries first |
| `USE_COMPILER` | `gnu` | Use GNU Fortran |
| `USE_LIBS` | `mkl` | Use Intel MKL for numerical libraries |
| `COMP_STATIC` | `false` | Use dynamic linking |

Save the Makefile and compile SHARC:

```bash
make install
```

After the installation completes, clean the build directory:

```bash
make clean
```

The compiled SHARC executables should now be located in:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/bin/
```

---

# 7. Enable PySHARC

After successfully compiling the standard SHARC binaries, enable PySHARC.

Go back to the source directory:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
```

Open the Makefile:

```bash
nano Makefile
```

Change:

```make
USE_PYSHARC := false
```

to:

```make
USE_PYSHARC := true
```

The relevant section should now look like:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

Save the Makefile.

---

# 8. Compile and Install PySHARC

Activate the dedicated Conda environment:

```bash
conda activate pysharc_3.0.2
```

Make sure GCC is selected:

```bash
export CC=gcc
```

Go to the PySHARC directory:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
```

## 8.1 Test the PySHARC Extension Build

Before running the complete installation, compile the Python extension manually:

```bash
CC=gcc python sharc_setup build_ext --build-lib .
```

This provides a useful compatibility check for the compiler and Python environment.

If the command completes successfully, proceed with the installation.

## 8.2 Install PySHARC

Run:

```bash
make install
```

This installs the PySHARC libraries and Python components into the SHARC installation.

---

# 9. Configure the SHARC Environment

After installation, SHARC provides the `sharcvars.sh` script for setting the required environment variables.

Go to the SHARC `bin` directory:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin
```

Source the environment:

```bash
source sharcvars.sh
```

This sets the SHARC-related environment variables, including:

```text
SHARC
SHARCLIB
PYTHONPATH
LD_LIBRARY_PATH
```

Check the SHARC path:

```bash
echo $SHARC
```

It should point to:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/bin
```

---

# 10. Verify the Installation

Several checks should be performed after installation.

## 10.1 Check the SHARC Version

Run:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin
./sharc.x --version
```

The output should contain:

```text
Version: 3.0
```

along with the corresponding build information.

---

## 10.2 Check PySHARC

Activate the PySHARC environment:

```bash
conda activate pysharc_3.0.2
```

Go to the PySHARC directory:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
```

Run:

```bash
python -c "import sharc; print('PySHARC import OK')"
```

A successful installation should print:

```text
PySHARC import OK
```

---

## 10.3 Check for Missing Shared Libraries

Run:

```bash
ldd sharc/sharc*.so | grep "not found"
```

If the command produces **no output**, there are no unresolved shared-library dependencies.

If `not found` is reported, the corresponding library path needs to be investigated.

---

# 11. Complete Command Sequence

The following is the complete command sequence for the installation.

> **Note:** The SHARC `Makefile` must be edited at the indicated steps. The commands below assume that the required Makefile settings have been entered.

## 11.1 Load the Environment

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0

export CC=gcc
```

## 11.2 Install Miniconda3

```bash
cd ~/SOFTWARES

wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

bash Miniconda3-latest-Linux-x86_64.sh

source ~/.bashrc

conda config --set auto_activate_base false
```

## 11.3 Create the PySHARC Environment

```bash
conda create -n pysharc_3.0.2 -c conda-forge \
python=3.9 numpy scipy h5py six matplotlib \
python-dateutil pyyaml pyparsing kiwisolver cycler \
netcdf4 hdf5 h5utils gfortran_linux-64

conda activate pysharc_3.0.2
```

## 11.4 Download SHARC 3.0.2

```bash
mkdir -p ~/SOFTWARES/SHARC

cd ~/SOFTWARES/SHARC

wget https://github.com/sharc-md/sharc/archive/refs/tags/v3.0.2.tar.gz

tar -xzf v3.0.2.tar.gz

rm v3.0.2.tar.gz
```

## 11.5 Build Standard SHARC

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
```

Edit the Makefile:

```make
USE_PYSHARC := false
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

Then:

```bash
make install
make clean
```

## 11.6 Enable PySHARC

Edit the same Makefile and change:

```make
USE_PYSHARC := false
```

to:

```make
USE_PYSHARC := true
```

The final configuration should be:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

## 11.7 Build PySHARC

```bash
conda activate pysharc_3.0.2

export CC=gcc

cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc

CC=gcc python sharc_setup build_ext --build-lib .

make install
```

## 11.8 Verify PySHARC

```bash
python -c "import sharc; print('PySHARC import OK')"
```

Expected:

```text
PySHARC import OK
```

## 11.9 Configure SHARC

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin

source sharcvars.sh
```

## 11.10 Verify SHARC

```bash
./sharc.x --version
```

## 11.11 Check Shared Libraries

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc

ldd sharc/sharc*.so | grep "not found"
```

---

# 12. Why These Modules and Versions Are Used

The following Param Rudra environment was used successfully for compiling SHARC 3.0.2:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0
```

The SHARC Makefile is configured as:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

The important components are described below.

---

## 12.1 GNU Compiler

SHARC 3.0.2 is compiled using the GNU compiler:

```make
USE_COMPILER := gnu
```

The system GCC/GFortran environment on Param Rudra was found to be suitable for compiling SHARC 3.0.2.

---

## 12.2 Intel MKL 2024.0

The SHARC installation uses:

```make
USE_LIBS := mkl
```

Intel oneAPI MKL provides optimized implementations of numerical libraries such as BLAS and LAPACK, which are required by SHARC.

The module used is:

```bash
module load compiler/oneapi2024/mkl/2024.0
```

Using MKL avoids the need to install a separate BLAS/LAPACK stack.

---

## 12.3 Intel TBB

The module:

```bash
module load compiler/oneapi2024/tbb
```

is loaded before MKL because the MKL module depends on TBB in the Param Rudra environment.

---

## 12.4 Intel Compiler Runtime

The Intel compiler runtime is loaded using:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
```

Although GNU compilers are used to compile SHARC in this setup, the Intel runtime is part of the oneAPI environment used by the MKL stack.

---

## 12.5 Intel Fortran Compiler Module

The Intel Fortran environment is loaded using:

```bash
module load compiler/oneapi2024/ifort/2024.0.2
```

However, the SHARC Makefile explicitly selects the GNU compiler:

```make
USE_COMPILER := gnu
```

Therefore, the SHARC Fortran source is compiled with GNU Fortran rather than Intel Fortran.

The Intel Fortran module is retained as part of the tested oneAPI environment on Param Rudra.

---

## 12.6 Intel MPI

The Intel MPI module:

```bash
module load compiler/oneapi2024/mpi/2021.11
```

provides the Intel MPI environment.

MPI is not the primary requirement for compiling the basic SHARC binaries, but it is included in the tested Param Rudra environment and may be useful for subsequent HPC workflows.

---

# 13. Why GCC Is Used

The following command is important for the PySHARC build:

```bash
export CC=gcc
```

The Conda environment may provide its own C compiler wrapper. For example, Conda can provide a compiler such as:

```text
x86_64-conda-linux-gnu-cc
```

For this SHARC 3.0.2 installation, the system GCC available on Param Rudra was used instead.

Therefore, PySHARC is compiled explicitly with:

```bash
CC=gcc python sharc_setup build_ext --build-lib .
```

This was found to provide a compatible compilation environment for the SHARC 3.0.2 PySHARC extension.

---

# 14. Why a Separate Conda Environment Is Used

A dedicated Conda environment is created:

```text
pysharc_3.0.2
```

instead of installing the PySHARC dependencies into the global Python environment.

This provides several advantages:

1. PySHARC dependencies remain isolated from other projects.
2. Different SHARC versions can use different Python environments.
3. Python package conflicts are minimized.
4. The installation does not require administrator privileges.
5. The environment can be reproduced more easily on another system.

For example:

```text
~/SOFTWARES/miniconda3/envs/
├── pysharc_3.0.2/
├── pysharc_4.0.0/
└── ...
```

---

# 15. Installing Multiple SHARC Versions

Different SHARC versions should be kept in separate directories.

For example:

```text
~/SOFTWARES/SHARC/
├── sharc-3.0.2/
├── sharc-4.0.0/
└── sharc-4.x.x/
```

Installing SHARC 4.0 should therefore not modify:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/
```

Instead, SHARC 4.0 should be extracted into its own directory:

```text
~/SOFTWARES/SHARC/sharc-4.0.0/
```

A separate Conda environment can also be created:

```text
pysharc_4.0.0
```

if required by the new SHARC version.

This makes it possible to maintain multiple SHARC installations simultaneously.

---

# 16. Using SHARC After Installation

When starting a new shell, the required environment must be loaded before running SHARC.

Load the modules:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0
```

Set GCC:

```bash
export CC=gcc
```

Activate the PySHARC environment:

```bash
conda activate pysharc_3.0.2
```

Set the SHARC environment:

```bash
source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh
```

Check the installation:

```bash
echo $SHARC
which python
```

The SHARC path should correspond to:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/bin
```

---

# 17. Troubleshooting

## 17.1 PySHARC compilation fails

Make sure GCC is being used:

```bash
which gcc
gcc --version
```

Then:

```bash
export CC=gcc
```

and retry:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc

CC=gcc python sharc_setup build_ext --build-lib .
```

---

## 17.2 PySHARC cannot be imported

First activate the correct Conda environment:

```bash
conda activate pysharc_3.0.2
```

Then test:

```bash
python -c "import sharc; print('PySHARC import OK')"
```

During installation verification, run the command from:

```text
~/SOFTWARES/SHARC/sharc-3.0.2/pysharc/
```

---

## 17.3 Shared library is missing

Check the PySHARC extension:

```bash
ldd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc/sharc/sharc*.so | grep "not found"
```

If a library is reported as:

```text
not found
```

check that the required Param Rudra modules and the correct Conda environment are active.

---

## 17.4 SHARC executable cannot be found

Make sure the SHARC environment has been sourced:

```bash
source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh
```

Then:

```bash
echo $SHARC
```

and:

```bash
ls $SHARC
```

---

# 18. Optional SHARC Tests

The official SHARC installation also provides a test suite for checking the fundamental functionality of SHARC.

After setting the SHARC environment:

```bash
source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh
```

the basic test suite can be run using:

```bash
$SHARC/tests.py
```

For the `wfoverlap` component, the corresponding test can be run from:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/wfoverlap/source
make test
```

These tests are optional but recommended after completing the installation.

---

# Installation Summary

The final installation consists of the following components:

```text
Param Rudra
│
├── oneAPI environment
│   ├── compiler-rt 2024.0.2
│   ├── ifort 2024.0.2
│   ├── MPI 2021.11
│   ├── TBB
│   └── MKL 2024.0
│
├── Miniconda3
│
├── Conda environment
│   └── pysharc_3.0.2
│
└── SHARC
    └── sharc-3.0.2
        ├── bin/
        ├── lib/
        ├── pysharc/
        └── source/
```

The final SHARC configuration is:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

The C compiler is explicitly set to:

```bash
export CC=gcc
```

A successful installation should pass the following checks:

```bash
./sharc.x --version
```

and:

```bash
python -c "import sharc; print('PySHARC import OK')"
```

The shared-library check should produce no output:

```bash
ldd sharc/sharc*.so | grep "not found"
```

---

## References

- [SHARC GitHub Repository](https://github.com/sharc-md/sharc)
- [SHARC 3.0.2 Release](https://github.com/sharc-md/sharc/releases/tag/v3.0.2)
- [SHARC 3.0.2 Installation Instructions](https://github.com/sharc-md/sharc/blob/v3.0.2/INSTALL)
- [Miniconda Documentation](https://docs.conda.io/)

#### Note that the SPaiNN and SchNarc interfaces are mutually exclusive, since they require different versions of SchNetPack!
#### ⚠️ Important:
You cannot use SPaiNN and SchNarc simultaneously in the same environment due to conflicting schnetpack versions. Choose the one that fits your workflow and install accordingly.
