---
layout: post
title: Flashing Latest Merlin on Aquila X2 with H32 chip
---

It's been a while since I wrote anything, because everything I do is pretty well documented and just requires me to actually read existing documentation, or someone else already wrote an article about it.

However this time there wasn't really a single article about it and I probaly want to come back to how to do this, so here we go.

# The situation until 2023

I bought a Aquila X2 (well, **I** really didn't but that's a story for a different day) around a year ago, and wanted to print some stuff with octoprint

... and it didn't really work at all for me, not to mention this pretty scary message.

![unsafe firmware warning](https://github.com/surepy/surepy.github.io/assets/24486494/c8b58332-44bf-4ade-a249-db187b048b6a)  
_scary!_

Thankfully, a custom firmware fixe- _oh I can't use any of these._

Thankfully _(Attempt 2)_, alexqzd _(our savior)_ has a [h32 build](https://github.com/alexqzd/Marlin-H32/releases/tag/v1.3.6) with some known issues (which I haven't really ran into), but it works perfectly for printing.

though,

```
⚠️ This firmware is no longer being maintained.
I have moved on to other projects and this firmware is no longer being developed.
```

alex stopped maintaining his fork of Marlin, and I thought a more updated build for my H32 printer would be nice.

## Our new god, shadow578 blesses us

in ``r/VoxelabAquila``, a post is made

``Marlin for the Voxlab Aquila X2 (H32) - u/shadow_578``

:O no way?

``since the aquila X2 with H32 SoC has very little support for custom firmware, i decided to port marlin to the H32 myself.``

:OO??

``a pre-built firmware is available under releases, but you can also build the firmware yourself following the readme.``

:OOOOO??

![shadow's marlin repo](https://github.com/surepy/surepy.github.io/assets/24486494/9decc8cd-acf0-4469-b840-62dd10937e0c)

aw, the prebuilt binaries aren't there anymore... wait.

``(now in upstream)``

![shookt-shocked](https://github.com/surepy/surepy.github.io/assets/24486494/259035c6-38f7-439c-bf8c-d4b29a1c7f28)

Well, let's try it out!

# Building

**NOTE: As of time of writing, H32 (aka HC32F460JEUA / HC32) is only in the bugfix-2.1.x branch, which is considered "Nightly", and could be unstable.**
**You have been warned!**

Okay, let's get Building.

## Prerequsites and Preparing

Install Visual Studio Code, with Auto build Marlin extension. _(This should also install PlatformIO IDE for you.)_

![Auto build Marlin](https://github.com/surepy/surepy.github.io/assets/24486494/59008e09-2a4e-4014-80da-4ee2cd2661fb)

Also download Marlin's source code by either cloning https://github.com/MarlinFirmware/Marlin.git, or download it from https://marlinfw.org/meta/download/

Make sure you either clone or download the ``bugfix-2.1.x`` verion/branch!

![cloning](https://github.com/surepy/surepy.github.io/assets/24486494/6d22e2c5-68b9-4fea-a271-494ee308f2e0)  
_I named it marlin2 because I already had a marlin folder, just name it marlin._

***NOTE: This will change. if H32 is in the stable release, use that one instead. (at the moment H32 is not in the stable release) I will try to edit this portion when that happens.***

now open the folder that you just downloaded or cloned, make sure you're in the directory where platformio.ini exists

![platformio.ini](https://github.com/surepy/surepy.github.io/assets/24486494/51623600-af88-43ff-b951-447f9e8f8ad8)

## Configuring Build

![default auto build configuration](https://github.com/surepy/surepy.github.io/assets/24486494/90956c00-3a27-419b-b858-33db7d592ffb)

You should be greeted with this screen. as you can see "RAMPS 14 EFB" is probably not what we want, and our archtecture is probably not "ATmega2560"

_Hint: it's not_

so let's go configuring.

Open ``platformio.ini`` and change ``default_envs`` to ``HC32F460C_aquila_101`` to change the default board configuration on platformIO.

![default_envs diff](https://github.com/surepy/surepy.github.io/assets/24486494/beac1325-48d0-4b5f-a257-e0a044bf786b)  
_(This value is defined by [ini/hc32.ini](https://github.com/MarlinFirmware/Marlin/blob/16acb57b22747b495953ecef065ba625f4dc3df8/ini/hc32.ini#L81-L87))_

Now we need to modify``Configuration.h`` and ``Configuration_adv.h`` for actaully building for our board.

Instead of trying to configure it ourselves and probably messing up, We're gonna use Sample Configurations (provided by shadow578).

He uploads his sample configurations in his github issues, linked below  
\>> https://github.com/shadow578/Marlin-H32/issues/38 << 

For this guide, we will be using this specific configuration-  
![nov 11 aquila config](https://github.com/surepy/surepy.github.io/assets/24486494/151ddd87-dca5-4dfa-a0a1-c0c89e853599)  
but even if a new configuration is released the guide should be relatively same.

Download the zip file and you will be greeted with these two files that we happened to need.

![Aquila-X2_config.zip](https://github.com/surepy/surepy.github.io/assets/24486494/559e447f-3ecc-48a2-936c-136ade215f1a)

Decompresss them and overwrite the existing configs in the Marlin folder, and your auto build configuration should update to this.

![Updated Build](https://github.com/surepy/surepy.github.io/assets/24486494/6350d5ae-f35e-47a6-af83-7f09d0bab947)

Unfortunately, We can't just build as-is with this configuration, and need to make some changes to make it build.

***And there might be more issues that you have to fix, as this branch is a nightly branch and there could be destructive changes in configurations.***

Fortunately, These errors are pretty straightforward to fix, as the compiler error just straight up tells us what to do.

![Z_PROBE_OFFSET_RANGE_ is now PROBE_OFFSET](https://github.com/surepy/surepy.github.io/assets/24486494/eddb5994-c5b8-43a5-9f67-75f6cbe604af)

for this as the compiler tells you to do, copy the ``PROBE_OFFSET_*`` portion of the default configuration, 
![Screenshot_20240328_002558](https://github.com/surepy/surepy.github.io/assets/24486494/41f76998-02f6-4ed2-98cc-266dc087ce00)

and merge it back to our ``Configuration.h``

![Screenshot_20240328_002751](https://github.com/surepy/surepy.github.io/assets/24486494/e0d75def-4a1f-455a-900e-1254304e0122)

to make it look something like this.

for ``SPEED_EDIT`` macros not being defined

![SPEED_EDIT_* not declared](https://github.com/surepy/surepy.github.io/assets/24486494/6d1c3dec-7964-4512-b8f1-1975d21844e9)

just revert this entire portion of this code (or put it at the bottom of ``Configuration_adv.h``), 

![revert this code](https://github.com/surepy/surepy.github.io/assets/24486494/cb635a4e-1bc9-4cb1-a5e1-629003084953)

and it should build!

![Successful build](https://github.com/surepy/surepy.github.io/assets/24486494/4cb86059-55ad-4254-912f-9a67dc4ee5bd)

Congratulations! now you have a latest Marlin build for your X2!

## Display Firmware

Alex's display firmware nor the default firmware will not work for us, so let's get one!

The X2's display is actually the same as Ender-3 V2's screen, named _"DWIN display"_

![DWIN display](https://github.com/surepy/surepy.github.io/assets/24486494/40f4828c-a56d-4f1c-b7bb-ccf6eff84ab3) ![aquila x2 and aquila's display](https://github.com/surepy/surepy.github.io/assets/24486494/a90613d7-bfc5-4714-bec9-1404c77acd40)

_you can see that they are literally the same._

So we just grab the LCD files from the [Example Configuration of Ender-3 V2](https://github.com/MarlinFirmware/Configurations/tree/bugfix-2.1.x/config/examples/Creality/Ender-3%20V2)

- aka ``DWIN_SET.tar.gz`` and you're done!

## Customizing

This is kind of out of scope for this guide, but you should consider looking into configurations more.

Please check [Merlin's documentation on configuration](https://marlinfw.org/docs/configuration/configuration.html) for more info!

![display timeout](https://github.com/surepy/surepy.github.io/assets/24486494/4ffd33f8-735d-4a73-ab7a-3ab9a3084039)  
_for example, you can make your display time out with this_

![manual move](https://github.com/surepy/surepy.github.io/assets/24486494/59cdbd97-cd03-4e01-8b62-29cbfc268e80)  
_I've reverted this so I can have manual move options on the printer_

![m92](https://github.com/surepy/surepy.github.io/assets/24486494/2c39b84b-dc49-45d5-ab1c-e5f4dd564af0)  
_I've also reverted this so M92 gets included_

# Flashing

Flashing process is the same with all the other firmwares.

grab a random sdcard, and format it to fat32

![fat32](https://github.com/surepy/surepy.github.io/assets/24486494/32bb35bf-1eb4-4b54-ac32-df6cc9dd6616)

extract ``DWIN_SET`` folder to sdcard

and also firmware.bin from ``<merlin-folder>/.pio/build/HC32F460C_aquila_101/firmware.bin`` to sdcard, in a folder called ``firmware``.

![directory structure](https://github.com/surepy/surepy.github.io/assets/24486494/947a960e-29b5-4ae0-aaa2-3c8470a9e87e)  
_your directory structure should look like this._

flash on your display and motherboard and you're good!

# Happy Printing!

![marlin boot screen on h32](https://github.com/surepy/surepy.github.io/assets/24486494/21ab1418-696c-4771-a323-095a08329e02)

You did it! Now you're on upstream Merlin with your H32 aquila X2!

I hope you get to make cool stuff with this!

_Thank you to shadow578 (aka Chris) for making this possible._

# Refrences/Links
- [Alex's Marlin-H32](https://github.com/alexqzd/Marlin-H32/releases)
- [Marlin for the Voxlab Aquila X2 (H32) - u/shadow_578](https://www.reddit.com/r/VoxelabAquila/comments/11nqfqi/marlin_for_the_voxlab_aquila_x2_h32/)
  - [Marlin-H32](https://github.com/shadow578/Marlin-H32)
  - [Use the ender 3v2 DWIN_SET...](https://www.reddit.com/r/VoxelabAquila/comments/11nqfqi/comment/jbtk99o/)
- [Sample Confirgurations for Aquila X2](https://github.com/shadow578/Marlin-H32/issues/38)
  - [Deprecated Sample Configurations For Aquila X2](https://github.com/shadow578/Marlin-Configurations-H32)
- [Marlin](https://github.com/MarlinFirmware/Marlin/)
  - [hc32.ini](https://github.com/MarlinFirmware/Marlin/blob/bugfix-2.1.x/ini/hc32.ini)
  - [Display update guide for Ender-3 V2 family screens](https://github.com/MarlinFirmware/Configurations/tree/bugfix-2.1.x/config/examples/Creality/Ender-3%20V2)
- [platformio.ini](https://docs.platformio.org/en/latest/projectconf/index.html)
- [Is the Aquila X2 better than the original? - A deeper look into the Voxelab Aquila X2 components ](https://youtu.be/jIL3Gwd8k0Y)
  - (Used for DWIN screenshot) 

