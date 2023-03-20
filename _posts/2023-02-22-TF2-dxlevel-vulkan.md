---
layout: post
title: "TF2: -dxlevel vulkan"
---

## Introduction

**WARN: this comes with a VAC risk, VERY unlikly in my opinion but just keep that in mind.**

I decided one day to add dxvk and d3d8to9 to tf2, and found out that it comes with at least a **25%** performance gain.

This is a blog post on how I found it out, and thought that this experience was funny enough not to write about.

## Why did you do this

It was 2023/2/17, and I was having a chat with "pony goon"- just some tf2/sniper gameplay related chat.

![image](https://user-images.githubusercontent.com/24486494/220641288-80b783ee-2d3b-40e3-8b05-e24e00cbb7cb.png)

~~blame mastercommfig wiki for suggesting me dx10 idk~~

they suggested me to use dx8 for better performance and due to the sniper scope fish eye effect.

After procrastinating a bit, I finally tried it.

It was good, I was hitting shots more and it was more pleasureable to aim; but my Reshade was broken.

This wasn't surprising, as it's a dx9 version that is currently installed into tf2 at the moment. 

I've always used reshade for frame time counter / constant clock / and fidelityFX CAS (I find it really nice)

and occationnal meme play, and wanted back. 

Just have to run ``Reshade_Setup.exe`` and...

![image](https://user-images.githubusercontent.com/24486494/226157285-9ebc84f3-374b-41b3-99eb-ca955738ae58.png)

huh, no dx8. why?

![image](https://user-images.githubusercontent.com/24486494/226157317-eaf8e0cb-6060-4446-9256-ac177f8a6efa.png)

What's this?

``This will convert incoming Direct3D 8 API calls and bytecode shaders to Direct3D 9, which ReShade then can work with.``...?

and then it hit me.

## The cooking

![image](https://user-images.githubusercontent.com/24486494/220642103-ba887317-9c7d-4ecd-b32f-990a4ac00950.png)

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/K2m1LfZY_MI?start=814" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

> "... a 1.79 times performance Improvement is enormous..."
> 
> Luke: "Yeah, uh dxvk vulcan-based implementation of D3D9/D3D10 and D3D11, yeah" 
> 
> Linus: "for Linux / Wine / Windows"
> 
> Luke: "apparently, yeah so that's cool"
> 
> Linus: So, Linux API hackery, being used to make Intel's...
> 
> Luke: like **79% performance increases with cs:go** on Windows!
>
> Linus: imagine anything else in the world where by instead of running it natively, 
> **running it through a translation layer gets you an 80 performance Improvement.**

I remembered listening on the WAN show about how dxvk was improving old directX game performance in windows with Intel's Arc GPU's with DXVK

... and decided to test how well it works on tf2, but on dx9 as dx8 wasn't an option.

# Why is this working

In case I get instantly VAC banned for whatever reason i used a free account

![image](https://user-images.githubusercontent.com/24486494/220643304-60191c52-7745-4db4-be0c-befb5b63b1c4.png)

(1 votekick later....)

![image](https://user-images.githubusercontent.com/24486494/226156761-d5babffe-d817-42a1-966f-e2612b337068.png)

![image](https://user-images.githubusercontent.com/24486494/220644198-9ee02f67-67c8-4e0a-8303-fd471159e46c.png)

The frame stability improvments were VERY noticable.

I was hitting much more shots because of how smooth everything was compared to before; It made me take new risky plyas,
and got away with that risky play.

It was kind of insane how powerful dxvk was. but we needed to go further.

# Stop

Okay, dxvk from dx9 works AMAZING, but what if we add ANOTHER graphics API?
\- statement for the utterly deranged

Above I learned that d3d8to9 exists, due to reshade; but what if we just..

> .py — 02/20/2023 1:09 AM
> 
> ok what if
> 
> dx8 (game) 
> 
> -> dx9 (d3d8to9) 
> 
> -> vulkan (dxvk)

First the game refused to launch

> .py — 02/20/2023 1:17 AM
>
> the game runs
> 
> i just get a black screen
> 
> maybe i disable fullscreen
> 
> ?
> 
> .py — 02/20/2023 1:26 AM
> vac is proabably super convused rn

but through sheer power of restarting over and over again 

> .py — 02/20/2023 2:48 AM
> 
> i got it to launch

let's join a server!

![image](https://user-images.githubusercontent.com/24486494/226156876-20095531-c96b-483d-b8ef-c8e0e233dd1a.png)

![20230220021422_1](https://user-images.githubusercontent.com/24486494/220643857-2c509773-b898-4ffb-a786-b6af84a5045b.jpg)

![image](https://user-images.githubusercontent.com/24486494/226156917-0de9332a-da11-4bc7-95c5-5eb9ecc02c21.png)

## Okay, what if I'm wrong?

From then I thought to myself: What if it's just a dumb placebo? What if I just look dumb right now?

So I created a bot server of 32 players, recorded a demo and used this following launch options to compare frametimes min/max/avg instantly between running in dx8 or keeping dx9 version.

``%command% -insecure -steam -game tf -dxlevel 80 +timedemo benchmark -novid -noquicktime -nojoy -nohltv -particles 1 -precachefontchars``

Results?

![image](https://user-images.githubusercontent.com/24486494/226158411-3477d552-c7a9-4f24-9c75-be7cfd06c4c4.png)

![image](https://user-images.githubusercontent.com/24486494/226158487-71a5f9fc-7011-4bc8-a277-1675f48b2769.png)

![20230220_042353](https://user-images.githubusercontent.com/24486494/226158495-4a347bf4-ec51-4b0e-aadd-6bedaaa6d722.jpg)

## Putting a nail the coffin 

I decided to distribute this to few people I know and trust in tf2. One of the people I posted to was Fiona.

She kindly and took some benchmaks for me, which was awesome as my tests weren't exactly scientific.

![image](https://user-images.githubusercontent.com/24486494/226158682-c447716b-2c94-4474-bcfa-de0304b77775.png)

she returned with this legendary graph.

![image (1)](https://user-images.githubusercontent.com/24486494/226158630-d8eabd19-99ea-49ff-9dd3-1ea4fb739b8b.png)

## Con??clusion??? 

Linux API hackery does make windows applications- like tf2 run much better.

I'm probably gonna be using this to as much games I can, for extra frames.
