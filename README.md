# Prerequisites:
1. Python 2 (2.6 or 2.7) or 3 (3.3 - 3.6)
2. A C/C++ compiler
3. The git and curl commands.
4. If using the gpg subcommand, gnupg2 is required.
5. Currently needs to be run from a filesystem that supports flock locking semantics. 

# Installation:
Clone the github repository:
``` bash
$ git clone https://github.com/llnl/spack.git
```

# Set up the environment and enable shell support:

``` bash
$ export SPACK_ROOT=/path/to/spack
$ . $SPACK_ROOT/share/spack/setup-env.sh
```

This adds Spack to the ``` bash $PATH ``` and adds the path to Spack environment modules to the $MODULEPATH and allows Spack to execute other commands.


To install Spack in a different directory:

$ spack bootstrap /path/to/prefix



To check available compilers:
~> spack compilers
==> Available compilers
-- cce CNL-any --------------------------------------------------
cce@8.5.7  cce@8.5.2  cce@8.5.0  cce@8.4.5  cce@8.4.3  cce@8.4.1  cce@8.3.14  cce@8.3.12  cce@8.3.8
cce@8.5.5  cce@8.5.1  cce@8.4.6  cce@8.4.4  cce@8.4.2  cce@8.4.0  cce@8.3.13  cce@8.3.10

-- gcc CNL-any --------------------------------------------------
gcc@6.3.0  gcc@6.2.0  gcc@6.1.0  gcc@5.3.0  gcc@5.2.0  gcc@5.1.0  gcc@4.9.3  gcc@4.9.2  gcc@4.8.1

-- gcc suse_linux11-x86_64 --------------------------------------
gcc@6.2.0  gcc@5.1.0  gcc@4.7  gcc@4.3

-- intel CNL-any ------------------------------------------------
intel@17.0.1.132  intel@16.3.3.210  intel@15.0.2.164  intel@15.0.1.133  intel@14.0.4.211

-- intel suse_linux11-x86_64 ------------------------------------
intel@15.0.2

Spack did not correctly detect that CDL is Sandybridge, you will need to configure it manually (below) and change the target of the CDL from x86_64 to Sandybridge, otherwise, you will get the following message when installing on target OS x86_64:
~> spack install --fake libdwarf %intel  os=suse_linux11 target=x86_64  ^libelf os=suse_linux11 target=x86_64
==> Installing libelf
==> Error: ValueError: Can't recreate arch for spec cray-suse_linux11-x86_64 on current arch cray-CNL-haswell; spec architecture is too different
/home/hammadsa/spack/lib/spack/spack/package.py:869, in architecture:
     863      @property
     864      def architecture(self):
     865          """Get the spack.architecture.Arch object that represents the
     866          environment in which this package will be built."""
     867          if not self.spec.concrete:
     868              raise ValueError("Can only get the arch for concrete package.")
  >> 869          return spack.architecture.arch_for_spec(self.spec.architecture)

hammadsa@cdl2:~> spack compilers
==> Available compilers
-- cce CNL-any --------------------------------------------------
cce@8.5.7  cce@8.5.2  cce@8.5.0  cce@8.4.5  cce@8.4.3  cce@8.4.1  cce@8.3.14  cce@8.3.12  cce@8.3.8
cce@8.5.5  cce@8.5.1  cce@8.4.6  cce@8.4.4  cce@8.4.2  cce@8.4.0  cce@8.3.13  cce@8.3.10

-- gcc CNL-any --------------------------------------------------
gcc@6.3.0  gcc@6.2.0  gcc@6.1.0  gcc@5.3.0  gcc@5.2.0  gcc@5.1.0  gcc@4.9.3  gcc@4.9.2  gcc@4.8.1

-- gcc suse_linux11-sandybridge ---------------------------------
gcc@6.2.0  gcc@5.1.0  gcc@4.7  gcc@4.3

-- intel CNL-any ------------------------------------------------
intel@17.0.1.132  intel@16.3.3.210  intel@15.0.2.164  intel@15.0.1.133  intel@14.0.4.211

-- intel suse_linux11-sandybridge -------------------------------
intel@15.0.2

hammadsa@cdl2:~> spack compiler info cce@8.5.7
cce@8.5.7:
        paths:
                cc = cc
                cxx = CC
                f77 = ftn
                fc = ftn
        modules  = ['PrgEnv-cray', u'cce/8.5.7']
        operating system  = CNL
hammadsa@cdl2:~> spack compiler info intel@17.0.1.132
intel@17.0.1.132:
        paths:
                cc = cc
                cxx = CC
                f77 = ftn
                fc = ftn
        modules  = ['PrgEnv-intel', u'intel/17.0.1.132']
        operating system  = CNL
