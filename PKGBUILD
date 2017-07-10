# Maintainer: Alexey Kasatkin <alexeikasatkin@gmail.com>
# ArchLinux maintainer: Jaroslav Lichtblau <dragonlord@aur.archlinux.org>
# Contributor: dibblethewrecker dibblethewrecker.at.jiwe.dot.org
# Contributor: William Rea <sillywilly@gmail.com>
# Contributor: Petri Moisio <petri.k.moisio@gmail.com>

_realname=gdal
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}"
pkgver=2.2.0
pkgrel=2
pkgdesc="A translator library for raster geospatial data formats (mingw-w64)"
arch=('any')
url="http://www.gdal.org/"
license=('custom')
makedepends=("${MINGW_PACKAGE_PREFIX}-gcc"
             "${MINGW_PACKAGE_PREFIX}-pkg-config"
             "${MINGW_PACKAGE_PREFIX}-postgresql"
             "${MINGW_PACKAGE_PREFIX}-qhull")
depends=("${MINGW_PACKAGE_PREFIX}-cfitsio"
         "${MINGW_PACKAGE_PREFIX}-curl"
         "${MINGW_PACKAGE_PREFIX}-expat"
         "${MINGW_PACKAGE_PREFIX}-geos"
         "${MINGW_PACKAGE_PREFIX}-giflib"
         "${MINGW_PACKAGE_PREFIX}-hdf5"
         "${MINGW_PACKAGE_PREFIX}-jasper"
         "${MINGW_PACKAGE_PREFIX}-json-c"
         "${MINGW_PACKAGE_PREFIX}-libfreexl"
         "${MINGW_PACKAGE_PREFIX}-libgeotiff"
         "${MINGW_PACKAGE_PREFIX}-libiconv"
         "${MINGW_PACKAGE_PREFIX}-libjpeg"
         "${MINGW_PACKAGE_PREFIX}-libkml"
         "${MINGW_PACKAGE_PREFIX}-libpng"
         "${MINGW_PACKAGE_PREFIX}-libspatialite"
         "${MINGW_PACKAGE_PREFIX}-libtiff"
         "${MINGW_PACKAGE_PREFIX}-libwebp"
         "${MINGW_PACKAGE_PREFIX}-libxml2"
         "${MINGW_PACKAGE_PREFIX}-openjpeg2"
         "${MINGW_PACKAGE_PREFIX}-pcre"
         "${MINGW_PACKAGE_PREFIX}-poppler"
         "${MINGW_PACKAGE_PREFIX}-proj"
         "${MINGW_PACKAGE_PREFIX}-sqlite3"
         "${MINGW_PACKAGE_PREFIX}-xerces-c"
         "${MINGW_PACKAGE_PREFIX}-xz")
optdepends=("${MINGW_PACKAGE_PREFIX}-postgresql")
options=('strip' 'staticlibs')

source=(http://download.osgeo.org/${_realname}/${pkgver}/${_realname}-${pkgver}.tar.gz
        0001-external-qhull-static.patch
        0002-libproj4-dll-name.patch)
sha256sums=('d06546a6e34b77566512a2559e9117402320dd9487de9aa95cb8a377815dc360'
            '6952586cd4436003748e8d99f2e8e8a660ba59bdb3c0b294493695ef57f93077'
            '1b8e2ccad84537c6ee569b254b766a25cba11fae85b891d712a91eea3c7a0f46')

prepare() {
  [[ -d ${srcdir}/build-${MINGW_CHOST} ]] && rm -rf ${srcdir}/build-${MINGW_CHOST}
  cp -rf ${_realname}-${pkgver} build-${MINGW_CHOST}
  cd "${srcdir}/build-${MINGW_CHOST}"
  
  sed -i "s|/usr/local|${MINGW_PREFIX}|g" configure.ac
  sed -i "s|/usr|${MINGW_PREFIX}|g" configure.ac
  sed -i "s|mandir='\${prefix}/man'|mandir='\${prefix}/share/man'|g" configure.ac
  for p in m4/*.m4
  do
    sed -i "s|/usr|${MINGW_PREFIX}|g" $p
  done
  
  patch -p1 -i ${srcdir}/0001-external-qhull-static.patch
  patch -p1 -i ${srcdir}/0002-libproj4-dll-name.patch
  
  ./autogen.sh
}

build() {
  cd "${srcdir}/build-${MINGW_CHOST}"

  CFLAGS+=" -fno-strict-aliasing"

  ./configure \
    --build=${MINGW_CHOST} \
    --host=${MINGW_CHOST} \
    --target=${MINGW_CHOST} \
    --prefix=${MINGW_PREFIX} \
    --enable-static \
    --disable-shared \
    --disable-debug \
    --without-poppler \
    --without-xerces \
    --with-webp \
    --with-spatialite=/mingw32 \
    --with-liblzma=yes \
    --with-expat-inc=/mingw32/include \
    --with-expat-lib="-L/mingw32/lib -lexpat" \
    --without-python \
    --without-perl \
    --without-threads \
    --without-jasper \
    --with-pg=/mingw32/bin/pg_config \
    --without-libkml \
    --with-png=/mingw32 \
    --with-jpeg=/mingw32 \
    --with-odbc=/c/Rtools/mingw_32/i686-w64-mingw32 \
    --with-freexl=/mingw32 \
    --with-static-proj4=yes

  sed -i GDALmake.opt -e "s|EXE_DEP_LIBS.*|EXE_DEP_LIBS = \$\(GDAL_SLIB\)|g"
  sed -i GNUmakefile -e "s|\$(GDAL_ROOT)\/||g"

  read -n1 -r -p "Press any key to continue..." key

  make
#  make man
}

package () {
  cd "${srcdir}/build-${MINGW_CHOST}"
  make DESTDIR="${pkgdir}" install
  make DESTDIR="${pkgdir}" install-man

  # install license
  install -D -m644 LICENSE.TXT "${pkgdir}${MINGW_PREFIX}/share/licenses/${_realname}/LICENSE"
}
