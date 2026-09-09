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



# OpenMolcas 2026 MPI Installation on Param Rudra

This document describes the installation of **OpenMolcas 2026 with MPI support** on the **Param Rudra supercomputer**.

The installation uses:

- Intel oneAPI compilers
- Intel MPI
- Intel MKL
- ScaLAPACK
- Global Arrays (GA)
- Python 3.12
- CMake
- MPI-enabled OpenMolcas

The procedure below records the configuration used for a successful installation.

---

## 1. Installation Overview

### Software

| Component | Version / Configuration |
|---|---|
| OpenMolcas | `v26.06-1186-g641cfea24` |
| Intel compiler | oneAPI 2024 |
| Intel MPI | `2021.11` |
| Intel MKL | `2024.0` |
| Global Arrays | Built-in / bundled |
| Python | `3.12` |
| C compiler | `mpiicx` |
| Fortran compiler | `mpiifx` |
| MPI | Intel MPI |
| Linear algebra | Intel MKL |
| HDF5 | Disabled |

---

## 2. Directory Structure

The following directory structure is used:

```text
/home/kalpa.bhu/SOFTWARES/OpenMolcas/
└── OpenMolcas_2026/
    ├── build_mpi/
    ├── OM_install_mpi/
    └── source files
```

### Source directory

```bash
/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026
```

### Build directory

```bash
/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/build_mpi
```

### Installation directory

```bash
/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
```

---

## 3. Load the Param Rudra Environment

Start with a clean module environment:

```bash
module purge
```

Load the Intel oneAPI environment:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0
```

---

## 4. Verify Intel MPI

Check the MPI executables:

```bash
which mpiexec
which mpirun
```

Expected:

```text
/home/apps/Compiler/intel/openapi2024/mpi/2021.11/bin/mpiexec
/home/apps/Compiler/intel/openapi2024/mpi/2021.11/bin/mpirun
```

Also check the MPI compiler wrappers:

```bash
which mpiicx
which mpiifx
```

The MPI compiler wrappers used for this installation are:

```text
mpiicx  -> Intel LLVM C compiler
mpiifx  -> Intel LLVM Fortran compiler
```

---

## 5. Create the Python Environment

OpenMolcas requires Python for the `pymolcas` driver.

Create a dedicated Conda environment:

```bash
conda create -n openmolcas-26 python=3.12
```

Activate it:

```bash
conda activate openmolcas-26
```

Verify:

```bash
python --version
which python
```

Expected:

```text
Python 3.12.x
```

### 5.1 Install `pyparsing`

The generated `pymolcas` driver requires the Python package `pyparsing`.

Install it using:

```bash
conda install -c conda-forge pyparsing
```

Verify:

```bash
python -c "import pyparsing; print(pyparsing.__version__)"
```

---

## 6. Set the MPI Compilers

For the OpenMolcas build, use the Intel MPI compiler wrappers:

```bash
export CC=mpiicx
export FC=mpiifx
```

Verify:

```bash
echo $CC
echo $FC
```

Expected:

```text
mpiicx
mpiifx
```

### Why `mpiicx` and `mpiifx`?

The Intel MPI installation provides several compiler wrappers.

The older `mpiicc` wrapper points to the deprecated Intel `icc` compiler. On Param Rudra, `icc` is not available.

Therefore:

```text
mpiicc  -> not suitable
mpiicx  -> use this
```

and for Fortran:

```text
mpiifx  -> use this
```

---

## 7. Configure OpenMolcas with CMake

Go to the build directory:

```bash
cd /home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/build_mpi
```

Set the MPI compilers:

```bash
export CC=mpiicx
export FC=mpiifx
```

Run CMake:

```bash
cmake \
  -DMPI=ON \
  -DGA=ON \
  -DGA_BUILD=ON \
  -DGCCROOT=/usr \
  -DLINALG=MKL \
  -DHDF5=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi \
  ..
