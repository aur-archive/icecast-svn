# Maintainer: paluh <paluho at gmail dot com>
pkgname=icecast-svn
_pkgname=icecast
pkgver=18146
pkgrel=2
pkgdesc="Streaming OGG and MP3 server, IPv6 compatible"
arch=('i686' 'x86_64')
url="http://www.icecast.org/"
license=('GPL2')
depends=('automake' 'libxslt' 'libvorbis' 'curl>=7.19.2' 'speex' 'libtheora')
makedepends=('subversion' 'autoconf' 'automake')
provides=('icecast')
conflicts=('icecast')
backup=('etc/icecast/icecast.xml')
install=icecast.install
source=(icecast.xml.chroot.patch
	rc-icecast
	icecast.logrotate)
md5sums=('83c20253cd9052987cfde792dbdbe342'
	'66415b295278a87c94ddc45ffe8f0789'
	'8fad3bf3283fa2bd651b71fdf505eae9')

_svntrunk=http://svn.xiph.org/icecast/trunk/${_pkgname}/
_svnmod=${_pkgname}

build() {
  cd "$srcdir"

  [ -d ${_svnmod}/.svn ] &&
    (cd ${_svnmod} && svn up -r $pkgver) ||
    svn co ${_svntrunk} -r $pkgver ${_svnmod}

  # temporary build directories
  [ -d $srcdir/${_svnmod}-build ] && rm -r $srcdir/${_svnmod}-build
  cp -r ${_svnmod} ${_svnmod}-build
  cd ${_svnmod}-build/

  ./autogen.sh
  make distclean
  ./configure --prefix=/usr --sysconfdir=/etc/icecast --localstatedir=/var

  # build program
  make
}

package() {
  cd "$srcdir/${_svnmod}-build"

  make DESTDIR="$pkgdir" install

  # install license
  install -D -m644 $srcdir/$_svnmod/COPYING $pkgdir/usr/share/licenses/$_pkgname/COPYING

  # patch default config to enable chroot
  cd "$pkgdir/etc/icecast"
  patch -Np0 -i "$srcdir/icecast.xml.chroot.patch"

  # rc.d script
  install -D -m755 "$srcdir/rc-icecast" "$pkgdir/etc/rc.d/icecast"

  # stuff for chroot
  mkdir -p "$pkgdir/var/icecast/"
  cp -R ${pkgdir}/usr/share/icecast/{admin,web} "$pkgdir/var/icecast/"

  # cleaning
  rm -rf "$pkgdir/usr/etc/icecast.xml"
  rm -rf "$pkgdir/usr/share"
  rm -rf ${srcdir}/*-build
}