hammadsa@cdl2:~> spack compiler info gcc@6.2.0
gcc@6.2.0:
        paths:
                cc = cc
                cxx = CC
                f77 = ftn
                fc = ftn
        modules  = ['PrgEnv-gnu', u'gcc/6.2.0']
        operating system  = CNL
gcc@6.2.0:
        paths:
                cc = /opt/gcc/6.2.0/bin/gcc
                cxx = /opt/gcc/6.2.0/bin/g++
                f77 = /opt/gcc/6.2.0/bin/gfortran
                fc = /opt/gcc/6.2.0/bin/gfortran
        modules  = []
        operating system  = suse_linux11

Notice Spack Cray Compiler detection for CDL and CNL. For CNL, Spack detects the compilers using module avail command (Craype-). Then it writes the appropriate PrgEnv and compiler module name to compilers.yaml and sets the paths to the C, C++, and Fortran compilers with Cray’s compiler wrapper names (i.e. cc, CC, ftn). During build time, Spack will load the correct PrgEnv and compiler module and will call appropriate wrapper.

Manual compiler configuration:
In case auto-detection fails, manually configure the compilers by editing  ~/.spack/compilers.yaml using the following command:
~> spack config edit compilers

If a compiler has not been detected, pass the path to where the compiler is installed to spack compiler find or spack compiler add
~> spack compiler find /path/to/compiler

Listing available packages in Spack repo and available versions:

Ex 1: List all packages. Currently 1652 packages available in Spack repo.

hammadsa@cdl2:~> spack list
==> 1652 packages.
abinit                           llvm                                   py-werkzeug
abyss                            llvm-lld                               py-wheel
ack                              llvm-openmp-ompt                       py-widgetsnbextension
activeharmony                    lmdb                                   py-wrapt
adept-utils                      lmod                                   py-xarray
adios                            lndir                                  py-xlrd


Ex 2: Search for all packages whose name contains libdwarf:

hammadsa@cdl2:~> spack list libdwarf
==> 1 packages.
libdwarf


Ex 3: Search for all packages whose name or description contains lib:

hammadsa@cdl2:~> spack list -d lib
==> 459 packages.
adept-utils               libbeagle         lua                 py-pywavelets
adlbx                     libbson           lua-luafilesystem   py-readme-renderer
amrex                     libcanberra       lwgrp               py-rope
ant                       libcap            lzo                 py-rpy2
argtable                  libcerf           magma               py-rtree
armadillo                 libcircle         mallocmc            py-scipy
atlas                     libconfig         matio               py-seaborn



Ex 4: show available versions of libdwarf both safe (for which Spack has the checksum) and remote ( those Spack could scrape from the web).

hammadsa@cdl2:~> spack versions libdwarf
==> Safe versions (already checksummed):
  20160507  20130729  20130207  20130126
==> Remote versions (not yet checksummed):
  20170709  20161021  20160923  20151114  20150507  20150112  20140208
  20170416  20161001  20160613  20150915  20150310  20140519  20140131
  20161124  20160929  20160115  20150913  20150115  20140413



To find more information on a particular package:

hammadsa@cdl2:~> spack info elk
MakefilePackage:    elk

Description:
    An all-electron full-potential linearised augmented-plane wave (FP-LAPW)
    code with many advanced features.

Homepage:           http://elk.sourceforge.net/

Safe versions:
    3.3.17    https://sourceforge.net/projects/elk/files/elk-3.3.17.tgz

Variants:
    Name [Default]    Allowed values    Description


    blas [on]         True, False       Build with custom BLAS library

    fft [on]          True, False       Build with custom FFT library

    lapack [on]       True, False       Build with custom LAPACK library

    libxc [on]        True, False       Link to Libxc functional library

    mpi [on]          True, False       Enable MPI parallelism

    openmp [on]       True, False       Enable OpenMP support


Installation Phases:
    edit    build    install

Build Dependencies:
    blas  fftw  lapack  libxc  mpi

Link Dependencies:
    blas  fftw  lapack  libxc  mpi

Run Dependencies:
    None

Virtual Packages:
    None



Spack Spec:

Shows the spec and the concrete spec (what’s going to be installed).
 a spec consists of the following pieces:
Package name identifier.
@ Optional version specifier (@1.2:1.4)
% Optional compiler specifier, with an optional compiler version (gcc or gcc@4.7.3)
+ or - or ~ Optional variant specifiers (+debug, -qt, or ~qt) for boolean variants
name=<value> Optional variant specifiers that are not restricted to boolean variants
name=<value> Optional compiler flag specifiers. Valid flag names are cflags, cxxflags, fflags, cppflags, ldflags, and ldlibs.
target=<value> os=<value> Optional architecture specifier (target=haswell os=CNL10)
^ Dependency specs (^callpath@1.1)

