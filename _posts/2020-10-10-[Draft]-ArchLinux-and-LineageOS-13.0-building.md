---
layout: post
title: [Draft] ArchLinux and LineageOS 13.0 building
---

While Covid19, Distance learning and stuff are happening, I have decided to take a jab at the android building thing again. 

This time hoping for success as I built more experience over the years. So far it's not going so well.

because previously I used xubuntu16.04 all the build tools were relativly legacy, and there were a lot of guides i can quicky google and would work.

... If It were that simple. I wouldn't be writing this article. let's get started.

## Getting the Source and creating an environment for build.

This is [pretty well documented in the arch wiki.](https://wiki.archlinux.org/index.php/Android#Required_packages) enable mulilib and follow the following:

```
As of 2020/April, to build either AOSP 10 or LineageOS 17.1 you need (possibly a subset of):

    base-devel multilib-devel gcc repo git gnupg gperf sdl wxgtk2 squashfs-tools curl ncurses zlib schedtool perl-switch zip unzip libxslt bc rsync ccache lib32-zlib lib32-ncurses lib32-readline ncurses5-compat-libs lib32-ncurses5-compat-libs

and a TTF font installed, e.g. ttf-dejavu. In particular, no Python2 or Java are required, as they are provided by AOSP/Lineage. What follows below is only relevant to obsolete Android versions.

To build any version of Android, you need to install these packages:

    lib32-gcc-libs git gnupg flex bison gperf sdl wxgtk2 squashfs-tools curl ncurses zlib schedtool perl-switch zip unzip libxslt python2-virtualenv bc rsync ncurses5-compat-libsAUR lib32-zlib lib32-ncurses lib32-readline lib32-ncurses5-compat-libs

The aosp-devel metapackage provides them all for simple installation.

Additionally, LineageOS requires the following packages: xml2, lzop, pngcrush, imagemagick

They can be installed with the lineageos-devel metapackage.
```

**Recommended!:** 
alternatively, you can just use [this script by akhilnarang](https://github.com/akhilnarang/scripts/blob/master/setup/arch-manjaro.sh)





