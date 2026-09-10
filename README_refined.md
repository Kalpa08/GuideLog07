# 📘 GuideLog07 — HPC Software Installation Guidebook

A maintained reference for installing and running computational-chemistry software across our clusters (**ParamShivay**, a generic **Compute Server**, and **Param Rudra**). Each chapter is self-contained: environment setup → build → verification → troubleshooting.

---

## 🗂️ Table of Contents

| # | Chapter | Cluster / Server | Status |
|---|---|---|---|
| 1 | [OpenMolcas](#chapter-1--openmolcas-on-paramshivay) | ParamShivay | ✅ Installed |
| 2 | [SHARC 3.x.x](#chapter-2--sharc-3xx-on-paramshivay) | ParamShivay | ✅ Installed |
| 3 | [BAGEL](#chapter-3--bagel) | Compute Server | ✅ Installed |
| 4 | [SHARC 4.0](#chapter-4--sharc-40) | Compute Server | ✅ Installed |
| 5 | [ML Interfaces (SPaiNN / SchNarc)](#chapter-5--machine-learning-interfaces-for-sharc-40) | Compute Server (`sharc4.0` env) | ✅ Installed |
| 6 | [SHARC 3.0.2 (+ PySHARC)](#chapter-6--sharc-302-with-pysharc-on-param-rudra) | Param Rudra | ✅ Installed |
| 7 | [OpenMolcas 2026 MPI](#chapter-7--openmolcas-2026-mpi-on-param-rudra) | Param Rudra | ✅ Installed — runtime MPI test pending |
| 8 | [SHARC 4.1 + WFoverlap](#chapter-8--sharc-41--wfoverlap-on-param-rudra) | Param Rudra | ✅ Installed — `make test` for WFoverlap pending |

> 💡 **How to use this guidebook:** each chapter is copy-paste runnable top to bottom. Callout boxes mark **⚠️ known issues**, **💡 tips**, and **📌 persistent changes** (edits to source files that survive across sessions, as opposed to per-session environment variables).

---
---

# Chapter 1 — OpenMolcas on ParamShivay

**Cluster:** ParamShivay, IIT BHU

## 1.1 Load the Environment

```bash
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
```

## 1.2 Clone, Configure, and Build

```bash
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

## 1.3 Set the `MOLCAS` Path

```bash
vi ~/.bashrc
export MOLCAS=/path/to/installation/
source ~/.bashrc
```

---
---

# Chapter 2 — SHARC 3.x.x on ParamShivay

**Cluster:** ParamShivay, IIT BHU

## 2.1 Load the Environment

```bash
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

## 2.2 Download & Configure

Download from the [SHARC releases page](https://github.com/sharc-md/sharc/releases/), then go to the `source/` directory and edit the Makefile:

```make
USE_PYSHARC := false

#intel, gnu
USE_COMPILER := gnu

#mkl,gnu
USE_LIBS := mkl

#Static libraries
COMP_STATIC := false

#needed for PYSHARC
ANACONDA := /home/apps/bio_tools/conda
# =======================================
```

## 2.3 Build (Normal, then PySHARC)

```bash
make install
```

then in `source/`:

```bash
make clean
```

Set `USE_PYSHARC := true`, then:

```bash
make install    # run in pysharc/
```

## 2.4 Verify & Finalize

- Check `bin/` — confirm all binaries and Python scripts are present.
- Add the `bin/` path to `.bashrc` and `source` it.

---
---

# Chapter 3 — BAGEL

**Server:** Compute Server &nbsp;·&nbsp; **Date:** 28/05/2025

## 3.1 Load Intel MPI and Compilers

```bash
. /opt/compilers/intel/setvars.sh
```

## 3.2 Install Boost Libraries (v1.88.0)

```bash
wget https://archives.boost.io/release/1.88.0/source/boost_1_88_0.tar.gz
tar -xzf boost_1_88_0.tar.gz
cd boost_1_88_0
./bootstrap.sh
./b2 install --prefix=/opt/SOFTWARES/bagel/boost_1_88_0/boost_installed
```

Add to `.bashrc` (or export in the current shell):

```bash
export BOOST_ROOT=/opt/SOFTWARES/bagel/boost_1_88_0/boost_installed
export LD_LIBRARY_PATH=$BOOST_ROOT/lib:$LD_LIBRARY_PATH
```

## 3.3 Download BAGEL

```bash
git clone https://github.com/qsimulate-open/bagel.git
cd bagel
```

## 3.4 Set Compiler & MPI Variables

```bash
export CC=mpicc
export CXX=mpicxx
export FC=mpiifort
export I_MPI_FABRICS=ofi
export FI_PROVIDER=shm
export I_MPI_PIN=0
export I_MPI_DEBUG=0
```

## 3.5 Build and Install

```bash
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

---
---

# Chapter 4 — SHARC 4.0

**Server:** Compute Server &nbsp;·&nbsp; **Date:** 28/05/2025

## 4.1 Load Intel Compilers & Set MPI Variables

```bash
source /opt/compilers/intel/setvars.sh

export CC=mpicc
export CXX=mpicxx
export FC=mpiifort
export I_MPI_FABRICS=ofi
export FI_PROVIDER=shm
export I_MPI_PIN=0
export I_MPI_DEBUG=0
```

## 4.2 Create the Conda Environment

```bash
conda create -n sharc4.0 -c conda-forge python=3.12 numpy scipy h5py matplotlib \
pyparsing netcdf4 gfortran_linux-64 pyscf openmm numba sympy pyyaml pytorch pytest ase \
opt_einsum threadpoolctl

conda activate sharc4.0

conda install -n sharc4.0 libzip openssl
```

## 4.3 Download and Build SHARC 4.0

```bash
wget https://github.com/sharc-md/sharc4/archive/refs/tags/v4.0.tar.gz
tar -xvf v4.0.tar.gz
cd sharc4-4.0
```

## 4.4 Compile PySHARC and Fortran Sources

```bash
cd pysharc
make install

cd ../source
make install

cd ../wfoverlap/source
make
```

✅ **SHARC 4.0 is now ready for use in the compute server.**

---
---

# Chapter 5 — Machine Learning Interfaces for SHARC 4.0

**Date:** 28/05/2025

SHARC 4.0 supports two neural-network-potential interfaces for nonadiabatic dynamics — **SPaiNN** and **SchNarc**. These are mutually exclusive, since they depend on incompatible versions of `schnetpack`.

> ⚠️ **Important:** Do not attempt to use SPaiNN and SchNarc in the same environment. Choose one per workflow.

## 5.1 Option 1 — SPaiNN

```bash
conda activate sharc4.0

git clone https://github.com/CompPhotoChem/SPaiNN.git
cd SPaiNN && pip install .
```

## 5.2 Option 2 — SchNarc

```bash
pip install schnetpack==1.0.1
git clone https://github.com/schnarc/SchNarc.git
cd SchNarc && pip install .
```

---
---

# Chapter 6 — SHARC 3.0.2 (with PySHARC) on Param Rudra

A complete, reproducible procedure for installing **SHARC 3.0.2 with PySHARC** on **Param Rudra**, in the user's home directory, with **no administrator privileges required**.

**Stack used:**
- GNU compiler for SHARC
- Intel oneAPI MKL for BLAS/LAPACK
- Miniconda3 for the PySHARC Python environment (Python 3.9)
- Conda-forge packages for Python / NetCDF / HDF5 dependencies

### Chapter Contents
6.1 [Directory Structure](#61-installation-directory-structure) · 6.2 [Load Modules](#62-load-the-required-modules) · 6.3 [Install Miniconda3](#63-install-miniconda3) · 6.4 [Create the Conda Env](#64-create-the-pysharc-conda-environment) · 6.5 [Download SHARC 3.0.2](#65-download-sharc-302) · 6.6 [Build Standard SHARC](#66-compile-the-standard-sharc-binaries) · 6.7 [Enable PySHARC](#67-enable-pysharc) · 6.8 [Build PySHARC](#68-compile-and-install-pysharc) · 6.9 [Configure Environment](#69-configure-the-sharc-environment) · 6.10 [Verify](#610-verify-the-installation) · 6.11 [Full Command Sequence](#611-complete-command-sequence) · 6.12 [Why These Choices](#612-why-these-modules-and-versions-are-used) · 6.13–6.16 [GCC / Isolation / Multi-version / Usage](#613-why-gcc-is-used) · 6.17 [Troubleshooting](#617-troubleshooting) · 6.18 [Optional Tests](#618-optional-sharc-tests)

---

## 6.1 Installation Directory Structure

All SHARC versions live under:

```text
~/SOFTWARES/SHARC/
```

Example layout after installing 3.0.2:

```text
~/SOFTWARES/
├── miniconda3/
└── SHARC/
    └── sharc-3.0.2/
        ├── bin/
        ├── lib/
        ├── pysharc/
        ├── source/
        └── ...
```

Future versions get their own sibling directory (and optionally their own conda env):

```text
~/SOFTWARES/SHARC/
├── sharc-3.0.2/
├── sharc-4.0.0/
└── ...
```

```text
pysharc_3.0.2
pysharc_4.0.0
```

---

## 6.2 Load the Required Modules

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0

export CC=gcc
```

Check the compilers:

```bash
which gcc
gcc --version

which gfortran
gfortran --version
```

> ⚠️ `compiler/oneapi2024/mkl/2024.0` should only be loaded **once**. TBB is loaded before MKL because MKL depends on TBB in this environment.

---

## 6.3 Install Miniconda3

```bash
cd ~/SOFTWARES
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
source ~/.bashrc
conda config --set auto_activate_base false
conda --version
```

---

## 6.4 Create the PySHARC Conda Environment

```bash
conda create -n pysharc_3.0.2 -c conda-forge \
python=3.9 numpy scipy h5py six matplotlib \
python-dateutil pyyaml pyparsing kiwisolver cycler \
netcdf4 hdf5 h5utils gfortran_linux-64

conda activate pysharc_3.0.2

python --version
echo $CONDA_PREFIX
```

Expected: `Python 3.9.x`, and `$CONDA_PREFIX` pointing to e.g.:

```text
/home/<username>/SOFTWARES/miniconda3/envs/pysharc_3.0.2
```

**Why these packages?** They provide the scientific-Python stack PySHARC needs (`numpy`, `scipy`, `h5py`, `matplotlib`, `netcdf4`, `hdf5` + utilities, `python-dateutil`, `pyyaml`, `pyparsing`, `kiwisolver`, `cycler`, `six`) — including NetCDF/HDF5, so no system-wide NetCDF install is required.

---

## 6.5 Download SHARC 3.0.2

```bash
mkdir -p ~/SOFTWARES/SHARC
cd ~/SOFTWARES/SHARC

wget https://github.com/sharc-md/sharc/archive/refs/tags/v3.0.2.tar.gz
tar -xzf v3.0.2.tar.gz
rm v3.0.2.tar.gz

ls ~/SOFTWARES/SHARC/   # expect: sharc-3.0.2
```

---

## 6.6 Compile the Standard SHARC Binaries

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
nano Makefile
```

Set:

```make
USE_PYSHARC := false
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

| Variable | Value | Purpose |
|---|---|---|
| `USE_PYSHARC` | `false` | Build the standard SHARC binaries first |
| `USE_COMPILER` | `gnu` | Use GNU Fortran |
| `USE_LIBS` | `mkl` | Use Intel MKL for numerical libraries |
| `COMP_STATIC` | `false` | Use dynamic linking |

```bash
make install
make clean
```

Binaries land in `~/SOFTWARES/SHARC/sharc-3.0.2/bin/`.

---

## 6.7 Enable PySHARC

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
nano Makefile
```

```diff
- USE_PYSHARC := false
+ USE_PYSHARC := true
```

Final block:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

---

## 6.8 Compile and Install PySHARC

```bash
conda activate pysharc_3.0.2
export CC=gcc
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
```

**6.8.1 Test the extension build manually first:**

```bash
CC=gcc python sharc_setup build_ext --build-lib .
```

**6.8.2 Then install:**

```bash
make install
```

---

## 6.9 Configure the SHARC Environment

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin
source sharcvars.sh
```

Sets: `SHARC`, `SHARCLIB`, `PYTHONPATH`, `LD_LIBRARY_PATH`.

```bash
echo $SHARC   # → ~/SOFTWARES/SHARC/sharc-3.0.2/bin
```

---

## 6.10 Verify the Installation

**6.10.1 SHARC version:**

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin
./sharc.x --version    # expect "Version: 3.0"
```

**6.10.2 PySHARC:**

```bash
conda activate pysharc_3.0.2
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
python -c "import sharc; print('PySHARC import OK')"
```

**6.10.3 Missing shared libraries:**

```bash
ldd sharc/sharc*.so | grep "not found"    # expect no output
```

---

## 6.11 Complete Command Sequence

```bash
# --- Load environment ---
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0
export CC=gcc

# --- Install Miniconda3 ---
cd ~/SOFTWARES
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
source ~/.bashrc
conda config --set auto_activate_base false

# --- Create PySHARC environment ---
conda create -n pysharc_3.0.2 -c conda-forge \
python=3.9 numpy scipy h5py six matplotlib \
python-dateutil pyyaml pyparsing kiwisolver cycler \
netcdf4 hdf5 h5utils gfortran_linux-64
conda activate pysharc_3.0.2

# --- Download SHARC 3.0.2 ---
mkdir -p ~/SOFTWARES/SHARC
cd ~/SOFTWARES/SHARC
wget https://github.com/sharc-md/sharc/archive/refs/tags/v3.0.2.tar.gz
tar -xzf v3.0.2.tar.gz
rm v3.0.2.tar.gz

# --- Build standard SHARC ---
cd ~/SOFTWARES/SHARC/sharc-3.0.2/source
#  edit Makefile: USE_PYSHARC := false, USE_COMPILER := gnu, USE_LIBS := mkl, COMP_STATIC := false
make install
make clean

# --- Enable PySHARC ---
#  edit Makefile: USE_PYSHARC := true

# --- Build PySHARC ---
conda activate pysharc_3.0.2
export CC=gcc
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
CC=gcc python sharc_setup build_ext --build-lib .
make install

# --- Verify PySHARC ---
python -c "import sharc; print('PySHARC import OK')"

# --- Configure SHARC ---
cd ~/SOFTWARES/SHARC/sharc-3.0.2/bin
source sharcvars.sh

# --- Verify SHARC ---
./sharc.x --version

# --- Check shared libraries ---
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
ldd sharc/sharc*.so | grep "not found"
```

---

## 6.12 Why These Modules and Versions Are Used

The tested working environment:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0
```

with Makefile:

```make
USE_PYSHARC := true
USE_COMPILER := gnu
USE_LIBS := mkl
COMP_STATIC := false
```

**6.12.1 GNU Compiler** — `USE_COMPILER := gnu`; the system GCC/GFortran on Param Rudra was found suitable for SHARC 3.0.2.

**6.12.2 Intel MKL 2024.0** — `USE_LIBS := mkl` gives optimized BLAS/LAPACK without a separate stack. Loaded via `module load compiler/oneapi2024/mkl/2024.0`.

**6.12.3 Intel TBB** — `module load compiler/oneapi2024/tbb` must load **before** MKL, since MKL depends on it here.

**6.12.4 Intel Compiler Runtime** — `compiler-rt/2024.0.2` is part of the oneAPI environment the MKL stack relies on, even though GNU compiles the SHARC source.

**6.12.5 Intel Fortran Module** — `ifort/2024.0.2` is loaded and retained as part of the tested oneAPI stack, but `USE_COMPILER := gnu` means SHARC Fortran is actually compiled with **GNU Fortran**, not Intel Fortran.

**6.12.6 Intel MPI** — `mpi/2021.11` isn't required for the basic SHARC binaries but is part of the tested environment and useful for later HPC workflows.

---

## 6.13 Why GCC Is Used

```bash
export CC=gcc
```

Conda may ship its own C compiler wrapper (e.g. `x86_64-conda-linux-gnu-cc`). For this installation, the **system GCC** on Param Rudra was used instead, hence building PySHARC explicitly with:

```bash
CC=gcc python sharc_setup build_ext --build-lib .
```

This was found to give a compatible build environment for the 3.0.2 PySHARC extension.

---

## 6.14 Why a Separate Conda Environment Is Used

A dedicated env (`pysharc_3.0.2`) rather than the global Python install gives:

1. Isolation of PySHARC dependencies from other projects.
2. Different SHARC versions → different Python environments.
3. Minimized package conflicts.
4. No administrator privileges required.
5. Easier reproducibility on another system.

```text
~/SOFTWARES/miniconda3/envs/
├── pysharc_3.0.2/
├── pysharc_4.0.0/
└── ...
```

---

## 6.15 Installing Multiple SHARC Versions

Keep versions in separate directories — installing 4.0 must not touch 3.0.2:

```text
~/SOFTWARES/SHARC/
├── sharc-3.0.2/
├── sharc-4.0.0/
└── sharc-4.x.x/
```

with a separate conda env (e.g. `pysharc_4.0.0`) per version if needed.

---

## 6.16 Using SHARC After Installation

Every new shell needs:

```bash
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb
module load compiler/oneapi2024/mkl/2024.0

export CC=gcc

conda activate pysharc_3.0.2

source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh

echo $SHARC
which python
```

`$SHARC` should resolve to `~/SOFTWARES/SHARC/sharc-3.0.2/bin`.

---

## 6.17 Troubleshooting

**6.17.1 PySHARC compilation fails**

```bash
which gcc
gcc --version
export CC=gcc
cd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc
CC=gcc python sharc_setup build_ext --build-lib .
```

**6.17.2 PySHARC cannot be imported**

```bash
conda activate pysharc_3.0.2
python -c "import sharc; print('PySHARC import OK')"
```

Run this from `~/SOFTWARES/SHARC/sharc-3.0.2/pysharc/`.

**6.17.3 Shared library is missing**

```bash
ldd ~/SOFTWARES/SHARC/sharc-3.0.2/pysharc/sharc/sharc*.so | grep "not found"
```

If something is `not found`, re-check that the Param Rudra modules and correct conda env are active.

**6.17.4 SHARC executable cannot be found**

```bash
source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh
echo $SHARC
ls $SHARC
```

---

## 6.18 Optional SHARC Tests

```bash
source ~/SOFTWARES/SHARC/sharc-3.0.2/bin/sharcvars.sh
$SHARC/tests.py
```

For the `wfoverlap` component:

```bash
cd ~/SOFTWARES/SHARC/sharc-3.0.2/wfoverlap/source
make test
```

---

### Chapter 6 Summary

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

Final Makefile config: `USE_PYSHARC := true · USE_COMPILER := gnu · USE_LIBS := mkl · COMP_STATIC := false`, with `export CC=gcc`.

**References**
- [SHARC GitHub Repository](https://github.com/sharc-md/sharc)
- [SHARC 3.0.2 Release](https://github.com/sharc-md/sharc/releases/tag/v3.0.2)
- [SHARC 3.0.2 Installation Instructions](https://github.com/sharc-md/sharc/blob/v3.0.2/INSTALL)
- [Miniconda Documentation](https://docs.conda.io/)

> ⚠️ SPaiNN and SchNarc (Chapter 5) remain mutually exclusive due to conflicting `schnetpack` versions.

---
---

# Chapter 7 — OpenMolcas 2026 MPI on Param Rudra

Installation of **OpenMolcas 2026 with MPI support** on **Param Rudra**, using Intel oneAPI compilers, Intel MPI, Intel MKL, ScaLAPACK, Global Arrays, Python 3.12, and CMake.

## 7.1 Overview

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

## 7.2 Directory Structure

```text
/home/kalpa.bhu/SOFTWARES/OpenMolcas/
└── OpenMolcas_2026/
    ├── build_mpi/
    ├── OM_install_mpi/
    └── source files
```

- Source: `/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026`
- Build: `/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/build_mpi`
- Install: `/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi`

## 7.3 Load the Param Rudra Environment

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0
```

## 7.4 Verify Intel MPI

```bash
which mpiexec
which mpirun
```

Expected:

```text
/home/apps/Compiler/intel/openapi2024/mpi/2021.11/bin/mpiexec
/home/apps/Compiler/intel/openapi2024/mpi/2021.11/bin/mpirun
```

```bash
which mpiicx
which mpiifx
```

```text
mpiicx  -> Intel LLVM C compiler
mpiifx  -> Intel LLVM Fortran compiler
```

## 7.5 Create the Python Environment

```bash
conda create -n openmolcas-26 python=3.12
conda activate openmolcas-26

python --version
which python    # expect Python 3.12.x
```

**7.5.1 Install `pyparsing`** (required by the generated `pymolcas` driver):

```bash
conda install -c conda-forge pyparsing
python -c "import pyparsing; print(pyparsing.__version__)"
```

## 7.6 Set the MPI Compilers

```bash
export CC=mpiicx
export FC=mpiifx

echo $CC
echo $FC
```

> ⚠️ **Why `mpiicx` / `mpiifx`?** The older `mpiicc` wrapper points to the deprecated Intel `icc`, which is not available on Param Rudra. Use `mpiicx` for C and `mpiifx` for Fortran instead.

## 7.7 Configure with CMake

```bash
cd /home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/build_mpi

export CC=mpiicx
export FC=mpiifx

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

### CMake Options Explained

| Option | Description |
| --- | --- |
| `-DMPI=ON` | Enables MPI parallelization |
| `-DGA=ON` | Enables Global Arrays |
| `-DGA_BUILD=ON` | Builds the bundled Global Arrays library |
| `-DGCCROOT=/usr` | Specifies the system GCC installation |
| `-DLINALG=MKL` | Uses Intel MKL for linear algebra |
| `-DHDF5=OFF` | Disables HDF5 support |
| `-DCMAKE_INSTALL_PREFIX=...` | Final installation directory |

## 7.8 Why Global Arrays Must Be Enabled

MPI in this configuration requires Global Arrays, so `-DMPI=ON -DGA=ON -DGA_BUILD=ON` are used together. The bundled build produces `libga.a` and `libarmci.a`.

## 7.9 ⚠️ Known Issue: `GCCROOT` for Global Arrays

The first build failed with:

```text
GCCROOT cmake option not set when using clang compilers.
Please set a valid path to the GCC installation.
```

**Cause:** OpenMolcas compiles with Intel LLVM (`mpiicx` / `mpiifx`), but the bundled Global Arrays build also needs a GCC installation. Param Rudra's system GCC lives at `/usr`.

**Fix:** add `-DGCCROOT=/usr` (already included in §7.7's final command).

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

## 7.10 Check the CMake Configuration

Confirm MPI is enabled:

```text
MPI: TRUE
MPI_IMPLEMENTATION: impi
MPI_C: .../mpiicx
MPI_Fortran: .../mpiifx
```

and MKL is selected, including libraries such as:

```text
libmkl_scalapack_ilp64.so
libmkl_intel_ilp64.so
libmkl_core.so
libmkl_sequential.so
libmkl_blacs_intelmpi_ilp64.so
```

## 7.11 Compile

```bash
make -j48
```

(or plain `make` if you don't want to set the process count). Major targets built include `scf.exe`, `seward.exe`, `rasscf.exe`, `rassi.exe`, `caspt2.exe`, `slapaf.exe`, `surfacehop.exe`, `single_aniso.exe`, `vibrot.exe`, `parnell.exe`, and others.

## 7.12 ⚠️ Known Issue: Missing `python` at 100% Build

```text
/usr/bin/env: 'python': No such file or directory
```

**Cause:** the system provides `python3` but not a `python` executable.

**Fix:**

```bash
conda create -n openmolcas-26 python=3.12
conda activate openmolcas-26
make
```

No full rebuild from scratch was required — the build resumed and completed:

```text
[100%] Built target pymolcas_target
```

## 7.13 Install

```bash
make install
```

Installed to `/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi`, containing:

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

## 7.14 Set the Runtime Environment

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate openmolcas-26

export MOLCAS=/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi
export MOLCAS_NPROCS=2       # e.g. 48 for a 48-process job, matched to Slurm resources
```

## 7.15 Verify `pymolcas`

```bash
$MOLCAS/pymolcas -version
```

Expected (approximately):

```text
python driver version = py2.32
(after the original perl EMIL interpreter of Valera Veryazov)
```

> 💡 `py2.32` is the **PyMolcas driver version**, not the OpenMolcas release version.

## 7.16 Verify the OpenMolcas Version

```bash
$MOLCAS/sbin/version
```

```text
v26.06-1186-g641cfea24
```

i.e. OpenMolcas 26.06, Git revision `641cfea24`.

## 7.17 Verify MPI and MKL Libraries

```bash
ldd $MOLCAS/bin/rasscf.exe | grep -E 'mpi|mkl|not found'
```

Should show Intel MPI (`libmpi_ilp64.so`, `libmpifort.so.12`, `libmpi.so.12`) and Intel MKL (`libmkl_scalapack_ilp64.so.2`, `libmkl_intel_ilp64.so.2`, `libmkl_core.so.2`, `libmkl_sequential.so.2`, `libmkl_blacs_intelmpi_ilp64.so.2`).

## 7.18 Check for Missing Libraries

```bash
ldd $MOLCAS/bin/rasscf.exe | grep "not found"    # expect no output
```

## 7.19 Verify `seward.exe`

```bash
ldd $MOLCAS/bin/seward.exe | grep -E 'mpi|mkl|not found'
ldd $MOLCAS/bin/seward.exe | grep "not found"     # expect no output
```

## 7.20 Complete Environment Reference

**Build-time:**

```bash
export CC=mpiicx
export FC=mpiifx
```

```text
-DMPI=ON
-DGA=ON
-DGA_BUILD=ON
-DGCCROOT=/usr
-DLINALG=MKL
-DHDF5=OFF
```

**Runtime:**

```bash
module load ...
conda activate openmolcas-26
export MOLCAS=...
export MOLCAS_NPROCS=...
```

`CC`/`FC` are not needed once OpenMolcas is already built.

## 7.21 Recommended Verification Sequence

```bash
which python && python --version
which mpiexec && which mpirun
$MOLCAS/pymolcas -version
$MOLCAS/sbin/version
ldd $MOLCAS/bin/rasscf.exe | grep -E 'mpi|mkl|not found'
ldd $MOLCAS/bin/rasscf.exe | grep "not found"     # expect nothing
```

## 7.22 Final Installation Checklist

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

## 7.23 Troubleshooting

**Problem 1 — `icc: command not found`**
Cause: `mpiicc` uses deprecated `icc`.
Fix: `export CC=mpiicx` and `export FC=mpiifx` instead of `mpiicc`.

**Problem 2 — Global Arrays asks for `GCCROOT`**
Fix: add `-DGCCROOT=/usr` to the CMake command (see §7.9).

**Problem 3 — `python: No such file or directory`**
Cause: system has `python3` but not `python`.
Fix: `conda create -n openmolcas-26 python=3.12` → `conda activate openmolcas-26` → re-run `make`.

**Problem 4 — `ModuleNotFoundError: No module named 'pyparsing'`**
Fix:
```bash
conda activate openmolcas-26
conda install -c conda-forge pyparsing
python -c "import pyparsing; print(pyparsing.__version__)"
$MOLCAS/pymolcas -version
```

**Problem 5 — `ldd` shows `not found`**
Fix: re-load the full Param Rudra Intel environment (§7.3), then repeat the `ldd` check.

## 7.24 Build Configuration — One Block

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

## 7.25 Runtime Configuration — One Block

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

## 7.26 ⏭️ Next Step: MPI Runtime Test

Library-level checks don't prove an MPI calculation actually *runs*. The final validation must happen on a **compute node** with a small test job, confirming:

1. `pymolcas` starts correctly.
2. SEWARD runs successfully.
3. RASSCF runs successfully.
4. Intel MPI launches multiple processes.
5. Global Arrays initializes correctly.
6. MKL/ScaLAPACK libraries are used successfully.
7. The calculation terminates normally.

Only after this should the installation be used in production.

## 7.27 Summary

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

| | |
|---|---|
| Compiler (C / Fortran) | `mpiicx` / `mpiifx` |
| Linear algebra | Intel MKL |
| MPI | Intel MPI 2021.11 |
| Global Arrays | Bundled OpenMolcas Global Arrays |
| GCC (required by GA) | `/usr` |
| Python | Conda env `openmolcas-26`, Python 3.12 |
| Install path | `/home/kalpa.bhu/SOFTWARES/OpenMolcas/OpenMolcas_2026/OM_install_mpi` |
| Version | `v26.06-1186-g641cfea24` |

Compiled, installed, and verified at the executable/library level. **Remaining:** an actual MPI runtime calculation on a compute node.

---
---

# Chapter 8 — SHARC 4.1 + WFoverlap on Param Rudra

> A complete, self-sufficient installation record: Conda environment creation, SHARC core, PySHARC, and WFoverlap — including every pitfall hit along the way.

## 8.1 Overview

| | |
|---|---|
| **Install location** | `/home/kalpa.bhu/SOFTWARES/SHARC/sharc4` |
| **Conda environment** | `sharc4.1` (Python 3.12) |
| **Compiler** | Intel oneAPI `ifx` 2024.0.2 |
| **Math library** | Intel MKL 2024.0 |
| **MPI** | Intel MPI 2021.11 |
| **Git revision** | `v4.1-4-gec7ae737` (SHARC 4.1) |
| **PySHARC** | Built and verified |
| **WFoverlap** | `wfoverlap_ascii.x` built (ASCII variant, no COLUMBUS deps) |
| **Status** | ✅ SHARC + PySHARC + WFoverlap all built. `make test` for WFoverlap still pending |

> ⚠️ The `sharc.x --version` banner reports `Version: 4.0 (April 1, 2025)` — a stale string in `source/definitions.F90`. Trust `git describe` instead (§8.1.3), which confirms the source is actually **SHARC 4.1**.

---

## Part A — SHARC 4.1

### A.1 Create the Conda Environment

```bash
conda create -n sharc4.1 -c conda-forge python=3.12 numpy scipy h5py matplotlib \
pyparsing netcdf4 gfortran_linux-64 pyscf openmm numba sympy pyyaml pytorch pytest ase \
opt_einsum threadpoolctl pip joblib
```

This pulls Python 3.12 and the full scientific stack SHARC/PySHARC needs (numerics, I/O, electronic-structure interfaces, ML/optimization utilities) from `conda-forge` in one shot.

### A.2 Load the Compiler & Library Environment

```bash
module purge

module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate sharc4.1

unset CC
unset FC

which ifx
which ifort
```

SHARC 4.1 is built with `ifx`.

### A.3 Verify the Source Version

```bash
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4
git describe --tags --always
```

```text
v4.1-4-gec7ae737
```

### A.4 Configure the SHARC Makefile

```bash
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/source
vi Makefile
```

```make
USE_PYSHARC := false
USE_COMPILER := intel
USE_LIBS := mkl
COMP_STATIC := false
ANACONDA := ${CONDA_PREFIX}
```

### A.5 Compile SHARC (Normal Build)

```bash
make install
```

Warnings like `warning #6379` (structure alignment) are non-fatal.

```bash
cd ../bin
./sharc.x --version
```

### A.6 Enable PySHARC

```diff
- USE_PYSHARC := false
+ USE_PYSHARC := true
```

```bash
make install
```

### A.7 Build PySHARC

```bash
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/pysharc

unset PYTHONPATH
python sharc_setup build_ext --build-lib .
```

Produces `sharc/sharc.cpython-312-x86_64-linux-gnu.so` and installs into:

```text
/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/lib/
├── libsharc.so
└── libsharcnc.so
```

### A.8 Set the Runtime Environment

```bash
unset PYTHONPATH

export SHARC=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/bin
export SHARCLIB=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/lib
export PYSHARC=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/pysharc

export PYTHONPATH=$PYSHARC:$SHARCLIB
export LD_LIBRARY_PATH=$SHARCLIB:$LD_LIBRARY_PATH
```

or:

```bash
source /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/bin/sharcvars.sh
export PYTHONPATH=$PYSHARC:$SHARCLIB
```

### A.9 Verify PySHARC

```bash
which python
python --version
```

```text
/home/kalpa.bhu/SOFTWARES/miniconda3/envs/sharc4.1/bin/python
Python 3.12.x
```

```bash
python -c "import sharc; print(sharc.__file__)"
```

```text
/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/pysharc/sharc/__init__.py
```

```bash
python -c "import sharc.sharc; print('PySHARC 4.1: OK')"
```

```text
PySHARC 4.1: OK
```

### A.10 Fix Shared-Library Errors

```text
ImportError: libsharc.so: cannot open shared object file
```

Fix:

```bash
export LD_LIBRARY_PATH=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/lib:$LD_LIBRARY_PATH
```

Verify:

```bash
ldd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/pysharc/sharc/sharc.cpython-312-x86_64-linux-gnu.so | grep "not found"
```

Expected: **no output**.

### A.11 ⚠️ Critical Pitfall: Global `geodesic` Env in `.bashrc`

A `.bashrc` had globally injected the `geodesic` Conda environment (Python 3.9):

```bash
export PATH=/home/kalpa.bhu/SOFTWARES/miniconda3/envs/geodesic/bin:$PATH
export PYTHONPATH=/home/kalpa.bhu/SOFTWARES/miniconda3/envs/geodesic/lib/python3.9/site-packages:$PYTHONPATH
```

**Effect:** even with `CONDA_PREFIX` correctly pointing at `sharc4.1`, SHARC silently ran under Python 3.9, producing:

```text
SyntaxError: invalid syntax
```

at a `match` statement — `match` requires Python ≥ 3.10.

**Fix:** remove those two lines from global `.bashrc` entirely. Only activate `geodesic` on demand:

```bash
conda activate geodesic
```

This keeps SHARC's Python environment isolated from unrelated projects.

---

## Part B — WFoverlap

WFoverlap ships inside the SHARC 4.1 tree and shares the same conda environment and Intel toolchain set up in Part A. We build **`wfoverlap_ascii.x`** rather than the full `wfoverlap.x`, since the latter needs the COLUMBUS libraries.

### B.1 Location

```bash
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/wfoverlap/source
```

(Assumes the environment from **A.2** — `module load ...` + `conda activate sharc4.1` — is already active.)

### B.2 ⚠️ Known Issue: Broken `MKLROOT`

```bash
echo "$MKLROOT"
```

```text
/home/apps/Compiler/intel/openapi2024/``/mkl/2024.0
```

Note the stray literal backticks (`` `` ``) embedded in the path — these break any shell command passing `$MKLROOT` through to `ifx`.

Verify the libraries still exist, ignoring the broken variable:

```bash
find "$MKLROOT" -name "libmkl_intel_ilp64.a" -o \
                -name "libmkl_intel_thread.a" -o \
                -name "libmkl_core.a"
```

Confirmed present under `$MKLROOT/lib/`.

### B.3 Fix: Create a Clean MKL Symlink

```bash
ln -sfn "$MKLROOT" "$HOME/SOFTWARES/mkl-2024.0"
```

```text
/home/kalpa.bhu/SOFTWARES/mkl-2024.0  →  (real MKL install)
```

```bash
export MKLROOT="$HOME/SOFTWARES/mkl-2024.0"
```

> 💡 **Why this works:** every subsequent `$MKLROOT` reference (including inside the Makefile) now resolves through a symlink with no backticks, so it survives shell expansion cleanly.

### B.4 Modify the WFoverlap Makefile

**Before** (breaks on the malformed path):

```make
LALIB = -Wl,--start-group ${MKLROOT}/lib/libmkl_intel_ilp64.a ...
```

**After** (uses the clean `MKLROOT` variable consistently):

```make
LALIB  = -Wl,--start-group $(MKLROOT)/lib/libmkl_intel_ilp64.a $(MKLROOT)/lib/libmkl_intel_thread.a $(MKLROOT)/lib/libmkl_core.a -Wl,--end-group -liomp5 -lpthread -lm -ldl
```

Rest of the compiler settings unchanged:

```make
OMP = -qopenmp
FC  = ifx
OPT = -O3 -ipo

FCFLAGS = $(OPT) $(OMP) $(PROFILE) $(DEBUG) -fpp -i8 -DEXTBLAS -I"${MKLROOT}/include" -z muldefs
```

> 📌 This is the **one persistent change** made to the repo — everything else in this section is environment setup repeated per session.

### B.5 Dry-Run the Build (Recommended)

```bash
make -n wfoverlap_ascii.x | tail -3
```

Expected output confirms the linker points at the clean MKL path:

```text
/home/kalpa.bhu/SOFTWARES/mkl-2024.0/lib/libmkl_intel_ilp64.a
/home/kalpa.bhu/SOFTWARES/mkl-2024.0/lib/libmkl_intel_thread.a
/home/kalpa.bhu/SOFTWARES/mkl-2024.0/lib/libmkl_core.a
```

### B.6 Build

```bash
make clean
make wfoverlap_ascii.x
```

Compiled with:

```text
ifx  -O3  -ipo  -qopenmp  -fpp  -i8  -DEXTBLAS
```

linked against MKL. Only compiler output — a single non-fatal remark:

```text
remark #8291   (from read_turbomole.f90)
```

### B.7 Result

```bash
cp wfoverlap_ascii.x ../../bin
ln -fs wfoverlap_ascii.x ../../bin/wfoverlap.x
```

| File | Description |
|---|---|
| `wfoverlap/bin/wfoverlap_ascii.x` | The compiled ASCII executable |
| `wfoverlap/bin/wfoverlap.x` | Symlink → `wfoverlap_ascii.x` |

---

## Quick Reference — Full Sequence from Scratch

```bash
# --- 0. Create the conda environment (one-time) ---
conda create -n sharc4.1 -c conda-forge python=3.12 numpy scipy h5py matplotlib \
pyparsing netcdf4 gfortran_linux-64 pyscf openmm numba sympy pyyaml pytorch pytest ase \
opt_einsum threadpoolctl pip joblib

# --- 1. Load environment ---
module purge
module load compiler/oneapi2024/compiler-rt/2024.0.2
module load compiler/oneapi2024/ifort/2024.0.2
module load compiler/oneapi2024/mpi/2021.11
module load compiler/oneapi2024/tbb/2021.11
module load compiler/oneapi2024/mkl/2024.0

conda activate sharc4.1
unset CC FC PYTHONPATH

# --- 2. Build SHARC (normal) ---
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/source
# edit Makefile: USE_PYSHARC := false, USE_COMPILER := intel, USE_LIBS := mkl, ANACONDA := ${CONDA_PREFIX}
make install

# --- 3. Build PySHARC ---
# edit Makefile: USE_PYSHARC := true
make install
cd ../pysharc
unset PYTHONPATH
python sharc_setup build_ext --build-lib .

# --- 4. Set runtime environment ---
export SHARC=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/bin
export SHARCLIB=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/lib
export PYSHARC=/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/pysharc
export PYTHONPATH=$PYSHARC:$SHARCLIB
export LD_LIBRARY_PATH=$SHARCLIB:$LD_LIBRARY_PATH

# --- 5. Build WFoverlap ---
cd /home/kalpa.bhu/SOFTWARES/SHARC/sharc4/wfoverlap/source
ln -sfn "$MKLROOT" "$HOME/SOFTWARES/mkl-2024.0"
export MKLROOT="$HOME/SOFTWARES/mkl-2024.0"
# edit Makefile LALIB line to use $(MKLROOT) consistently (see B.4)
make clean
make wfoverlap_ascii.x
```

## Final Installation Structure

```text
/home/kalpa.bhu/SOFTWARES/SHARC/sharc4/
│
├── bin/
│   ├── sharc.x
│   ├── sharcvars.sh
│   └── ...
│
├── lib/
│   ├── libsharc.so
│   ├── libsharcnc.so
│   └── ...
│
├── pysharc/
│   └── sharc/
│       ├── __init__.py
│       └── sharc.cpython-312-x86_64-linux-gnu.so
│
├── source/
└── wfoverlap/
    ├── source/
    └── bin/
        ├── wfoverlap_ascii.x
        └── wfoverlap.x  (symlink → wfoverlap_ascii.x)
```

**Conda environment:**

```text
/home/kalpa.bhu/SOFTWARES/miniconda3/envs/sharc4.1
```

## Chapter 8 Summary

```text
sharc4.1 (conda-forge env, Python 3.12)
        │
        ├── Intel ifx 2024.0.2
        ├── Intel MKL 2024.0
        ├── Intel MPI 2021.11
        ├── SHARC 4.1 (source, git tag v4.1-4-gec7ae737)
        │     └── PySHARC (sharc.cpython-312-x86_64-linux-gnu.so)
        └── WFoverlap (wfoverlap_ascii.x)
```

**Critical rules to remember:**

> 1. Never globally add the `geodesic` (Python 3.9) environment to `PATH` or `PYTHONPATH` — activate it only on demand (`conda activate geodesic`), or SHARC's Python ≥3.10 code (e.g. `match` statements) breaks silently.
> 2. The cluster's `$MKLROOT` module variable contains stray backticks — always resolve it through the clean symlink at `$HOME/SOFTWARES/mkl-2024.0` before compiling WFoverlap.

**Outstanding item:**

- [ ] Run `make test` for WFoverlap to verify the build (not yet done)

---
---

# 📎 Appendix — Cross-Chapter Notes

- **Two SHARC 4.0 builds exist in this log**: Chapter 4 (generic Compute Server, GNU/OHPC-flavored) and Chapter 8 (Param Rudra, Intel `ifx`/MKL-flavored SHARC **4.1**). They use different conda environments (`sharc4.0` vs `sharc4.1`) and different toolchains — don't mix commands between them.
- **Two OpenMolcas builds exist**: Chapter 1 (ParamShivay, serial/basic CMake config) and Chapter 7 (Param Rudra, full MPI + Global Arrays build). Chapter 7 is the more recent, HPC-parallel installation.
- **wfoverlap** appears in three places: bundled inside Chapter 4's SHARC 4.0 (`make` only, no special MKL handling needed there), Chapter 6's optional test step (`make test`), and Chapter 8 Part B (the fully documented Param Rudra build with the `MKLROOT` fix).
- **Outstanding `make test` runs** are still pending for: WFoverlap in Chapter 8, and the full OpenMolcas MPI runtime validation in Chapter 7 (§7.26).