Ex 1: Show the libdwarf unconstrained spec

hammadsa@cdl2:~> spack spec libdwarf
Input spec
--------------------------------
libdwarf

Normalized
--------------------------------
libdwarf
    ^elf

Concretized
--------------------------------
libdwarf@20160507%gcc@5.1.0 arch=cray-CNL-haswell
    ^elfutils@0.163%gcc@5.1.0 arch=cray-CNL-haswell

Ex 2: Show libdwarf version 20130729 spec when for cce compiler.

hammadsa@cdl2:~> spack spec libdwarf@20130729 %cce
Input spec
--------------------------------
libdwarf@20130729%cce

Normalized
--------------------------------
libdwarf@20130729%cce
    ^elf

Concretized
--------------------------------
libdwarf@20130729%cce@8.3.10 arch=cray-CNL-haswell
    ^elfutils@0.163%cce@8.3.10 arch=cray-CNL-haswell



Ex 3: Show libdwarf spec for cce compilers and libelf dependency (instead of elfutils)

hammadsa@cdl2:~> spack spec libdwarf %cce ^libelf
Input spec
--------------------------------
libdwarf%cce
    ^libelf

Normalized
--------------------------------
libdwarf%cce
    ^libelf

Concretized
--------------------------------
libdwarf@20160507%cce@8.3.10 arch=cray-CNL-haswell
    ^libelf@0.8.13%cce@8.3.10 arch=cray-CNL-haswell

Ex 4: Show libdwarf spec for cce compiler on CDL (notice target and OS). Notice that Spack was not able to create a concretized spec because cce is not avaiiable for CDL.

hammadsa@cdl2:~> spack spec libdwarf %cce os=suse_linux11 target=sandybridge  ^libelf
Input spec
--------------------------------
libdwarf%cce arch=cray-suse_linux11-sandybridge
    ^libelf

Normalized
--------------------------------
libdwarf%cce arch=cray-suse_linux11-sandybridge
    ^libelf

Concretized
--------------------------------
==> Error: No compilers with spec cce found for operating system suse_linux11 and target sandybridge.
Run 'spack compiler find' to add compilers.



Ex 5: Now,  Show libdwarf spec for intel compiler on CDL (notice target and OS).

hammadsa@cdl2:~>  spack spec libdwarf %intel  os=suse_linux11 target=sandybridge  ^libelf os=suse_linux11 target=sandybridge

Input spec
--------------------------------
libdwarf%intel arch=cray-suse_linux11-sandybridge
    ^libelf arch=cray-suse_linux11-sandybridge

Normalized
--------------------------------
libdwarf%intel arch=cray-suse_linux11-sandybridge
    ^libelf arch=cray-suse_linux11-sandybridge

Concretized
--------------------------------
libdwarf@20160507%intel@15.0.2 arch=cray-suse_linux11-sandybridge
    ^libelf@0.8.13%intel@15.0.2 arch=cray-suse_linux11-sandybridge

Ex 6: Show libdwarf spec for intel compiler with the compiler flag cppflags="-O3" on CDL

hammadsa@cdl2:~> spack spec libdwarf %intel cppflags="-O3" os=suse_linux11 target=sandybridge  ^libelf os=suse_linux11 target=sandybridge
Input spec
--------------------------------
libdwarf%intel cppflags="-O3"  arch=cray-suse_linux11-sandybridge
    ^libelf arch=cray-suse_linux11-sandybridge

Normalized
--------------------------------
libdwarf%intel cppflags="-O3"  arch=cray-suse_linux11-sandybridge
    ^libelf arch=cray-suse_linux11-sandybridge

Concretized
--------------------------------
libdwarf@20160507%intel@15.0.2 cppflags="-O3"  arch=cray-suse_linux11-sandybridge
    ^libelf@0.8.13%intel@15.0.2 cppflags="-O3"  arch=cray-suse_linux11-sandybridge


Now, let’s do a fake installation for all of the above specs using --fake:

hammadsa@cdl2:~> spack install --fake libdwarf
==> Installing elfutils
==> Building elfutils [AutotoolsPackage]
==> Successfully installed elfutils
  Fetch: .  Build: 0.27s.  Total: 0.27s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/elfutils-0.163-gjdij7nrmkv4aklmdfvcb4hiuj6ixyiv
==> Installing libdwarf
==> Building libdwarf [Package]
==> Successfully installed libdwarf
  Fetch: .  Build: 0.37s.  Total: 0.37s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/libdwarf-20160507-vxnpxxtpiipgqjltol764ugupckgoajm
