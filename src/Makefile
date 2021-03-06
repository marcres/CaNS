# fortran compiler
FC   = mpif90
# flags
BIG := # -mcmodel=medium
DBG := # -g -fbacktrace -Wall -O0 -Wextra -pedantic -fcheck=all
OMP := #-mp # -fopenmp
OPT := -O3 -Mcuda=ccall,cuda10.0
#OPT := -O3 -Mcuda=cc70,nofma
OTH := -DTIMING -DGPU_MPI -DEPA2A -DTIMING -DUSE_CUDA -DUSE_NVTX -DEPHC #-DDEBUG #-DIMPDIFF
PROF := #-pg
FFLAGS := $(OPT) $(BIG) $(DBG) $(PROF) $(OMP) $(OTH)
TARGET = cans
#
OBJ = bound.o chkdiv.o chkdt.o common_mpi.o correc.o debug.o fft.o fftw.o fillps.o initflow.o initgrid.o initmpi.o initsolver.o load.o main.o mom.o momd.o moms.o output.o param.o rk.o sanity.o solver.o decomp_2d.o io.o types.o nvtx.o
#
LIBS = -lfftw3 -lfftw3_threads -Mcudalib=cufft -L/usr/local/cuda/lib64 -lnvToolsExt 
#
all: $(TARGET)
#
$(TARGET): $(OBJ)
	$(FC) $(FFLAGS) $(OBJ) $(LIBS) -o $(TARGET)
# src, run and data directories
SRCDIR := $(pwd)
RUNDIR := $(SRCDIR)../run
DATDIR := $(RUNDIR)/data
#
run: $(TARGET)
	@mkdir -p $(RUNDIR) $(DATDIR)
	@cp $(TARGET) $(RUNDIR)
	@cp dns.in $(RUNDIR)
	@printf "\nExecutable file $(TARGET) and input file dns.in copied to run folder $(RUNDIR)\n"
#
.PHONY: clean
clean:
	rm -rf *.o *.mod *dSYM $(TARGET)
# dependencies
bound.o: bound.f90 common_mpi.o nvtx.o types.o
	$(FC) $(FFLAGS) -cpp -c  bound.f90 
chkdiv.o: chkdiv.f90 common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  chkdiv.f90 
chkdt.o: chkdt.f90 common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  chkdt.f90 
common_mpi.o: common_mpi.f90 param.o types.o
	$(FC) $(FFLAGS) -cpp -c  common_mpi.f90 
correc.o: correc.f90 types.o
	$(FC) $(FFLAGS) -cpp -c  correc.f90 
debug.o: debug.f90 common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  debug.f90 
fillps.o: fillps.f90 types.o
	$(FC) $(FFLAGS) -cpp -c  fillps.f90
fft.o: fft.f90 common_mpi.o fftw.o types.o
	$(FC) $(FFLAGS) -cpp -c  fft.f90
fftw.o: fftw.f90 param.o
	$(FC) $(FFLAGS) -cpp -c  fftw.f90
initflow.o: initflow.f90 common_mpi.o decomp_2d.o types.o
	$(FC) $(FFLAGS) -Mnovect -cpp -c  initflow.f90 
initgrid.o: initgrid.f90 param.o types.o
	$(FC) $(FFLAGS) -cpp -c  initgrid.f90 
initmpi.o: initmpi.f90 decomp_2d.o common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  initmpi.f90
initsolver.o: initsolver.f90 common_mpi.o fft.o param.o types.o
	$(FC) $(FFLAGS) -cpp -c  initsolver.f90 
load.o: load.f90 common_mpi.o decomp_2d.o io.o types.o
	$(FC) $(FFLAGS) -cpp -c  load.f90 
main.o: main.f90 bound.o chkdiv.o chkdt.o debug.o fft.o fillps.o initflow.o initflow.o initgrid.o initmpi.o initsolver.o load.o output.o param.o rk.o sanity.o solver.o types.o out1d.h90 out2d.h90 out3d.h90 nvtx.o
	$(FC) $(FFLAGS) -cpp -c  main.f90
mom.o: mom.f90 common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  mom.f90
momd.o: momd.f90 common_mpi.o types.o
	$(FC) $(FFLAGS) -cpp -c  momd.f90
moms.o: moms.f90 types.o
	$(FC) $(FFLAGS) -cpp -c  moms.f90
output.o: output.f90 io.o types.o
	$(FC) $(FFLAGS) -cpp -c  output.f90
param.o: param.f90 types.o
	$(FC) $(FFLAGS) -cpp -c  param.f90
rk.o: rk.f90 debug.o mom.o momd.o moms.o param.o types.o nvtx.o
	$(FC) $(FFLAGS) -cpp -c  rk.f90
sanity.o: sanity.f90 bound.o chkdiv.o common_mpi.o correc.o debug.o decomp_2d.o fft.o fillps.o initflow.o initmpi.o initsolver.o param.o solver.o types.o
	$(FC) $(FFLAGS) -cpp -c  sanity.f90
solver.o: solver.f90 fft.o param.o types.o
	$(FC) $(FFLAGS) -cpp -c  solver.f90
types.o: types.f90
	$(FC) $(FFLAGS) -cpp -c  $<
# 2decomp&fft library
FFLAGS_2DECOMP :=# -DOVERWRITE -DEVEN
2decompdir=2decomp/
decomp_2d.o: $(2decompdir)decomp_2d.f90 nvtx.o
	$(FC) $(FFLAGS) -cpp $(FFLAGS_2DECOMP) -c $(2decompdir)decomp_2d.f90
io.o: $(2decompdir)io.f90 decomp_2d.o
	$(FC) $(FFLAGS) -cpp $(FFLAGS_2DECOMP) -c $(2decompdir)io.f90
nvtx.o: nvtx.f90
	$(FC) $(FFLAGS) -cpp $(FFLAGS_2DECOMP) -c nvtx.f90
