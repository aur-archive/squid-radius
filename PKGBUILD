# Maintainer: Jiehan Zheng <zheng@jiehan.org>
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Mark Coolen <mark.coolen@gmail.com>
# Contributor: Tom Newsom <Jeepster@gmx.co.uk>
# Contributor: Kevin Piche <kevin@archlinux.org>

pkgname=squid-radius
pkgver=3.3.5
pkgrel=2
pkgdesc='Full-featured Web proxy cache server with RADIUS authentication support compiled'
arch=('x86_64' 'i686')
url='http://www.squid-cache.org'
depends=('openssl' 'pam' 'cron' 'perl' 'libltdl')
makedepends=('libcap')
provides=('squid')
license=('GPL')
options=('emptydirs')
backup=('etc/squid/squid.conf'
        'etc/squid/mime.conf'
        'etc/conf.d/squid')
install=$pkgname.install
source=("http://www.squid-cache.org/Versions/v3/3.3/squid-$pkgver.tar.bz2"
        'squid.pam'
        'squid.cron'
        'squid.service')
md5sums=('c7991aa8528034af0e21a69edf26e41b'
         '270977cdd9b47ef44c0c427ab9034777'
         'a71425c4951f2e5b640d19e6a5048531'
         'ceeb57c69ebb165676219222f109a24e')
build() {
  cd "$srcdir/squid-$pkgver"

  # gcc 4.6 doesn't support -fhuge-objects.
  sed '/^    HUGE_OBJECT_FLAG=/ s/"-fhuge-objects"//' -i configure

  # fix cache_dir, cache_dir size, and effective group.
  sed '/^DEFAULT_SWAP_DIR/ s@/cache@/cache/squid@' -i src/Makefile.in
  sed '/^#cache_dir/ s/100/256/
       /^NAME: cache_effective_group/ {n;n;s/none/proxy/}' -i src/cf.data.pre

  sed -i '1,1i#include <errno.h>' helpers/external_acl/file_userip/ext_file_userip_acl.cc

  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --datadir=/usr/share/squid \
    --sysconfdir=/etc/squid \
    --libexecdir=/usr/lib/squid \
    --localstatedir=/var \
    --with-logdir=/var/log/squid \
    --with-pidfile=/run/squid.pid \
    --enable-auth \
    --enable-auth-basic \
    --enable-auth-ntlm \
    --enable-auth-digest \
    --enable-auth-negotiate \
    --enable-removal-policies="lru,heap" \
    --enable-storeio="aufs,ufs,diskd" \
    --enable-delay-pools \
    --enable-arp-acl \
    --enable-ssl \
    --enable-snmp \
    --enable-linux-netfilter \
    --enable-ident-lookups \
    --enable-useragent-log \
    --enable-cache-digests \
    --enable-referer-log \
    --enable-arp-acl \
    --enable-htcp \
    --enable-carp \
    --enable-epoll \
    --with-large-files \
    --enable-arp-acl \
    --with-default-user=proxy \
    --enable-async-io \
    --enable-truncate \
    --enable-icap-client \
    --enable-basic-auth-helpers="squid_radius_auth"
  make
}

package() {
  cd "$srcdir"

  make -C "squid-$pkgver" DESTDIR="$pkgdir" install
  install -Dm755 "$srcdir/squid.cron" "$pkgdir/etc/cron.weekly/squid"
  install -Dm644 "$srcdir/squid.pam" "$pkgdir/etc/pam.d/squid"
  install -Dm644 "$srcdir/squid.service" \
    "$pkgdir/usr/lib/systemd/system/squid.service"
  rm -rf "$pkgdir/run" "$pkgdir/var/run"
}

# vim: ts=2 sw=2 et ft=sh