hammadsa@cdl2:~> spack install --fake libdwarf@20130729 %cce
==> Installing elfutils
==> Building elfutils [AutotoolsPackage]
==> Successfully installed elfutils
  Fetch: .  Build: 0.22s.  Total: 0.22s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/elfutils-0.163-q7dpsvytyjmpwr6ztp5dg4lvh7f64eks
==> Installing libdwarf
==> Building libdwarf [Package]
==> Successfully installed libdwarf
  Fetch: .  Build: 0.35s.  Total: 0.35s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/libdwarf-20130729-btyhoaikwjdmavehcaj5u3zt34zmdjey
hammadsa@cdl2:~> spack install --fake libdwarf %cce ^libelf
==> Installing libelf
==> Building libelf [AutotoolsPackage]
==> Successfully installed libelf
  Fetch: .  Build: 0.23s.  Total: 0.23s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/libelf-0.8.13-67oyb2ycbd5p5rzuykh57yzd2sjsg2nz
==> Installing libdwarf
==> Building libdwarf [Package]
==> Successfully installed libdwarf
  Fetch: .  Build: 0.35s.  Total: 0.35s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/libdwarf-20160507-ssa7vqs4oqptva4j2sif5nnafdwo2fx6
hammadsa@cdl2:~> spack install --fake libdwarf %intel  os=suse_linux11 target=sandybridge  ^libelf os=suse_linux11 target=sandybridge
==> Installing libelf
==> Building libelf [AutotoolsPackage]
==> Successfully installed libelf
  Fetch: .  Build: 0.18s.  Total: 0.18s.
[+] /home/hammadsa/spack/opt/spack/cray-suse_linux11-sandybridge/intel-15.0.2/libelf-0.8.13-svbjqog5qh5pvocsa43vetvptzhpfppi
==> Installing libdwarf
==> Building libdwarf [Package]
==> Successfully installed libdwarf
  Fetch: .  Build: 0.24s.  Total: 0.24s.
[+] /home/hammadsa/spack/opt/spack/cray-suse_linux11-sandybridge/intel-15.0.2/libdwarf-20160507-diazx77homt7557d7a6razkrvlcu7ha7


List installed packages:

-dlvf flags show the full dependency DAG, dependency hashes as well as versions, variants and spec compiler-flags.

hammadsa@cdl2:~> spack find -dlvf
==> 10 installed packages.
-- cray-CNL-haswell / cce@8.3.10 --------------------------------
q7dpsvy    elfutils@0.163%cce

btyhoai    libdwarf@20130729%cce
q7dpsvy        ^elfutils@0.163%cce

ssa7vqs    libdwarf@20160507%cce
67oyb2y        ^libelf@0.8.13%cce

67oyb2y    libelf@0.8.13%cce


-- cray-CNL-haswell / gcc@5.1.0 ---------------------------------
gjdij7n    elfutils@0.163%gcc

vxnpxxt    libdwarf@20160507%gcc
gjdij7n        ^elfutils@0.163%gcc


-- cray-suse_linux11-sandybridge / intel@15.0.2 -----------------
diazx77    libdwarf@20160507%intel
svbjqog        ^libelf@0.8.13%intel

pygjlym    libdwarf@20160507%intel cppflags="-O3"
ycckv6k        ^libelf@0.8.13%intel cppflags="-O3"

svbjqog    libelf@0.8.13%intel

ycckv6k    libelf@0.8.13%intel cppflags="-O3"

Uninstall packages:

hammadsa@cdl2:~> spack uninstall libdwarf@20160507
==> Error: libdwarf@20160507 matches multiple packages:

-- cray-CNL-haswell / cce@8.3.10 --------------------------------
ssa7vqs libdwarf@20160507%cce


-- cray-CNL-haswell / gcc@5.1.0 ---------------------------------
vxnpxxt libdwarf@20160507%gcc


-- cray-suse_linux11-sandybridge / intel@15.0.2 -----------------
diazx77 libdwarf@20160507%intel

pygjlym libdwarf@20160507%intel cppflags="-O3"


==> Error: You can either:
    a) use a more specific spec, or
    b) use `spack uninstall --all` to uninstall ALL matching specs.

hammadsa@cdl2:~> spack uninstall libdwarf@20160507%intel
==> Error: libdwarf@20160507%intel matches multiple packages:

-- cray-suse_linux11-sandybridge / intel@15.0.2 -----------------
diazx77 libdwarf@20160507%intel

pygjlym libdwarf@20160507%intel cppflags="-O3"


==> Error: You can either:
    a) use a more specific spec, or
    b) use `spack uninstall --all` to uninstall ALL matching specs.

