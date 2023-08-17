# Based on https://archlinuxarm.org/forum/viewtopic.php?f=7&t=14372

pkgname=('mdnsresponder' 'nss-mdnsresponder')
pkgver=1790.40.31
pkgrel=1
pkgdesc="Apple's official implementation of mDNS/DNS-SD/Bonjour/Zeroconf"
arch=('i686' 'x86_64' 'armv6h' 'armv7h')
url='https://developer.apple.com/opensource/'
license=('CUSTOM')
depends=('glibc')
options=('!libtool')
provides=('avahi')
conflicts=('avahi')
source=(https://github.com/apple-oss-distributions/mDNSResponder/archive/refs/tags/mDNSResponder-$pkgver.tar.gz)

sha256sums=(
  'SKIP' # TODO
)

prepare() {
  msg2 'Patching for Linux'
  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver"

  # TODO: Patch for Linux
  # patch --forward --strip=1 --input="${srcdir}/linux_1.patch"
}

build() {
  if [ "$(echo $srcdir|grep -E '[ "]')" ];then
  error 'Spaces found in path, this will break the Makefile';exit 1;fi
  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver/mDNSPosix"
  if [ ! -f fixed ];then fix;fi
  msg2 'Making mDNSResponder...'
  make os=linux
}

fix() {
  msg2 'Fixing Makefile...'
  sed -i 's/CP = cp/CP = install -D/' Makefile
  sed -i 's/LN = ln -s -f/LN = ln -rs/' Makefile
  sed -i 's/cp \$/$(CP) $/' Makefile
  sed -i 's;NSSINSTPATH := ;NSSINSTPATH := $(DESTDIR)/usr;' Makefile
  sed -i 's/INSTBASE?=/INSTBASE?=$(DESTDIR)/' Makefile
  sed -i 's/STARTUPSCRIPTNAME?=mdns/STARTUPSCRIPTNAME?=mdnsd/' Makefile
  sed -i 's/MANPATH := /MANPATH := $(DESTDIR)/' Makefile
  sed -i 's:STARTUPSCRIPTDIR = $(INSTBASE)/etc/rc.d:STARTUPSCRIPTDIR = $(DESTDIR)/etc/rc.d:' Makefile
  sed -i 's:/etc/nss_mdns.conf:$(DESTDIR)/etc/nss_mdns.conf:' Makefile
  sed -i 's/cp -f/#cp -f/' Makefile
  sed -i 's/sed -e/#sed -e/' Makefile
  sed -i 's/\tldconfig/\t#ldconfig/' Makefile
  sed -i 's/\$@ start/#$@ start/' Makefile
  echo -e '\n$(STARTUPSCRIPTDIR): $(DESTDIR)\n\tmkdir -p $<' >>Makefile
  :>fixed
}

package_mdnsresponder() {
  backup=('etc/dnsextd.conf')

  cd "$srcdir/mDNSResponder-mDNSResponder-$pkgver/mDNSPosix"
  msg2 'Building...'
  make os=linux DESTDIR="$pkgdir" OPTINSTALL= install
  install -m755 "$srcdir"/dnsextd.rc "$pkgdir"/etc/rc.d/dnsextd
  install -m755 "$srcdir"/mdnsd.rc "$pkgdir"/etc/rc.d/mdnsd
  install -d "$pkgdir"/usr/share/man/man1
  install -D -m444 ../LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
  install -m444 ../mDNSShared/dns-sd.1 "$pkgdir"/usr/share/man/man1/
  install -m444 ../mDNSShared/{dnsextd,mDNSResponder}.8 "$pkgdir"/usr/share/man/man8
  install -m444 ../mDNSShared/dnsextd.conf "$pkgdir"/etc
  cp build/prod/mDNSNetMonitor "$pkgdir"/usr/bin
  cp build/prod/mDNS{Client,Responder,ProxyResponder}Posix "$pkgdir"/usr/bin
}

package_nss-mdnsresponder() {
  provides=('nss-mdns')
  conflicts=(${provides[@]})
  backup=('etc/nss_mdns.conf')
  pkgdesc="Apple's official implementation of mdns host name resolution"

  cd mDNSResponder-$pkgver/mDNSPosix
  msg2 'Building...'
  make os=linux DESTDIR="$pkgdir" InstalledNSS
  install -D -m444 ../LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
