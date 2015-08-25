# Maintainer: Eric Vidal <eric@obarun.org>
# based on the	original https://aur.archlinux.org/packages/socklog/
# 						Maintainer: Andy Weidenbaum <archbaum@gmail.com>
# 						Contributor: Roberto Alsina <ralsina@kde.org>

pkgname=socklog-obarun
pkgver=2.1.0
pkgrel=2
pkgdesc="Small and secure syslogd replacement for use with Runit"
arch=('x86_64')
depends=('glibc' 'runit' 'bash')
makedepends=('make')
conflicts=('socklog')
install=socklog_conf.install
url="http://smarden.org/socklog/"
license=('BSD')
source=(http://smarden.org/socklog/${pkgname%-obarun}-$pkgver.tar.gz
        headers.patch
        klog.sv.run
        socklog_conf.install)
sha256sums=('aa869a787ee004da4e5509b5a0031bcc17a4ab4ac650c2ce8d4e488123acb455'
            '6687d9d7c93c993b99f47948999c03b6b1402cda78d5f854d093ac881171ddf3'
            'ae9864e8dc86fb9d0de74bc988bb0f896c4811d12d52a649374903bd3257246a'
            '58f42f940547574ab059fa9bb20fc20e4046288ed1562f133dc1934f9f97cbbe')
provides=('socklog')

prepare() {
  cd "$srcdir/admin/${pkgname%-obarun}-$pkgver/src"

  msg 'Patching headers...'
  patch -p1 < "$srcdir/headers.patch"

  msg 'Setting default kernel message path to /proc/kmsg...'
  sed -i -e 's:^#define _PATH_KLOG "\/dev\/klog"$:#define _PATH_KLOG "\/proc\/kmsg":' socklog-conf.c
}

build() {
  cd "$srcdir/admin/${pkgname%-obarun}-$pkgver/src"

  msg 'Building...'
  echo "${CC:-gcc} ${CFLAGS:-O2 -Wall}" > conf-cc
  echo "${LD:-gcc -s}" > conf-ld
  make
}

check() {
  cd "$srcdir/admin/${pkgname%-obarun}-$pkgver/src"

  msg 'Testing...'
  make check
}

package() {
  cd "$srcdir/admin/${pkgname%-obarun}-$pkgver/src"
  
  msg 'Installing license...'
  install -D -m644 "$srcdir/admin/${pkgname%-obarun}-$pkgver/package/COPYING" "$pkgdir/usr/share/licenses/$pkgname/COPYING"

  msg 'Installing documentation...'
  install -d "$pkgdir/usr/share/doc/socklog"
  install -D -m644 "$srcdir/admin/${pkgname%-obarun}-$pkgver/doc"/*.html "$pkgdir/usr/share/doc/socklog"

  msg 'Installing man pages...'
  install -d "$pkgdir/usr/share/man/man8"
  install -D -m644 "$srcdir/admin/${pkgname%-obarun}-$pkgver/man"/* "$pkgdir/usr/share/man/man8"

  msg 'Installing executables...'
  install -d "$pkgdir/usr/bin"
  for i in `cat $srcdir/admin/${pkgname%-obarun}-$pkgver/package/commands`; do
    install -s -D -m755 "$srcdir/admin/${pkgname%-obarun}-$pkgver/src/$i" "$pkgdir/usr/bin"
  done

  msg 'Installing Runit service files...'
	mkdir -p "$pkgdir/etc/sv/klog"
	install -D -m755 "$srcdir/klog.sv.run" "$pkgdir/etc/sv/klog/run"
}
