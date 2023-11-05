# Arch Linux package for mDNSResponder

A small experiment to see if [Apple's Bonjour stack](https://github.com/apple-oss-distributions/mDNSResponder) can run on Linux. While the package builds, there are some caveats:

- The package still uses (legacy) SysVinit-style init scripts, which may need a compatibility layer to run on top of systemd. This could probably be fixed by writing custom systemd units instead.

> **Note:** For production, one should strongly consider using Avahi instead, which seems to be the preferred (and maintained) mDNS/DNS-SD stack on Linux.

## Credits

The `PKGBUILD` is based on the fantastic work by `keithspg` and `SJ_UnderWater` on patching the build for Linux:

- https://archlinuxarm.org/forum/viewtopic.php?f=7&t=14372
- https://github.com/aur-archive/mdnsresponder
