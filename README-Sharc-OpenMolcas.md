# GuideLog07
This is a maintained log for solving common errors while installing & running the softwares.

## Solution for OpenMolcas instllation in paramshivay iit bhu

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

## Solution for Sharc 3.x.x instllation in paramshivay iit bhu

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
     
## Installing BAGEL on a Compute Server
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


