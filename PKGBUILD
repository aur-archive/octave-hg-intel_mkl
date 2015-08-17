# Maintainer: Michael Levine <michael.levine at utoronto dot ca>

##################################################################################
# this PKGBUILD is a modification of the original octave-hg package, which uses
# Intel compiler and links against Intel MKL for a *significant* performance boost
#
# Intel Parallel Studio XE is freely available from Intel for non-commercial use.
# Refer to this package for details:
# https://aur.archlinux.org/packages/intel-parallel-studio-xe/
#
# 
# Machine-specific settings and customizations:
# Refer to icc/ifort documentation for details
 _mtune=core2
 _march=core2
 _xX=SSSE3
 _axX=SSSE3

# Inter-procedural Optimizations.  To enable, set the value of this variable to "-ipo"
 _ipo=""
# _ipo="-ipo"

# Setting for CPU architecture (i686 or x64).  This will change some of the library paths

 if [[ -n `uname -a | grep x86_64` ]]; then
    _cpuArch="x86_64"
 else
    _cpuArch="i686"
 fi

 # Customization options
 # to disable any options, change the variable to '--disable-XXX'

 _build_gui='--enable-gui'
 _build_docs='--enable-docs'
 _enable_java='--disable-java'
 _build_jit='--disable-jit'

 # Number of simultaneous Make jobs.  A higher number will speed up (parts) of the build
 # process at the expense of RAM.  Reduce this number if the build is failing due to 
 # out-of-memory errors
 _n_make_jobs=2

##################################################################################

pkgname=octave-hg-intel_mkl
pkgver=16638.3c2e457eeb72
pkgrel=3
pkgdesc="A high-level language, primarily intended for numerical computations. Modification of octave-hg package to utilize Intel MKL and compilers"
url="http://www.octave.org"
arch=('i686' 'x86_64')
license=('GPL')

# Some of these may be optional, e.g. arpack, lapack, qhull, but if they
# are installed, octave will be linked against them.
depends=('fftw>=3.2.2' 'curl' 'fltk' 'hdf5' 'glpk' 'arpack' \
  'qrupdate' 'qscintilla' 'qhull' 'graphicsmagick' 'intel-mkl' 'intel-openmp')

makedepends=('pcre' 'mercurial' 'texinfo' 'gcc-fortran' 'gperf' \
  'perl' 'rsync' 'intel-compiler-base' 'intel-openmp' 'intel-fortran-compiler' )
# For building the documentation, epstool and fig2dev are searched during
# build. The build works without them, but you may want to install
# transfig from [extra] and epstool from AUR
optdepends=('texinfo: for help-support in octave'
  'gnuplot: alternative plotting')
conflicts=('octave' 'octave3' 'octave-suitsparse')
provides=('octave=3.7.2+')
install=octave.install
options=('!emptydirs')
# makepkg doesn't handle subrepos well, so leave 'source' empty and
# clone from the repositories manually.
#source=(hg+http://hg.savannah.gnu.org/hgweb/octave)
md5sums=('SKIP')

_hgroot=http://www.octave.org/hg/
_hgrepo=octave


build_instructions() {

    echo -e ""
    echo -e ""
    echo -e "====================================================================================="
    echo -e " This PKGBUILD has specific customization options that should be confirmed/modified "
    echo -e " before building."
    echo -e ""
    echo -e " These options fall into 2 categories: "
    echo -e " * Configuration options"
    echo -e " * Compilation options"
    echo -e ""
    echo -e " It is critical that the Compilation options be verified prior to building the package."
    echo -e " The compiler flags being used for this package include heavy optimizations that are "
    echo -e " CPU-specific.  Incorrect selection of CPU-type will affect the performance and/or the"
    echo -e " ability of the software to execute on your hardware."
    echo -e " "
    echo -e " *  *  *  *"
    echo -e " Note re: Optimizations:"
    echo -e " Some users have had issues with inter-procedural optimizations (-ipo flag).  There is now"
    echo -e " a configuration option that allows the choice whether or not to enable -ipo.  This defaults"
    echo -e " to off, and can be enabled by setting the configuration options accordingly."
    echo -e " *  *  *  *"
    echo -e " "
    echo -e " "
    echo -e " Please ensure that Intel Parallel Studio has been installed and that environment variables "
    echo -e " are set correctly. This is accomplished by executing the appropriate scripts located in "
    echo -e " /opt/intel/composerxe/bin/ and /opt/intel/mkl/bin/ "
    echo -e " Refer to Parallel Studio package information and product documentation for more information"
    echo -e " "
    echo -e " The configuration options include whether or not to build the GUI interface, etc."
    echo -e " These can be selected according to your preferences."
    echo -e " "
    echo -e " "
    echo -e " Please note that this PKGBUILD overrides the compilation options that might be set in "
    echo -e " /etc/makepkg.conf "
    echo -e " This may not be ideal and may be fixed in the future"
        
    echo -e "====================================================================================="
    echo -e " "
    echo -e " "
}


