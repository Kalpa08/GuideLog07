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

## Solution for Sharc 3.0.1 instllation in paramshivay iit bhu

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
 ***  After this run ``` make clean``` in source, set USE_PYSHARC to true and run ```make install``` in pysharc/.
 *** go to bin and check whether all the binaries and python scripts are present or not
 *** set the path to bin in your .bashrc and source it to use.
     



  
