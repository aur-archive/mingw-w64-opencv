pkgname=mingw-w64-opencv
pkgver=2.4.10.1
pkgrel=5
pkgdesc="Open Source Computer Vision Library (mingw-w64)"
arch=('any')
license=('BSD')
url="http://opencv.org/"
options=('!buildflags' 'staticlibs' '!strip')
depends=('mingw-w64-crt' 'mingw-w64-jasper' 'mingw-w64-libpng' 'mingw-w64-libjpeg-turbo' 'mingw-w64-libtiff' 'mingw-w64-zlib' 'mingw-w64-openexr')
makedepends=('mingw-w64-cmake')
source=("https://github.com/Itseez/opencv/archive/2.4.10.1.tar.gz"
        "mingw-opencv-2.4.patch")
md5sums=("f84af999f45edc829bfda60a13fedbaa"
         "f5e7ccd0f42063ac0f372ed31205d7e3")
_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

prepare() {
  cd "$srcdir/opencv-$pkgver"
  patch -p1 -i $srcdir/mingw-opencv-2.4.patch
}

build() {
  cd "$srcdir/opencv-$pkgver"
  for _arch in ${_architectures}; do
    mkdir -p build-${_arch} && pushd build-${_arch}
    ${_arch}-cmake \
      -DCMAKE_SKIP_RPATH=ON \
      -DENABLE_SSE=0 \
      -DENABLE_SSE2=0 \
      -DCMAKE_BUILD_TYPE=Release \
      -DBUILD_TESTS=OFF \
      -DBUILD_DOCS=OFF \
      -DWITH_FFMPEG=OFF \
      -DINSTALL_C_EXAMPLES=0 \
      -DINSTALL_PYTHON_EXAMPLES=0 \
      -DBUILD_ZLIB=OFF \
      -DBUILD_TIFF=OFF \
      -DBUILD_JASPER=OFF \
      -DBUILD_JPEG=OFF \
      -DBUILD_PNG=OFF \
      -DBUILD_OPENEXR=OFF \
      ..
    make
    popd
    mkdir -p build-${_arch}-static && pushd build-${_arch}-static
    ${_arch}-cmake \
      -DCMAKE_SKIP_RPATH=ON \
      -DENABLE_SSE=0 \
      -DENABLE_SSE2=0 \
      -DCMAKE_BUILD_TYPE=Release \
      -DBUILD_TESTS=OFF \
      -DBUILD_DOCS=OFF \
      -DWITH_FFMPEG=OFF \
      -DINSTALL_C_EXAMPLES=0 \
      -DINSTALL_PYTHON_EXAMPLES=0 \
      -DBUILD_ZLIB=OFF \
      -DBUILD_TIFF=OFF \
      -DBUILD_JASPER=OFF \
      -DBUILD_JPEG=OFF \
      -DBUILD_PNG=OFF \
      -DBUILD_OPENEXR=OFF \
      -DBUILD_SHARED_LIBS=OFF \
      ..
    make
    popd
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "$srcdir/opencv-$pkgver/build-${_arch}-static"
    make DESTDIR="$pkgdir" install
    cd "$srcdir/opencv-$pkgver/build-${_arch}"
    make DESTDIR="$pkgdir" install
    install -d "$pkgdir"/usr/${_arch}/share/opencv
    install -m644 "$pkgdir"/usr/${_arch}/*.cmake "$pkgdir"/usr/${_arch}/share/opencv
    install -m644 "$pkgdir"/usr/${_arch}/LICENSE "$pkgdir"/usr/${_arch}/share/opencv
    install -d "$pkgdir"/usr/${_arch}/lib/pkgconfig
    install -m644 "$srcdir"/opencv-$pkgver/build-${_arch}/unix-install/opencv.pc \
      "$pkgdir"/usr/${_arch}/lib/pkgconfig/
    rm "$pkgdir"/usr/${_arch}/LICENSE
    rm "$pkgdir"/usr/${_arch}/*.cmake
    ${_arch}-strip --strip-unneeded "$pkgdir"/usr/${_arch}/bin/*.dll
    ${_arch}-strip -g "$pkgdir"/usr/${_arch}/lib/*.a
  done
}