```

---

## 8. Explanation of CMake Options

The important CMake options are:

| Option | Description |
| --- | --- |
| `-DMPI=ON` | Enables MPI parallelization |
| `-DGA=ON` | Enables Global Arrays |
| `-DGA_BUILD=ON` | Builds the bundled Global Arrays library |
| `-DGCCROOT=/usr` | Specifies the system GCC installation |
| `-DLINALG=MKL` | Uses Intel MKL for linear algebra |
| `-DHDF5=OFF` | Disables HDF5 support |
| `-DCMAKE_INSTALL_PREFIX=...` | Defines the final installation directory |

The most important options for the MPI installation are:

```bash
-DMPI=ON
-DGA=ON
-DGA_BUILD=ON
-DLINALG=MKL
```

---

## 9. Why Global Arrays Must Be Enabled

For this OpenMolcas configuration, MPI requires Global Arrays.

Therefore:

```bash
-DMPI=ON
-DGA=ON
-DGA_BUILD=ON
```

are used together.

The bundled Global Arrays library is compiled as part of the OpenMolcas build.

The resulting libraries include:

```text
libga.a
libarmci.a
```

---

## 10. Global Arrays GCCROOT Issue

During the first build, the Global Arrays configuration produced the following error:

```text
GCCROOT cmake option not set when using clang compilers.
Please set a valid path to the GCC installation.
```

This occurs because OpenMolcas itself is compiled using Intel LLVM:

```text
mpiicx
mpiifx
```

while the bundled Global Arrays build also requires access to a GCC installation.

The system GCC installation on Param Rudra is located under:

```text
/usr
```

Therefore, add:

```bash
-DGCCROOT=/usr
```

to the CMake command.

The final CMake command is therefore:

```bash
cmake \
  -DMPI=ON \
  -DGA=ON \
  -DGA_BUILD=ON \
  -DGCCROOT=/usr \
  -DLINALG=MKL \
  -DHDF5=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi \
  ..
```

This allows:

```text
OpenMolcas
    |
    +-- Intel LLVM (mpiicx / mpiifx)
    |
    +-- Intel MPI
    |
    +-- Intel MKL
    |
    +-- Global Arrays
           |
           +-- System GCC (/usr)
```

---

## 11. Check the CMake Configuration

After running CMake, verify that the configuration reports MPI as enabled.

Important configuration information should include:

```text
MPI: TRUE
MPI_IMPLEMENTATION: impi
MPI_C: .../mpiicx
MPI_Fortran: .../mpiifx
```

The linear algebra configuration should show Intel MKL, including libraries such as:

```text
libmkl_scalapack_ilp64.so
libmkl_intel_ilp64.so
libmkl_core.so
libmkl_sequential.so
libmkl_blacs_intelmpi_ilp64.so
```

The Global Arrays configuration should also be present.

---

## 12. Compile OpenMolcas

After successful CMake configuration, compile OpenMolcas:

```bash
make -j48
```

Alternatively:

```bash
make
```

if you do not want to explicitly specify the number of build processes.

The build should compile major OpenMolcas programs including:

```text
scf.exe
seward.exe
rasscf.exe
rassi.exe
caspt2.exe
slapaf.exe
surfacehop.exe
single_aniso.exe
vibrot.exe
parnell.exe
```

and other required components.

---

## 13. Python Error During the Build

During the first build, compilation reached 100%, but the following message appeared:

```text
/usr/bin/env: 'python': No such file or directory
```

The reason was that the system provided:

```bash
python3
```

but did not provide a `python` executable.

The solution was to create and activate the dedicated Conda environment:

```bash
conda create -n openmolcas-26 python=3.12
conda activate openmolcas-26
```

Then run:

```bash
make
```

again.

The subsequent build successfully generated the `pymolcas` target:

```text
[100%] Built target pymolcas_target
```

No complete rebuild from scratch was required.

---

## 14. Install OpenMolcas

After successful compilation:

```bash
make install
```

The installation is placed in:

```text
/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
```

Check the installation:

```bash
ls /home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
```

Expected directories/files include:

```text
CONTRIBUTORS.md
LICENSE
basis_library/
bin/
data/
doc/
lib/
molcas.rte
pymolcas
sbin/
```

---

## 15. Set the OpenMolcas Runtime Environment

For normal use, load the required modules:

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0
```

Activate the Python environment:

```bash
conda activate openmolcas-26
```

Set the OpenMolcas installation:

```bash
export MOLCAS=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
```

For an MPI calculation, define the number of processes:

```bash
export MOLCAS_NPROCS=2
```

For example, for a 48-process calculation:

```bash
export MOLCAS_NPROCS=48
```

The actual value should normally correspond to the resources requested from Slurm.

---

## 16. Verify `pymolcas`

Run:

```bash
$MOLCAS/pymolcas -version
```

A successful installation should return something similar to:

