---
layout: post
title:  "Install-CUTEST-for-MATLAB"
date:   2021-07-04 00:00:00 +0800
---
I installed cutest --with-matlab generally following the instructions of [Homebrew's CUTEst Tap](https://github.com/optimizers/homebrew-cutest) and [Xi's blog](http://xihey.com/2017/12/12/Installing-CUTEst-on-MacOSX-with-Matlab-Python/), perhaps with some different procedures from those two instructions.

The installlation is based on Matlab R2021a, macOS Big Sur 11.4, and Xcode Version 12.5.1. 

0. Prepare Xcode. I download Xcode from App store, it could take time (~11GB). Open the Xcode IDE after downloading, and agree the license agreement (has to be done).

1. Install CUTEst. Run the below commands in a terminal (from [Homebrew's CUTEst Tap](https://github.com/optimizers/homebrew-cutest))
```shell
$ brew tap optimizers/cutest
$ brew install cutest --with-matlab
$ brew install mastsif  # If you want the whole SIF collection.
$ brew install maros_mezaros #If you want the Maros-Mezaros collection
$ brew install netlib #If you want the NETLIB LP collection
```
2. Then we need to add some enviroment variables. First, run the below commands in a terminal
```shell 
$ for f in "archdefs" "mastsif" "sifdecode" "cutest"; do \
$ echo ". $(brew --prefix $f)/$f.bashrc" >> ~/.bashrc; \
$ done
```
, this is equivalent to adding below lines to ~/.bashrc
```
. /usr/local/opt/archdefs/archdefs.bashrc
. /usr/local/opt/mastsif/mastsif.bashrc
. /usr/local/opt/sifdecode/sifdecode.bashrc
. /usr/local/opt/cutest/cutest.bashrc
```
And then add the environment variable of your Matlab path, i.e., adding below line to ~/.bashrc.
```shell
export MYMATLAB="/Applications/MATLAB_R2021a.app"
```
You should use your MATLAB version if it is not R20201a. You can check the definition of these variables, by opening a new terminal, and execute
```shell
$ echo $MYMATLAB
```
If it print nothing, your terminal might not source ~/.bashrc automatically. The reason might relate to login shell vs non-login shell (see [why-doesnt-bashrc-run-automatically](https://apple.stackexchange.com/questions/12993/why-doesnt-bashrc-run-automatically)). Try add below line to ~/.bash_profile
```shell
$ source ~/.bashrc
```
and repeat opening a new terminal and executing
```shell
$ echo $MYMATLAB
```
3. Test. Now we can use cutest with a sample problem. Execute the command under any directory
```shell
$ cutest2matlab_osx LUBRIFC
```
The printout is like
    ```python
    #!/bin/bash -vx
    ###############################################################################
    #
    # cutest2matlab_osx: build the bridge between CUTEst and Matlab on OSX
    # without relying on Matlab gfortran support
    #
    # Adapted from cutest2matlab by D. Orban, June 2017. Original version by
    # Nick Gould for GALAHAD Productions, January 2013
    #
    ###############################################################################
    
    function help() {
      echo 'Use this script on OSX with gfortran and Matlab R2016a or later.'
      echo 'The script *may* work with earlier versions of Matlab but was not tested.'
      echo
      echo "Use: $(basename $0) PROBLEM[.SIF]"
      exit 0
    }
    
    [[ $# > 0 && ("$1" == '-h' || "$1" == '--help') ]] && help
    + [[ 1 > 0 ]]
    + [[ LUBRIFC == \-\h ]]
    + [[ LUBRIFC == \-\-\h\e\l\p ]]
    
    if [[ -z "$MYMATLABARCH" ]]; then
      if [[ -z "$MYARCH" ]]; then
        echo ' neither environment variable MYMATLABARCH nor MYARCH is set.'
        echo ' Set MYMATLABARCH as a gfortran-installed version from the list '
        /bin/ls -1 $CUTEST/versions 2>/dev/null
        echo ' and re-run.'
        exit 1
      else
        ARCH=${MYARCH}
      fi
    else
      ARCH=${MYMATLABARCH}
    fi
    + [[ -z mac64.osx.gfo ]]
    + ARCH=mac64.osx.gfo
    
    #  check that the architecture provided in MYMATLABARCH or MYARCH exists
    
    if [[ ! -e $CUTEST/versions/$ARCH ]] ; then
        echo ' The architecture privided by the environment variables MYMATLABARCH'
        echo '  and MYARCH has not been installed. Install a gfortran version of'
        echo ' CUTEst unsing install_cutest and re-run.'
        exit 2
    fi
    + [[ ! -e /usr/local/opt/cutest/libexec/versions/mac64.osx.gfo ]]
    
    #  check that a gfortran version is used (as this is what Matlab supports!)
    
    if [[ ${ARCH##*.} != 'gfo' && ${ARCH##*.} != 'gfo47' ]] ; then
        echo ' The architecture privided by the environment variables MYMATLABARCH'
        echo ' (or otherwise MYARCH) must use the gfortran compiler. Install a '
        echo ' gfortran version of CUTEst unsing install_cutest and re-run.'
        exit 3
    fi
    + [[ gfo != \g\f\o ]]
    
    . ${ARCHDEFS}/compiler.${ARCH}
    + . /usr/local/opt/archdefs/libexec/compiler.mac64.osx.gfo
    # GNU gfortran compiler for 64-bit Macs under OSX
    #
    # Fortran compilation and loading
    #
    
    FORTRAN='gfortran'
    ++ FORTRAN=gfortran
    BASIC='-c -fPIC -fno-second-underscore -flat_namespace'
    ++ BASIC='-c -fPIC -fno-second-underscore -flat_namespace'
    MBASIC='-fPIC -fno-second-underscore -flat_namespace'
    ++ MBASIC='-fPIC -fno-second-underscore -flat_namespace'
    LIBCMD=''
    ++ LIBCMD=
    MODCMD='-I$MOD'
    ++ MODCMD='-I$MOD'
    MODCMDB=$MODCMD
    ++ MODCMDB='-I$MOD'
    MVMODS='$(MV) -f $(OBJ)/*.mod $(MOD)/ || true'
    ++ MVMODS='$(MV) -f $(OBJ)/*.mod $(MOD)/ || true'
    OPTIMIZATION='-O'
    ++ OPTIMIZATION=-O
    NOOPTIMIZATION='-O0'
    ++ NOOPTIMIZATION=-O0
    DEBUG=
    ++ DEBUG=
    OPENMP='-fopenmp'
    ++ OPENMP=-fopenmp
    F77='-ffixed-form'
    ++ F77=-ffixed-form
    F90=''
    ++ F90=
    F95=''
    ++ F95=
    NOFMAIN=''
    ++ NOFMAIN=
    CCONDEF=
    ++ CCONDEF=
    USUAL=
    ++ USUAL=
    SPECIAL=
    ++ SPECIAL=
    F77SUFFIX=f90
    ++ F77SUFFIX=f90
    F95SUFFIX=f90
    ++ F95SUFFIX=f90
    CUDA=
    ++ CUDA=
    CUDACOMP=$FORTRAN
    ++ CUDACOMP=gfortran
    CUDAARCH=
    ++ CUDAARCH=
    CUDACODE=
    ++ CUDACODE=
    CUDABASIC=
    ++ CUDABASIC=
    CUDALIBS=
    ++ CUDALIBS=
    CUDAOPENMP=$OPENMP
    ++ CUDAOPENMP=-fopenmp
    TIMER=GEN
    ++ TIMER=GEN
    BLAS=
    ++ BLAS=
    LAPACK=
    ++ LAPACK=
    OMP4=no
    ++ OMP4=no
    IEEECK=
    ++ IEEECK=
    HSL=
    ++ HSL=
    SPRAL='-lgalahad_spral'
    ++ SPRAL=-lgalahad_spral
    METIS='-lgalahad_metis'
    ++ METIS=-lgalahad_metis
    PARDISO='-lgalahad_pardiso'
    ++ PARDISO=-lgalahad_pardiso
    WSMP='-lgalahad_wsmp'
    ++ WSMP=-lgalahad_wsmp
    NOT95=IS95
    ++ NOT95=IS95
    NOT64=IS64
    ++ NOT64=IS64
    BINSHELL=sh
    ++ BINSHELL=sh
    
    # decode
    [[ $# > 0 ]] && sifdecoder -A $ARCH $1
    + [[ 1 > 0 ]]
    + sifdecoder -A mac64.osx.gfo LUBRIFC
    
     Problem name: LUBRIFC
    
     Double precision version will be formed
    
     The objective function uses 1 nonlinear group
    
     There are 1251 linear equality constraints
     There are 1249 nonlinear equality constraints
    
     There are 1251 free variables
     There are 1249 variables bounded only from below
     There are 1249 variables bounded from below and above
     There are 2 fixed variables
    
    
     File successfully decoded
    
    # build small shared library specific to problem being decoded
    EXTERF=$([[ -f "EXTER.f" ]] && echo "EXTER.f" || echo "")
    [[ -f "EXTER.f" ]] && echo "EXTER.f" || echo ""
    ++ [[ -f EXTER.f ]]
    ++ echo EXTER.f
    + EXTERF=EXTER.f
    EXTERO=$([[ -f "EXTER.f" ]] && echo "EXTER.o" || echo "")
    [[ -f "EXTER.f" ]] && echo "EXTER.o" || echo ""
    ++ [[ -f EXTER.f ]]
    ++ echo EXTER.o
    + EXTERO=EXTER.o
    ${FORTRAN} ${BASIC} ${F77} -I${CUTEST}/modules/${MYARCH} ELFUN.f RANGE.f GROUP.f $EXTERF
    + gfortran -c -fPIC -fno-second-underscore -flat_namespace -ffixed-form -I/usr/local/opt/cutest/libexec/modules/mac64.osx.gfo ELFUN.f RANGE.f GROUP.f EXTER.f
    PROB=$(basename $1 .SIF)
    basename $1 .SIF
    ++ basename LUBRIFC .SIF
    + PROB=LUBRIFC
    ${FORTRAN} -shared -undefined dynamic_lookup -o lib${PROB}.dylib ELFUN.o RANGE.o GROUP.o $EXTERO
    + gfortran -shared -undefined dynamic_lookup -o libLUBRIFC.dylib ELFUN.o RANGE.o GROUP.o EXTER.o
    
    # build and link mex file
    [[ -f ${PWD}/mcutest.o ]] || ${MYMATLAB}/bin/mex -c -I${CUTEST}/include ${CUTEST}/src/matlab/mcutest.c
    + [[ -f /Users/wangqi/Desktop/cutest/mcutest.o ]]
    + /Applications/MATLAB_R2021a.app/bin/mex -c -I/usr/local/opt/cutest/libexec/include /usr/local/opt/cutest/libexec/src/matlab/mcutest.c
    Building with 'Xcode with Clang'.
    /usr/local/opt/cutest/libexec/src/matlab/mcutest.c:1196:9: warning: '/*' within block comment [-Wcomment]
            /* Finalize sparse data structure for sparse gradient */
            ^
    /usr/local/opt/cutest/libexec/src/matlab/mcutest.c:1197:53: warning: '/*' within block comment [-Wcomment]
            /* for (i = 0; i < nnzgci; i++) ir[i]--;    /* 0-based indexing */
                                                        ^
    2 warnings generated.
    
    MEX completed successfully.
    ${MYMATLAB}/bin/mex -I${CUTEST}/include -output mcutest -L${PWD} -l${PROB} -lcutest  mcutest.o
    + /Applications/MATLAB_R2021a.app/bin/mex -I/usr/local/opt/cutest/libexec/include -output mcutest -L/Users/wangqi/Desktop/cutest -lLUBRIFC -lcutest mcutest.o
    Building with 'Xcode with Clang'.
    ld: warning: dylib (/Users/wangqi/Desktop/cutest/libLUBRIFC.dylib) was built for newer macOS version (11.4) than being linked (10.14)
    ld: warning: dylib (/usr/local/lib/libcutest.dylib) was built for newer macOS version (11.4) than being linked (10.14)
    
    ld: warning: dylib (/Users/wangqi/Desktop/cutest/libLUBRIFC.dylib) was built for newer macOS version (11.4) than being linked (10.14)ld: warning: dylib (/usr/local/lib/libcutest.dylib) was built for newer macOS version (11.4) than being linked (10.14)
    
    MEX completed successfully.
    
    exit 0
    + exit 0
    ```
and several files are produced:
```
AUTOMAT.d         ELFUN.o           EXTER.o           GROUP.o           RANGE.f           libLUBRIFC.dylib  mcutest.o
ELFUN.f           EXTER.f           GROUP.f           OUTSDIF.d         RANGE.o           mcutest.mexmaci64
```
Now you can load the decoded problem in Matlab:
    ```
    $ matlab #start a matlab iterface
    >> prob = cutest_setup() % stucture prob will be shown
       prob =
    
            n: 3751                 % number of variables
            m: 2500                 % number of constraints
         nnzh: 8749                 % number of nonzeros in Hessian of Lagrangian
         nnzj: 1572494              % number of nonzeros in constraint Jacobian
            x: [3751x1 double]      % initial guess
           bl: [3751x1 double]      % lower bounds on variables
           bu: [3751x1 double]      % upper bounds on variables
            v: [2500x1 double]      % initial Lagrange multipliers
           cl: [2500x1 double]      % left-hand side of constraints
           cu: [2500x1 double]      % right-hand side of constraints
       equatn: [2500x1 logical]     % array indicating equality constraints
       linear: [2500x1 logical]     % array indicating linear constraints
         name: 'LUBRIFC   '         % problem name
    
    >> [f,g] = cutest_obj(prob.x);
    ```
See `$CUTEST/src/matlab/README.matlab` for more information on the Matlab interface.
4. The list of problems can be found in directory `/usr/local/opt/mastsif/share/mastsif/` 
