# Arch Linux package for mDNSResponder

A small experiment to see if Apple's Bonjour stack can run on Linux. While the package builds, there are some caveats:

- The package still uses (legacy) SysVinit-style init scripts, which may need a compatibility layer to run on top of systemd. This could probably be fixed by writing custom systemd units instead.
- The package uses an outdated version of the mDNSResponder (1790.40.31), since version 1807.101.2 [removes many of the cross-platform facilities](https://github.com/apple-oss-distributions/mDNSResponder/commit/d31f3447dfb1997e6457abf83502390b1f42f2ce).

> **Note:** For production, one should strongly consider using Avahi instead, which seems to be the preferred (and maintained) mDNS/DNS-SD stack on Linux.