```text
python driver version = py2.32
(after the original perl EMIL interpreter of Valera Veryazov)
```

### Important

The value:

```text
py2.32
```

is the **PyMolcas Python driver version**.

It is not the OpenMolcas release version.

---

## 17. Verify the OpenMolcas Version

Use:

```bash
$MOLCAS/sbin/version
```

The installed source version used for this build is:

```text
v26.06-1186-g641cfea24
```

This indicates:

```text
OpenMolcas 26.06
Git revision: 641cfea24
```

---

## 18. Verify MPI and MKL Libraries

Check the RASSCF executable:

```bash
ldd $MOLCAS/bin/rasscf.exe | grep -E 'mpi|mkl|not found'
```

The output should contain Intel MPI libraries such as:

```text
libmpi_ilp64.so
libmpifort.so.12
libmpi.so.12
```

and Intel MKL libraries such as:

```text
libmkl_scalapack_ilp64.so.2
libmkl_intel_ilp64.so.2
libmkl_core.so.2
libmkl_sequential.so.2
libmkl_blacs_intelmpi_ilp64.so.2
```

---

## 19. Check for Missing Libraries

Run:

```bash
ldd $MOLCAS/bin/rasscf.exe | grep "not found"
```

A successful installation should produce:

```text
<no output>
```

No output means that all dynamically linked libraries required by `rasscf.exe` were successfully resolved.

---

## 20. Verify `seward.exe`

The same check can be performed for SEWARD:

```bash
ldd $MOLCAS/bin/seward.exe | grep -E 'mpi|mkl|not found'
```

and:

```bash
ldd $MOLCAS/bin/seward.exe | grep "not found"
```

Again, the second command should produce no output.

---

## 21. Complete Environment Setup

The following is the recommended environment for running OpenMolcas 2026 MPI on Param Rudra:

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate openmolcas-26

export MOLCAS=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi

export MOLCAS_NPROCS=2
```

For a different number of MPI processes, change:

```bash
export MOLCAS_NPROCS=2
```

to the required number.

---

## 22. Build-Time Environment vs Runtime Environment

It is useful to distinguish between variables required for compiling OpenMolcas and variables required for running it.

### Build-time

These are required when configuring/building OpenMolcas:

```bash
export CC=mpiicx
export FC=mpiifx
```

and:

```bash
-DMPI=ON
-DGA=ON
-DGA_BUILD=ON
-DGCCROOT=/usr
-DLINALG=MKL
-DHDF5=OFF
```

### Runtime

For an already-installed OpenMolcas, the important settings are:

```bash
module load ...
conda activate openmolcas-26
export MOLCAS=...
export MOLCAS_NPROCS=...
```

`CC` and `FC` do not need to be exported every time an already-installed OpenMolcas executable is run.

---

## 23. Recommended Verification Sequence

After installation, use the following sequence:

### Step 1: Check Python

```bash
which python
python --version
```

### Step 2: Check MPI

```bash
which mpiexec
which mpirun
```

### Step 3: Check OpenMolcas

```bash
$MOLCAS/pymolcas -version
```

### Step 4: Check OpenMolcas source/build version

```bash
$MOLCAS/sbin/version
```

### Step 5: Check RASSCF dependencies

```bash
ldd $MOLCAS/bin/rasscf.exe | grep -E 'mpi|mkl|not found'
```

### Step 6: Check for unresolved libraries

```bash
ldd $MOLCAS/bin/rasscf.exe | grep "not found"
```

The last command should return nothing.

---

## 24. Final Installation Status

The installation is considered successfully built and installed when all of the following are satisfied:

```text
[✓] Intel oneAPI environment loaded
[✓] Intel MPI available
[✓] mpiicx available
[✓] mpiifx available
[✓] Intel MKL available
[✓] Python 3.12 environment created
[✓] pyparsing installed
[✓] CMake configuration successful
[✓] MPI enabled
[✓] Global Arrays enabled
[✓] Global Arrays compiled
[✓] OpenMolcas compiled
[✓] pymolcas generated
[✓] OpenMolcas installed
[✓] pymolcas -version works
[✓] OpenMolcas version verified
[✓] RASSCF MPI libraries resolved
[✓] RASSCF MKL libraries resolved
[✓] No missing shared libraries
```

---

## 25. Troubleshooting

### Problem 1: `icc: command not found`

**Error**

```text
icc: command not found
```

**Cause**

The Intel MPI `mpiicc` wrapper attempts to use the deprecated Intel `icc` compiler.

**Solution**

Use:

```bash
export CC=mpiicx
export FC=mpiifx
```

instead of:

```bash
export CC=mpiicc
```

---

### Problem 2: Global Arrays asks for `GCCROOT`

**Error**

```text
GCCROOT cmake option not set when using clang compilers.
Please set a valid path to the GCC installation.
```

**Solution**

Specify the system GCC installation:

```bash
-DGCCROOT=/usr
```

For example:

```bash
cmake \
  -DMPI=ON \
  -DGA=ON \
  -DGA_BUILD=ON \
  -DGCCROOT=/usr \
  -DLINALG=MKL \
  -DHDF5=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi \
  ..