hammadsa@cdl2:~> spack uninstall /pygjlym
==> The following packages will be uninstalled:

-- cray-suse_linux11-sandybridge / intel@15.0.2 -----------------
pygjlym libdwarf@20160507%intel cppflags="-O3"

==> Do you want to proceed? [y/N] y
==> Successfully uninstalled libdwarf@20160507%intel@15.0.2 arch=cray-suse_linux11-sandybridge /pygjlym

Install openmpi and gcc and elk:

hammadsa@cdl2:~> spack install --fake openmpi %intel
hammadsa@cdl2:~> spack install --fake gcc

hammadsa@cdl2:~> spack find -dlvf elk
==> 1 installed packages.
-- cray-CNL-haswell / gcc@5.1.0 ---------------------------------
j3piwwe    elk@3.3.17%gcc+blas+fft+lapack+libxc+mpi+openmp
43sxeok        ^fftw@3.3.6-pl2%gcc+double+float+long_double+mpi~openmp~pfft_patches~quad
wsn3bg5            ^openmpi@2.1.1%gcc~cuda fabrics=verbs ~java schedulers= ~sqlite3~thread_multiple+vt
u7tg2uh                ^hwloc@1.11.7%gcc~cuda+libxml2+pci
e5biiau                    ^libpciaccess@0.13.5%gcc
sdlmqmp                    ^libxml2@2.9.4%gcc~python
c43vgvc                        ^xz@5.2.3%gcc
jogye4n                        ^zlib@1.2.11%gcc+pic+shared
qmt7e46        ^libxc@3.0.0%gcc
rkbffe4        ^openblas@0.2.19%gcc~openmp+pic+shared



Show providers of virtual dependencies:

hammadsa@cdl2:~> spack providers mpi
    intel-mpi                                         mpich@3:       openmpi         spectrum-mpi
    intel-parallel-studio@cluster.0:cluster.9999+mpi  mvapich2       openmpi@1.6.5
    mpich                                             mvapich2@1.9   openmpi@1.7.5:
    mpich@1:                                          mvapich2@2.0:  openmpi@2.0.0:
hammadsa@cdl2:~> spack providers mpi@2
    intel-mpi                                         mpich@3:      mvapich2@2.0:  openmpi@1.7.5:
    intel-parallel-studio@cluster.0:cluster.9999+mpi  mvapich2      openmpi        openmpi@2.0.0:
    mpich                                             mvapich2@1.9  openmpi@1.6.5  spectrum-mpi
hammadsa@cdl2:~> spack providers mpi@3
    intel-mpi                                         mpich@3:       openmpi         spectrum-mpi
    intel-parallel-studio@cluster.0:cluster.9999+mpi  mvapich2       openmpi@1.7.5:
    mpich                                             mvapich2@2.0:  openmpi@2.0.0:
hammadsa@cdl2:~> spack providers blas
    atlas  intel-mkl  intel-parallel-studio+mkl  netlib-lapack~external-blas  openblas  veclibfort

Providing external packager through ~/.spack/packages.yaml

In this example I’m using cray-mpich as a provider for the MPI virtual dependency and cray-libsci as a provider for BLAS.

hammadsa@cdl2:~> cat ~/.spack/packages.yaml
packages:
  mpich:
    modules:
      mpich@7.2.6%cce@8.4.1  arch=cray-CNL-haswell: cray-mpich
    buildable: False
  openblas:
    modules:
      openblas@13.2.0%cce@8.4.1 arch=ccray-CNL-haswell: cray-libsci
    buildable: False
  all:
    compiler: [cce@8.4.1, gcc@5.1.0, intel@15.0.2.164, intel@15.0.2]
    providers:
      mpi: [mpich]
      blas: [openblas]



hammadsa@cdl2:~> spack install --fake elk +blas ^blas
==> mpich@7.2.6 : has external module in cray-mpich
==> mpich@7.2.6 : is actually installed in /opt/cray/mpt/7.2.6/gni/mpich-cray/8.3
==> mpich@7.2.6 : generating module file
==> mpich@7.2.6 : registering into DB
==> mpich@7.2.6 : has external module in cray-mpich
==> mpich@7.2.6 : is actually installed in /opt/cray/mpt/7.2.6/gni/mpich-cray/8.3
==> mpich@7.2.6 : already registered in DB
==> Installing fftw
==> Building fftw [AutotoolsPackage]
==> Successfully installed fftw
  Fetch: .  Build: 0.46s.  Total: 0.46s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/fftw-3.3.6-pl2-fdpjytou743px5wkrornwt467t32ybub
