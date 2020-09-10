---
layout: post
title: (Draft) My ArchLinux and LineageOS 13.0 building Experience
---

While Covid19, Distance learning and stuff are happening, I have decided to take a jab at the android building thing again. 

This time I thought I had a better chance of success because of the experience I built over the years. So far it's not going so well.

Previously, the source code was functional in xubuntu 16.04. with no modifications everything just compiled out of the box with some dependencies.

... If It were that simple. I wouldn't be writing this article. here's my experience so far and what I have done so far. let's get started.

## Creating an environment for build.

**Recommended!:** you can just use [this script by akhilnarang](https://github.com/akhilnarang/scripts/blob/master/setup/arch-manjaro.sh)

This is [pretty well documented in the arch wiki.](https://wiki.archlinux.org/index.php/Android#Required_packages) enable mulilib and follow the "build any version of Android" instructions.

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

### What version of java was latest again?

**make sure you define JAVA_HOME environment variable! build will complain!**

now in a perfect world- or you're building latest, you're done. 

But we're not doing that- and the world is nowhere near perfect, it's just too flawed.

Install either ``java-8-openjdk`` or ``java-7-openjdk`` and make sure to complain that you're using java7 or java8 in 2020s. I know. It's unbelievable.

Familarize yourself with [archlinux-java](https://wiki.archlinux.org/index.php/Java) too.

### Because Everything _MUST_ be python2.... right?

Every build script seems to be assuming that the command ``python`` would return python2, arch doesn't do that.

Either create a directory you can use as a PATH, then create a symlink of python2 named "python" to that directory, and then making sure it runs python2 all the time:

``ln -s /usr/bin/python2 ~/android-path/python``

Or you can do yourself a favor and run these two commands below before building; and never have to worry about it.

```
virtualenv2 venv
source venv/bin/activate
```

And now move onto: 

## Downloading the Source code

Downloading the source code is... well. just downloading. 

Go make a working directory,

``repo init -u git://github.com/LineageOS/android.git -b lineage-13.0`` and ``repo sync -j16``

Then go watch youtube... or something.

For this and next part you're about to spend countless hours fixing and debugging; 

So grab a nice long playlist of song(s) or a megamix.

Here are some song recommendations that I can think of right now.

[Taishi - Reverie for Another Sphere](https://youtu.be/5M-CfgAG2n4)

[Endorfin. - Spica](https://youtu.be/34Q1fGg70hA)

[Shibayan Records - Broad Border](https://youtu.be/w6He_2X-06c)

[Monstercat - The Best of 2013 Album Mix [Part 1]](https://youtu.be/w6He_2X-06c)

[S3RL - S3RL Always Presents...](https://youtu.be/XwLakmNQzHY)

[Novy(Amplified Sound)'s Touhou Progressive House & Trance Mixes,](https://www.youtube.com/playlist?list=PLqGzPZvdSk1OKWSj2d6nTPSJSCjQrUn6j)

I would like to dump every every song I love here; But let's move on to the actual topic in hand.

## Building the code....?

### Building in an alternate universe...

alright, let's see,

I'm going to run ``source build/envsetup.sh``, and use ``lunch`` to use my device, wait for it to finish executing.

then ``make -j16`` and I'll have my images! 

The only thing I have to worry about is fixing the image to actually be compatible with my phone and boot!


### Building in the reality.

_... or so I thought._

Why would something relating to computers _just work_ anyways? silly me.

Here's some issues I ran into, and the fixes I used for them.

**Bumpy road! skechy fixes ahead!**

#### nl_intern_locale_data: assertion 'cnt < (sizeof (_nl_value_type_lc_time) / sizeof (_nl_value_type_lc_time[0]))' failed.

set env LC_ALL to C, has never failed me and seems to be the solution.

``export LC_ALL=C``

#### make: *** No rule to make target '/.txt', needed by '/out/target/common/obj/PACKAGING/checkpublicapi-cm-last-timestamp'. Stop.

Probably something to do with assuming python2 but running python3; 

So i just use the virtualenv before doing anything.

```
virtualenv2 venv
source venv/bin/activate
```

run these two commands before building!

#### make: *** No rule to make target '/out/target/common/obj/JAVA_LIBRARIES/ambientsdk_intermediates/aar/classes.jar', needed by '/out/target/common/obj/APPS/messaging_intermediates/AndroidManifest.xml'. Stop.

This is an odd fix, device trees with the ``PRODUCT_NAME`` that starts with aosp_ seem to have this issue.

I can't figure out why, but simply re-naming ``PRODUCT_NAME`` to cm_devicename fixes it.

For example: ``aosp_mako`` would be ``cm_mako``.

#### ERROR: Communication error with Jack server (2)

with: ``curl: option --no-proxy: used '--no-' for option that isn't a boolean``

The best solution to an issue is usually avoiding it alltogether.

What I mean is- Yes, just don't use jack. [it's deprecated, too](https://android-developers.googleblog.com/2017/03/future-of-java-8-language-feature.html).
And I don't even know why enabling them is the default.

Use ``make ANDROID_COMPILE_WITH_JACK:=false``.

Unrelated Note: I heard Jack In The Box is "okay."

#### No rule to make target 'dtbToolCM'

You don't have qcom_common and you're building a qcom device.

Add ``LineageOS/android_device_qcom_common`` to ``.repo/local_manifests/roomservice.xml`` like example below.

```xml
<project path="device/qcom/common" remote="github" name="LineageOS/android_device_qcom_common"/>
```

#### 'multiple definition of yylloc'; scripts/dtc/dtc-lexer.lex.o: first defined here

You ran into a:

```bash
make[4]: *** [scripts/Makefile.host:100: scripts/dtc/dtc] Error 1
/usr/bin/ld: scripts/dtc/dtc-parser.tab.o: multiple definition of 'yylloc'; scripts/dtc/dtc-lexer.lex.o:(.bss+0x0): first defined here
```

Oh no! It's kernel patching time!

This happens because GCC 10 will default to ``-fno-common`` and that will fail at the 
```c
extern YYLTYPE yylloc;
```
definition at files ``scripts/dtc/dtc-lexer.lex.c_shipped`` and ``scripts/dtc/dtc-lexer.l``

[Simply remove them, or comment them out.](https://review.lineageos.org/c/LineageOS/android_kernel_oneplus_sm8150/+/273023)


#### error: cannot access OkCacheContainer

TODO: FIX


## Conclusion

This was my first long-sized article ever. It's really bad but I had fun doing it- and I should do stuff like these more.