```

---

### Problem 3: `python: No such file or directory`

**Error**

```text
/usr/bin/env: 'python': No such file or directory
```

**Cause**

The system provides `python3`, but the build requires the `python` command.

**Solution**

Activate the dedicated Conda environment:

```bash
conda activate openmolcas-26
```

Verify:

```bash
which python
python --version
```

Then run:

```bash
make
```

again.

---

### Problem 4: `ModuleNotFoundError: No module named 'pyparsing'`

**Error**

```text
ModuleNotFoundError: No module named 'pyparsing'
```

**Solution**

Install `pyparsing` in the OpenMolcas Conda environment:

```bash
conda activate openmolcas-26
conda install -c conda-forge pyparsing
```

Verify:

```bash
python -c "import pyparsing; print(pyparsing.__version__)"
```

Then:

```bash
$MOLCAS/pymolcas -version
```

---

### Problem 5: `ldd` shows `not found`

Run:

```bash
ldd $MOLCAS/bin/rasscf.exe | grep "not found"
```

If libraries are missing, first make sure the Param Rudra Intel environment is loaded:

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0
```

Then repeat the `ldd` check.

---

## 26. Example: Build Configuration in One Block

For future reference, the complete build configuration is:

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate openmolcas-26

export CC=mpiicx
export FC=mpiifx

cd /home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/build_mpi

cmake \
  -DMPI=ON \
  -DGA=ON \
  -DGA_BUILD=ON \
  -DGCCROOT=/usr \
  -DLINALG=MKL \
  -DHDF5=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi \
  ..

make -j48
make install
```

---

## 27. Example: Runtime Configuration in One Block

After installation:

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate openmolcas-26

export MOLCAS=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi

export MOLCAS_NPROCS=2
```

Verify:

```bash
$MOLCAS/pymolcas -version
$MOLCAS/sbin/version
ldd $MOLCAS/bin/rasscf.exe | grep "not found"
```

---

## 28. Next Step: MPI Runtime Test

Successful compilation and library checks do not by themselves prove that an MPI calculation runs correctly.

The final validation should therefore be performed on a **compute node** using a small test calculation.

For example:

```bash
export MOLCAS_NPROCS=2
```

Then run a small OpenMolcas calculation and verify that:

1. `pymolcas` starts correctly.
2. SEWARD runs successfully.
3. RASSCF runs successfully.
4. Intel MPI launches multiple processes.
5. Global Arrays initializes correctly.
6. MKL/ScaLAPACK libraries are used successfully.
7. The calculation terminates normally.

Only after this runtime test should the installation be used for production MPI calculations.

---

## 29. Summary

The OpenMolcas 2026 MPI installation on Param Rudra uses the following architecture:

```text
                    OpenMolcas 2026
                           |
                    +------+------+
                    |             |
                  MPI           MKL
                    |             |
             Intel MPI       ScaLAPACK
                    |
             Global Arrays
                    |
              MPI Processes
```

Compiler configuration:

```text
C       : mpiicx
Fortran : mpiifx
```

Linear algebra:

```text
Intel MKL
```

MPI:

```text
Intel MPI 2021.11
```

Global Arrays:

```text
Bundled OpenMolcas Global Arrays
```

GCC required by Global Arrays:

```text
/usr
```

Python:

```text
Conda environment: openmolcas-26
Python: 3.12
```

Installation:

```text
/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
```

OpenMolcas version:

```text
v26.06-1186-g641cfea24
```

The installation has been successfully compiled, installed, and verified at the executable/library level. The remaining validation is an actual MPI runtime calculation on a compute node.
