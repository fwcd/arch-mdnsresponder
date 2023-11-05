# Based on https://archlinuxarm.org/forum/viewtopic.php?f=7&t=14372

pkgname=('mdnsresponder' 'nss-mdnsresponder')
pkgver=2200.0.8
pkgrel=7
pkgdesc="Apple's official implementation of mDNS/DNS-SD/Bonjour/Zeroconf"
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
url='https://github.com/apple-oss-distributions/mDNSResponder'
license=('CUSTOM')
depends=('glibc' 'mbedtls')
options=('!libtool')
conflicts=('avahi')
source=(
  '0001-Remove-legacy-mbedtls-includes.patch'
  '0002-Include-limit.h-in-PlatformCommon.patch'
  'mdnsd.service'
  "https://github.com/apple-oss-distributions/mDNSResponder/archive/refs/tags/mDNSResponder-$pkgver.tar.gz"
)

sha256sums=(
  'de4b39d505241fe2162997fab30ef77b357360e246ec978f5a93d46478e141f9'
  '25560d37d41cb2caa61de5d1e8c96876f73b82a1030f957e9a26c1aa407d3202'
  'd19f98fbebbd0c77859c02592cc6d8c218b741f0c5b2a20c88bbd8ea623882a2'
  '68b6128481cb607678f81bce9f3868c5ad1f6d93b9ac07fa752a6e3d68e11f24'
)

prepare() {
  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver"

  for patch in "$srcdir"/*.patch; do
    msg2 "Applying $(basename "$patch")..."
    patch --forward --strip=1 --input="$patch"
  done

  cd "mDNSPosix"

  msg2 'Fixing Makefile...'
  sed -i 's/CP = cp/CP = install -D/' Makefile
  sed -i 's/LN = ln -s -f/LN = ln -rs/' Makefile
  sed -i 's/cp \$/$(CP) $/' Makefile
  sed -i 's;NSSINSTPATH := ;NSSINSTPATH := $(DESTDIR)/usr;' Makefile
  sed -i 's/INSTBASE?=/INSTBASE?=$(DESTDIR)/' Makefile
  sed -i 's/STARTUPSCRIPTNAME?=mdns/STARTUPSCRIPTNAME?=mdnsd/' Makefile
  sed -i 's/MANPATH := /MANPATH := $(DESTDIR)/' Makefile
  sed -i 's:STARTUPSCRIPTDIR = $(INSTBASE)/etc.*:STARTUPSCRIPTDIR = $(DESTDIR)/etc/init.d:' Makefile
  sed -i 's:$(wildcard /etc.*,:,:' Makefile
  sed -i 's:/etc/nss_mdns.conf:$(DESTDIR)/etc/nss_mdns.conf:' Makefile
  sed -i 's/cp -f/#cp -f/' Makefile
  sed -i 's/sed -e/#sed -e/' Makefile
  sed -i 's/\tldconfig/\t#ldconfig/' Makefile
  sed -i 's/\t\(.*\) start/#\t\1 start/' Makefile
  sed -i 's:/sbin/:/bin/:' Makefile
  echo -e '\n$(STARTUPSCRIPTDIR): $(DESTDIR)\n\tmkdir -p $<' >>Makefile
}

build() {
  if [ "$(echo $srcdir|grep -E '[ "]')" ]; then
    error 'Spaces found in path, this will break the Makefile'
    exit 1
  fi

  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver/mDNSPosix"

  msg2 'Making mDNSResponder...'
  make os=linux
}

package_mdnsresponder() {
  backup=('etc/dnsextd.conf')

  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver/mDNSPosix"

  msg2 'Installing...'
  make os=linux DESTDIR="$pkgdir" OPTINSTALL= install
  install -d "$pkgdir"/usr/share/man/man1
  install -D -m444 ../LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
  install -m444 ../mDNSShared/dns-sd.1 "$pkgdir"/usr/share/man/man1/
  install -m444 ../mDNSShared/{dnsextd,mDNSResponder}.8 "$pkgdir"/usr/share/man/man8
  install -m444 ../mDNSShared/dnsextd.conf "$pkgdir"/etc
  cp build/prod/mDNSNetMonitor "$pkgdir"/usr/bin
  cp build/prod/mDNS{Client,Responder,ProxyResponder}Posix "$pkgdir"/usr/bin

  msg2 'Replacing legacy SysVinit scripts with systemd unit...'
  rm -rf "$pkgdir"/etc/init.d
  install -Dm444 "$srcdir/mdnsd.service" "$pkgdir/usr/lib/systemd/system/mdnsd.service"
}

package_nss-mdnsresponder() {
  provides=('nss-mdns')
  conflicts=(${provides[@]})
  backup=('etc/nss_mdns.conf')
  pkgdesc="Apple's official implementation of mdns host name resolution"

  cd "mDNSResponder-mDNSResponder-$pkgver/mDNSPosix"

  msg2 'Installing NSS...'
  make os=linux DESTDIR="$pkgdir" InstalledNSS
  install -D -m444 ../LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