pkgver() {
  if [ -d $SRCDEST/${_hgrepo}/.hg ]; then
    cd $SRCDEST/${_hgrepo}
    echo $(hg identify -n).$(hg identify -i)
  else
  echo $pkgver.$(hg identify -i http://hg.savannah.gnu.org/hgweb/octave)
  fi
}

build() {
    
    build_instructions

  cd $SRCDEST
  
  if [ -d $SRCDEST/${_hgrepo} ]; then
    cd $SRCDEST/${_hgrepo}
    hg pull -u
  else
    hg clone ${_hgroot}${_hgrepo} 
  fi
  
  msg "Mercurial clone done or server timeout"
  msg "Starting make..."
  
  [ -d $SRCDEST/$_hgrepo-build ] && rm -rf $SRCDEST/$_hgrepo-build
  cp -rf $SRCDEST/$_hgrepo $SRCDEST/$_hgrepo-build
  
  cd $SRCDEST/$_hgrepo-build
  
  ./bootstrap 
  LANG=C

  # Set compiler flags and environment variables

  if [ ${_cpuArch} == "x86_64"]; then
    LDFLAGS='-L/opt/intel/mkl/lib -L/opt/intel/mkl/lib/intel64 -L/opt/intel/lib'
    _MKL="-lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -liomp5"
  else
      LDFLAGS='-L/opt/intel/mkl/lib -L/opt/intel/mkl/lib/ia32 -L/opt/intel/lib'
    _MKL="-lmkl_intel -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -liomp5"
  fi

  CFLAGS="$CFLAGS -DMKL_LP64 -DM_PI=3.1415926535897932384 -fpic -parallel -openmp -ip ${_ipo} -mtune=${_mtune} -march=${_march} -unroll-aggressive -use-intel-optimized-headers -vec-threshold50 -x${_xX} -ax${_axX}"
  CXXFLAGS=$CFLAGS
  FFLAGS="-fpic -O3 -x${_xX} -march=${_march} -mtune=${_mtune} -openmp -parallel -ax${_axX} -ip ${_ipo} -unroll-aggressive -vec-threshold50"
  AR=xiar
  LD=xild
  F77=ifort
  FC=ifort
  CC=icc
  CPP='icc -E'
  CXX=icpc
  CXXPP='icpc -E'
  MAKEFLAGS='-j ${_n_make_jobs}'

  FC=ifort F77=ifort ./configure --prefix=/usr --libexecdir=/usr/lib --enable-shared \
    --with-umfpack="-lumfpack -lsuitesparseconfig -lrt" ${_build_jit}  \
    --enable-qhull ${_enable_java} ${_build_docs} MOC=moc-qt4 UIC=uic-qt4 \
    --with-blas=${_MKL} \
    --with-lapack=${_MKL} \
    --with-fftw3=${_MKL} \
    --with-fftw3f=${_MKL} \
    ${_build_gui}

  # There is a problem when trying to compile stdio.c in libgnu with ICC throwing an error.  Comment out the offending lines to resolve.
  # Note that stdio.c is generated by Makefile.  In order to make this work, it is necessary to explicitly build libgnu first, then run sed
  # to modify the file to suit.
  cd $SRCDEST/$_hgrepo-build/libgnu
  make
  sed -i -e '1203 {s@^_@\/\/_@}' -e '929 {s@^_@\/\/_@}' -e '1557 {s@^_@\/\/_@}' stdio.h

  cd $SRCDEST/$_hgrepo-build
 
  make 
}

package() {
  cd $SRCDEST/${_hgrepo}-build
  make DESTDIR=${pkgdir} install
  # add octave library path to ld.so.conf.d
  install -d "${pkgdir}/etc/ld.so.conf.d"
  export _appver=$(${pkgdir}/usr/bin/octave --version | head -1 | sed "s/.*version \([^ ]*\).*$/\1/")
  echo "/usr/lib/${_hgrepo}/${_appver}" > "${pkgdir}/etc/ld.so.conf.d/${_hgrepo}.conf"
}
