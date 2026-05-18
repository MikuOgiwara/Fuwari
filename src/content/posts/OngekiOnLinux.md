---
title: Ongeki on linux
published: 2026-03-21
description: ''
image: ''
tags: [Linux, Tech, Arcade]
category: Arcade
draft: true 
lang: ''
---


# Introduction
I gona tell you my journey to play ongeki on linux.
When you get your hands on an ontroller, the first thing you quickly realize is that it was never designed with Linux in mind.
Under Windows, things are simple: the game talks to the controller through mu3io.dll, which handles inputs, LEDs, and the overall communication with the hardware.  

My frist thing to do was to get a game runing on linux. So I just copy my windows installation.install wine try to open and the game work ?  
Perfect now the next step is to get a controler.  
I gonna only talk about a ontroller, If you use other con, I don't cover in it. I gona focus on the led protocol cauz input have already have be documented on multiple driver, tools.

**I don’t provide any game links ! dump your own data. I’m not responsible for what you do.**
---
# Preface
- [Introduction](#introduction)
  - [**I don’t provide any game links ! dump your own data. I’m not responsible for what you do.**](#i-dont-provide-any-game-links--dump-your-own-data-im-not-responsible-for-what-you-do)
- [Preface](#preface)
- [The begine](#the-begine)
- [Lets make the game work !](#lets-make-the-game-work-)
  - [Make my own DLL](#make-my-own-dll)
  - [Use a another DLL](#use-a-another-dll)
    - [The Pink Issue](#the-pink-issue)
      - [Understanding the USB device and how the ontroller talk wis ongeki](#understanding-the-usb-device-and-how-the-ontroller-talk-wis-ongeki)
      - [Fuck winUSB](#fuck-winusb)
  - [And why just not pathtrue the ontroller ?](#and-why-just-not-pathtrue-the-ontroller-)
  - [Back to The brutally DLL but...](#back-to-the-brutally-dll-but)
  - [So the end ?](#so-the-end-)
- [Wine optimisation](#wine-optimisation)
- [end](#end)

---

# The begine
Ontroller talk wis segatools using winUSB this protocol dont work/existe on linux, I can use natif keyboad input for ongeki and ontroller, but I realy hate thats, I want my cute pink on wad UwU ! (remember thats for later lol).
On Windows, things are simple: the game talks to the controller through mu3io.dll(who "simulate" a IO4 protocole), which handles inputs, LED.  
On Linux, however, the situation is different.  
The controller identifies itself as the following USB device:
```Bus 001 Device 006: ID 0e8f:1216 GreenAsia Inc. ONTROLLER```  
This looks like a standard USB controller.

---

# Lets make the game work ! 
So for the ontroller I have try thres things:

- Try to make my own DLL. 
- Use a another DLL
- Use wine And try to pathtrue the ontroller and using MU3IO.dll


So lets cover in Order what I have try and do.

## Make my own DLL
I am too stupid to do it lmao.

## Use a another DLL 
Some one send me too discord this [this custom DLL for Ontroller on linux](https://github.com/nyairobi/brutally-simple-ontroller-driver), I literaly Wa I needed.
So after a compilation, put on my ongeki folder and the game just work perfect. I got all input, lever and 6 led perfecty work ! 
### The Pink Issue
As say on the read me off the dll. Wad led not work. And was I have say "I want my cute pink on wad" I need to have my pink Wad, So let try to make this to work! But for thats I need to understand how ongeki talk with [mu3io.dll](https://github.com/SirusDoma/Mu3IO.NET/blob/main/Source/Controller/Ontroller.cs) on windows.

#### Understanding the USB device and how the ontroller talk wis ongeki
To do thats the most simple approche was be to read [mu3io.dll](https://github.com/SirusDoma/Mu3IO.NET/blob/main/Source/Controller/Ontroller.cs) code and listen the usb protocole on wine using wireshark to compare the metode used bien mu3io and Brutally driver.
After a regarding (and claudeCode lol).I have understand how the ontroller talk wis the game:  

- **For buttons**
Buttons are sent to the game as a bitmask inside the input USB packet read by mu3io.dll.  
Each bit represents the state of a button:  
```
Bit 0 : Button 1
Bit 1 : Button 2
Bit 2 : Button 3
Bit 3 : Button 4
Bit 4 : Button 5
Bit 5 : Button 6
Bit 6 : Wad Left
Bit 7 : Wad Right 
```
mu3io.dll reads this value, decodes the bits, and forwards the button states to the game.  

- **For levers**  
The side levers are transmitted as analog values in the controller input packet.

These values represent the current lever position.
mu3io.dll reads them and forwards the position to the game, which interprets the movement as analog input for move the waifu.

- **For LED**
LED are controlled by sending a bulk USB packet from mu3io.dll to the controller.
LED Packet Format (60 bytes):
```
Bytes 0-2 : 0x444c01 header ("DL" + version)
Bytes 3-59: RGB color data
Bytes 3-20 : First 6 controller LEDs
Bytes 21-26: WAD LEDs (2 RGB entries)
Bytes 27-59: Remaining entries (unused)
```
Each LED uses 3 bytes (R, G, B) which define its color.
So wis this information I know how ongeki send information to the led and get information for the input. 

---

#### Fuck winUSB
So wis this information we know how the game work, But on linux, We need to get a HID device, and unfortunaly the ontroller driver only allow us to use, Keybord or WinUsb input.
but what is winUsb input ?


## And why just not pathtrue the ontroller ?

## Back to The brutally DLL but...


## So the end ? 

# Wine optimisation
# end