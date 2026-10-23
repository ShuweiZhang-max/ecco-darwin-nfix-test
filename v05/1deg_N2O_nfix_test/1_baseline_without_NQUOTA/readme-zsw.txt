# MITgcm ECCO-Darwin Simulation Guide (LLC90 1-degree)

This document outlines the workflow for compiling and running the 1-degree ECCO-Darwin model.

## 1. Code Preparation
Initialize the project directory and clone the required repositories:
  mkdir darwin_simulation
  cd darwin_simulation
  git clone --branch darwin_ckpt68d_at_c66g https://github.com/jahn/darwin3
  git clone --depth 1 https://github.com/MITgcm-contrib/ecco_darwin.git
  cd darwin3

## 2. Compilation
Create the build directory and link the modified source codes.
  mkdir build
  cd build
  cp ../code_darwin_new/DARWIN_DIAGS.h .
  cp ../code_darwin_new/darwin_plankton.F .
  cp ../code_darwin_new/darwin_forcing.F .
  cp ../code_darwin_new/darwin_diag*init* .
  cp ../1deg_code/code_darwin_v4r4_n2o/darwin_forcing.F .
  ln -sf ../code_darwin_new/* .
  ln -sf ../code_darwin_new_add_diags/* .
  rm DIAGNOSTICS_SIZE*

  ../tools/genmake2 -mpi -of ../tools/build_options/linux_amd64_ifort+mpi_ice_hpc2 \
    -mo '../1deg_code/code_darwin_v4r4_n2o ../1deg_code/code_darwin_v4r4 ../1deg_code/code_v4r4'
  make depend
  make -j 16

(Note: If successful, "=== Done ===" will appear and the 'mitgcmuv' executable will be generated.)

## 3. Setting Up the Run Directory
Create the 'run' folder parallel to 'build', and link/copy the necessary input and forcing files.
  cd ..
  mkdir run
  cd run

  cp /.../1deg_code/input_v4r4/* .
  rm data data.pkg data.diagnostics
  cp /.../1deg_code/input_darwin_v4r4/* .
  cp /.../1deg_code/input_darwin_v4r4_new/* .
  cp /.../input_new/* .

  INPUTDIR="/THL7/home/yfeng1982_5/darwin_simulation/data/LLC90/Release4"
  ln -sf ../build/mitgcmuv .
  ln -s ${INPUTDIR}/input_bin/* .
  ln -sf /.../data/apN2O_B2017/*1992* .
  ln -sf /.../data/apN2O_B2017/*1991* .
  ln -sf /THL7/home/yfeng1982_5/darwin_simulation/data/NOAA_MBL/*1992* .
  ln -sf /THL7/home/yfeng1982_5/darwin_simulation/data/LLC90/Release5/V4r5/* .

  mv pickup_ptracers.0000000001.35tra.2.data pickup_ptracers.0000000002.data
  mv pickup_ptracers.0000000001.35tra.meta pickup_ptracers.0000000002.meta
  rm pickup_ptracers.0000000001.*

  mkdir diags diags/monthly diags/budget

  yhbatch run_llc90_v4r4_n2o.sh
  yhq
