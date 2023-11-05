# Arch Linux package for mDNSResponder

A small experiment to see if [Apple's Bonjour stack](https://github.com/apple-oss-distributions/mDNSResponder), i.e. their implementation of [Multicast DNS (mDNS)](https://en.wikipedia.org/wiki/Multicast_DNS) and [DNS Service Discovery (DNS-SD)](https://en.wikipedia.org/wiki/Zero-configuration_networking#DNS-SD), can run on Linux.

> **Note:** For production, one should strongly consider using Avahi instead, which seems to be the preferred (and maintained) mDNS/DNS-SD stack on Linux. Avahi is also deeply integrated into many packages (often via CUPS), so this package is probably only useful in headless environments (unless one feels an irresistible urge to build Qt, GTK and a bunch of other packages manually without CUPS/Avahi/...).

## Usage

After installing the package, run

```sh
sudo systemctl enable mdnsd
sudo systemctl start mdnsd
```

to start the daemon. You can now try browsing for services on the network, e.g. with

```sh
dns-sd -B _services._dns-sd._udp
```

## Credits

The `PKGBUILD` is based on the fantastic work by `keithspg` and `SJ_UnderWater` on patching the build for Linux:

- https://archlinuxarm.org/forum/viewtopic.php?f=7&t=14372
- https://github.com/aur-archive/mdnsresponder