==> Installing libxc
==> Building libxc [Package]
==> Successfully installed libxc
  Fetch: .  Build: 0.29s.  Total: 0.29s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/libxc-3.0.0-q2efj4xwqzwhdv3neqvekxb4lcxurct7
==> openblas@13.2.0 : has external module in cray-libsci
==> openblas@13.2.0 : is actually installed in /opt/cray
==> openblas@13.2.0 : generating module file
==> openblas@13.2.0 : registering into DB
==> Installing elk
==> Building elk [MakefilePackage]
==> Successfully installed elk
  Fetch: .  Build: 0.93s.  Total: 0.93s.
[+] /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq

Now, see the difference between the 2 elk packages:

hammadsa@cdl2:~> spack find -dlvf elk
==> 2 installed packages.
-- cray-CNL-haswell / cce@8.4.1 ---------------------------------
7xctet2    elk@3.3.17%cce+blas+fft+lapack+libxc+mpi+openmp
fdpjyto        ^fftw@3.3.6-pl2%cce+double+float+long_double+mpi~openmp~pfft_patches~quad
vrpidds            ^mpich@7.2.6%cce+hydra+pmi+romio~verbs
q2efj4x        ^libxc@3.0.0%cce
25capia        ^openblas@13.2.0%cce~openmp+pic+shared


-- cray-CNL-haswell / gcc@5.1.0 ---------------------------------
j3piwwe    elk@3.3.17%gcc+blas+fft+lapack+libxc+mpi+openmp
43sxeok        ^fftw@3.3.6-pl2%gcc+double+float+long_double+mpi~openmp~pfft_patches~quad
wsn3bg5            ^openmpi@2.1.1%gcc~cuda fabrics=verbs ~java schedulers= ~sqlite3~thread_multiple+vt
u7tg2uh                ^hwloc@1.11.7%gcc~cuda+libxml2+pci
e5biiau                    ^libpciaccess@0.13.5%gcc
sdlmqmp                    ^libxml2@2.9.4%gcc~python
c43vgvc                        ^xz@5.2.3%gcc
jogye4n                        ^zlib@1.2.11%gcc+pic+shared
qmt7e46        ^libxc@3.0.0%gcc
rkbffe4        ^openblas@0.2.19%gcc~openmp+pic+shared

To view the complete DAG:

hammadsa@cdl2:~> spack graph /7xctet2
o  elk
|\
| |\
| | |\
o | | |  openblas
 / / /
| | o  fftw
| |/
|/|
o |  mpich
 /
o  libxc
hammadsa@cdl2:~> spack graph elk%gcc
o  elk
|\
| |\
| | |\
| | o |  fftw
| |/ /
|/| |
o | |  openmpi
o | |  hwloc
|\ \ \
| |\ \ \
| o | | |  libxml2
|/| | | |
| |\ \ \ \
| o | | | |  zlib
|  / / / /
| o | | |  xz
|  / / /
| o | |  libpciaccess
|/| | |
| |\ \ \
| o | | |  util-macros
|  / / /
o | | |  pkg-config
 / / /
o | |  libtool
o | |  m4
| o |  libxc
|  /
o |  libsigsegv
 /
o  atlas

Modulefiles:

Spack automatically generates the tcl modulefiles. However, the standard naming contains the SHA hash.


hammadsa@cdl2:~> module avail elk-3.3.17

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
elk-3.3.17-cce-8.4.1-7xctet2 elk-3.3.17-gcc-5.1.0-j3piwwe
hammadsa@cdl2:~> module avail gcc-7.1.0

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
gcc-7.1.0-gcc-5.1.0-wyjyule
hammadsa@cdl2:~> module avail openmpi-2.1.1

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
openmpi-2.1.1-gcc-5.1.0-wsn3bg5    openmpi-2.1.1-intel-15.0.2-2bjobfc
This is how the module file for gcc and openmpi looked like:

hammadsa@cdl2:~> module show gcc-7.1.0-gcc-5.1.0-wyjyule
-------------------------------------------------------------------
/home/hammadsa/spack/share/spack/modules/cray-CNL-haswell/gcc-7.1.0-gcc-5.1.0-wyjyule:

module-whatis    gcc @7.1.0
prepend-path     PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/bin
prepend-path     CMAKE_PREFIX_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/
prepend-path     LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/lib
prepend-path     LD_LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/lib
prepend-path     CPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/include
prepend-path     MANPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/man
-------------------------------------------------------------------

hammadsa@cdl2:~> module show openmpi-2.1.1-gcc-5.1.0-wsn3bg5
-------------------------------------------------------------------
/home/hammadsa/spack/share/spack/modules/cray-CNL-haswell/openmpi-2.1.1-gcc-5.1.0-wsn3bg5:

