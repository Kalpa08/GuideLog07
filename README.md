# GuideLog07
This is a maintained log for solving common errors while installing & running the softwares.

## Solution for OpenMolcas instllation in paramshivay iit bhu

* module load ohpc
* module load intel/2020.2.254
* module load conda
* export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/apps/bio_tools/conda/lib
* module load hdf5-1.12.0
* . /home/apps/spack/share/spack/setup-env.sh
* spack load intel-oneapi-mkl@2021.3.0
* module load gcc/10.2.0
* export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/$USER/slurm-library:/usr/lib64
* export FC=ifort

> git clone https://gitlab.com/Molcas/OpenMolcas.git
> cd OpenMolcas
> mkdir build
> cd build
> cmake -DLINALG=MKL -D CMAKE_INSTALL_PREFIX=/path/to/installation/ ../
> cp pymolcas bin/
* set the path of MOLCAS upto build directory.
*  
