# Maintainer: Nissar Chababy <funilrys at outlook dot com>
# Ex-Maintainer: K0n24d <konrad AT knauber DOT net>
pkgname=urbackup2-server
pkgver=2.5.33
pkgrel=2
pkgdesc="Client Server backup system"
arch=('i686' 'x86_64' 'armv5' 'armv6h' 'armv6' 'armv7h' 'armv7' 'aarch64')
url="http://www.urbackup.org/"
license=('GPL')
makedepends=('gcc-libs' 'gcc' 'make')
depends=('sqlite' 'crypto++' 'curl' 'fuse' 'zlib')
conflicts=('urbackup-server')
backup=("etc/default/urbackupsrv")
source=(
    "https://hndl.urbackup.org/Server/${pkgver}/urbackup-server-${pkgver}.tar.gz"
    "md5-bytes.patch"
)
install='urbackup.install'
sha512sums=('7a6c1fa7790e03fcfc7e91efaf0ed3819a014cd51c0b5941d7d4230144e11d7263e6ceb657d2d5cf6c293d8e069b6870bdee3ef3849a5e00326a48ed9f21fcc6'
            '34e25c868cf4572414fbc6c693877127152f9a97edf8865b4263a55cf16f71a5045ba96b1a9af8244ed49c35cab56e3fdb44348d191e9f85e2efb66392907132')

CFLAGS="-march=native -O2 -pipe -fstack-protector-strong"
CXXFLAGS="${CFLAGS}"
CPPFLAGS="${CPPFLAGS} -DNDEBUG"
MAKEFLAGS="-j$(nproc)"

build() {
    sed  -i '/\#include \"cryptopp_inc.h\"/a #include "assert.h"' "${srcdir}/urbackup-server-${pkgver}/cryptoplugin/AESGCMDecryption.h"

    patch -d"${srcdir}/urbackup-server-${pkgver}" -p0 < "${srcdir}/md5-bytes.patch"

    cd "${srcdir}/urbackup-server-${pkgver}"
    ./configure --prefix=/usr --sbindir=/usr/bin --sysconfdir=/etc \
    --localstatedir=/var --enable-packaging \
    --with-mountvhd --with-zlib --with-libcurl \
    --enable-embedded-cryptopp

    make
}

package() {
    cd "${srcdir}/urbackup-server-${pkgver}"

    make DESTDIR=$pkgdir install

    # Correct some file mode bits, thanks cfstras
    chmod a+x "${pkgdir}/usr/share/urbackup/www/"{css,fonts,js,images,}

    install -Dm644 "${srcdir}/urbackup-server-${pkgver}"/urbackup-server.service \
    "${pkgdir}"/usr/lib/systemd/system/urbackup-server.service
    install -Dm644 "${srcdir}/urbackup-server-${pkgver}"/urbackup-server-firewalld.xml \
    "${pkgdir}"/usr/lib/firewalld/services/urbackup-server-firewalld.xml
    install -Dm644 "${srcdir}/urbackup-server-${pkgver}"/logrotate_urbackupsrv \
    "${pkgdir}"/etc/logrotate.d/urbackupsrv
    install -Dm644 "${srcdir}/urbackup-server-${pkgver}"/docs/urbackupsrv.1 \
    "${pkgdir}"/usr/share/man/man1/urbackupsrv.1
    install -Dm644 "${srcdir}/urbackup-server-${pkgver}"/defaults_server \
    "${pkgdir}"/etc/default/urbackupsrv
}
# vim: ts=2