module-whatis    openmpi @2.1.1
prepend-path     PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/bin
prepend-path     CMAKE_PREFIX_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/
prepend-path     LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/lib
prepend-path     LD_LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/lib
prepend-path     CPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/include
prepend-path     MANPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/man
-------------------------------------------------------------------


Modulefiles contents and naming conventions can be customize through ~/.spack/modules.yml file

hammadsa@cdl2:~> cat ~/.spack/modules.yaml
modules:
  tcl:
    hash_length: 0
    naming_scheme: '${PACKAGE}/${VERSION}-${COMPILERNAME}-${COMPILERVER}'
    whitelist:
      - gcc
    blacklist:
      - '%gcc@4.8'
    all:
      conflict:
        - '${PACKAGE}'
      suffixes:
        '^openblas': openblas
        '^netlib-lapack': netlib
      filter:
        environment_blacklist: ['CPATH', 'LIBRARY_PATH']
      environment:
        set:
          '${PACKAGE}_ROOT': '${PREFIX}'
    gcc:
      environment:
        set:
          CC: gcc
          CXX: g++
          FC: gfortran
          F90: gfortran
          F77: gfortran
    openmpi:
      environment:
        set:
          SLURM_MPI_TYPE: pmi2
          OMPI_MCA_btl_openib_warn_default_gid_prefix: '0'
    netlib-scalapack:
      suffixes:
        '^openmpi': openmpi
        '^mpich': mpich

Important points about the above modules.yaml:

we instruct Spack to remove the hash from the module name and set the naming scheme of the modules.
we instruct Spack to conflict with any loaded module of the same package name.
We instruct Spack to add the suffix openblas to any module that was installed with the openblas dependency, and the suffix netlib to any module that was installed with the netlib-lapack dependency.
We instruct Spack as well to remove the CPATH and LIBRARY_PATH environment variables from the modulefiles.
We instruct Spack to set additional environment variables for gcc and openmpi modules.
We instruct Spack to NOT generate modulefiles for any modules compiled with gcc 4.8 compiler.
We instruct Spack set a package_ROOT variable that corresponds to the prefix inside the modulefile.

To regenerate the modulefiles and apply the changes:

hammadsa@cdl2:~> spack module refresh -y --module-type tcl
==> Regenerating tcl module files

After applying the changes:

hammadsa@cdl2:~> module avail elk

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
elk/3.3.17-cce-8.4.1-openblas elk-3.3.17-cce-8.4.1-7xctet2
elk/3.3.17-gcc-5.1.0-openblas elk-3.3.17-gcc-5.1.0-j3piwwe

--------------------------------------- /sw/xc40/modulefiles ----------------------------------------
elk/3.0.4
hammadsa@cdl2:~> module avail gcc

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
gcc/7.1.0-gcc-5.1.0         gcc-7.1.0-gcc-5.1.0-wyjyule

----------------------------------------- /opt/modulefiles ------------------------------------------
gcc/4.8.1          gcc/4.9.3          gcc/5.2.0          gcc/6.1.0          gcc/6.3.0
gcc/4.9.2          gcc/5.1.0(default) gcc/5.3.0          gcc/6.2.0
hammadsa@cdl2:~> module avail openmpi

--------------------- /home/hammadsa/spack/share/spack/modules/cray-CNL-haswell ---------------------
openmpi/2.1.1-gcc-5.1.0            openmpi-2.1.1-gcc-5.1.0-wsn3bg5
openmpi/2.1.1-intel-15.0.2         openmpi-2.1.1-intel-15.0.2-2bjobfc

--------------------------------------- /sw/xc40/modulefiles ----------------------------------------
openmpi/1.10.6
hammadsa@cdl2:~> module show gcc/7.1.0-gcc-5.1.0
-------------------------------------------------------------------
/home/hammadsa/spack/share/spack/modules/cray-CNL-haswell/gcc/7.1.0-gcc-5.1.0:

module-whatis    gcc @7.1.0
prepend-path     PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/bin
prepend-path     CMAKE_PREFIX_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/
prepend-path     LD_LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/lib
prepend-path     MANPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls/man
setenv           GCC_ROOT /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls
setenv           CC gcc
setenv           CXX g++
setenv           F90 gfortran
setenv           FC gfortran
setenv           F77 gfortran
conflict         gcc
-------------------------------------------------------------------

hammadsa@cdl2:~> module show openmpi/2.1.1-gcc-5.1.0
-------------------------------------------------------------------
/home/hammadsa/spack/share/spack/modules/cray-CNL-haswell/openmpi/2.1.1-gcc-5.1.0:

module-whatis    openmpi @2.1.1
prepend-path     PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/bin
prepend-path     CMAKE_PREFIX_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/
prepend-path     LD_LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/lib
prepend-path     MANPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb/man
setenv           SLURM_MPI_TYPE pmi2
setenv           OMPI_MCA_BTL_OPENIB_WARN_DEFAULT_GID_PREFIX 0
setenv           OPENMPI_ROOT /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/openmpi-2.1.1-wsn3bg5qvw7rjm2gglildntuztmfuxdb
conflict         openmpi
-------------------------------------------------------------------

hammadsa@cdl2:~> module show elk/3.3.17-cce-8.4.1-openblas
-------------------------------------------------------------------
/home/hammadsa/spack/share/spack/modules/cray-CNL-haswell/elk/3.3.17-cce-8.4.1-openblas:

module-whatis    elk @3.3.17
prepend-path     PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq/bin
prepend-path     CMAKE_PREFIX_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq/
prepend-path     LD_LIBRARY_PATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq/lib
prepend-path     MANPATH /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq/man
setenv           ELK_ROOT /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq
conflict         elk
-------------------------------------------------------------------


Show paths to installed modules:

hammadsa@cdl2:~> spack find -ep
==> 8 installed packages.
-- cray-CNL-haswell / cce@8.3.10 --------------------------------
    libdwarf@20130729  /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/libdwarf-20130729-btyhoaikwjdmavehcaj5u3zt34zmdjey
    libdwarf@20160507  /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.3.10/libdwarf-20160507-ssa7vqs4oqptva4j2sif5nnafdwo2fx6

-- cray-CNL-haswell / cce@8.4.1 ---------------------------------
    elk@3.3.17  /home/hammadsa/spack/opt/spack/cray-CNL-haswell/cce-8.4.1/elk-3.3.17-7xctet2qygcliynesfxv36pukgi6jdmq

-- cray-CNL-haswell / gcc@5.1.0 ---------------------------------
    elk@3.3.17         /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/elk-3.3.17-j3piwweagppmox6jwxcmcaqxfq5iryrx
    gcc@7.1.0          /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/gcc-7.1.0-wyjyuler4ndo6kb2wqumid6jbefszkls
    libdwarf@20160507  /home/hammadsa/spack/opt/spack/cray-CNL-haswell/gcc-5.1.0/libdwarf-20160507-vxnpxxtpiipgqjltol764ugupckgoajm

-- cray-CNL-haswell / intel@15.0.2 ------------------------------
    openmpi@2.1.1  /home/hammadsa/spack/opt/spack/cray-CNL-haswell/intel-15.0.2/openmpi-2.1.1-2bjobfcpywfoacrvc2phqmzlwxucirka

-- cray-suse_linux11-sandybridge / intel@15.0.2 -----------------
    libdwarf@20160507  /home/hammadsa/spack/opt/spack/cray-suse_linux11-sandybridge/intel-15.0.2/libdwarf-20160507-diazx77homt7557d7a6razkrvlcu7ha7

A VERY SIMPLE Package creation example:

If an application is not available in Spack repo or if it’s available but we would like to tweak the build process or add variants or whatever change we would like to make to adapt it to our needs. Then we can create or edit its package file which is a simple python file. There are many build system templates to use: : python, bazel, octave, perlmake, cmake, generic, waf, makefile, autoreconf, scons, r, autotools, perlbuild

Ex. Libglade (not available in Spack repo) - Dependency for p4vasp. Very simple, no dependencies that need to be installed. 

$ spack create http://ftp.gnome.org/pub/gnome/sources/libglade/2.6/libglade-2.6.4.tar.gz

This will open a package file in the editor. Needs to be modified to include dependencies through the depend_on directive, include variants through variant directive, add configure arguments and tweak the build process, if needed. None of this was needed for libglade.

To create a Cmake build template for some package:

$ spack create -t cmake http://url 

To edit the package file:

$  spack edit libglade

Then install the package as usual.

$ spack install libglade

Errors will show if the install process needs to be tweaked. The following are very USEFUL debugging tools.

To enter the build environment Spack used to build a package:

Use $ spack env spec

hammadsa@cdl2:~> spack env libglade%intel  bash

To enter the build directory:

Use $ spack cd spec

hammadsa@cdl2:~> spack cd libglade%intel
hammadsa@cdl2:~/spack/var/spack/stage/libglade-2.6.4-5g4upczcybakgsskgubgeivuvquvw4b2/libglade-2.6.4> ./configure





Errors and fixes:
--------------------------------
Error: ==> Error: YAML ProviderIndex was not a dict.
Fix: rm -rf ~/.spack/cache





