## How I made my GTX 770 work in my PC along my RTX 3080 to provide macOS compatibility

## Introduction
Back on February 4th, I upgraded my PC with a Strix RTX 3080 OC which replaced my previous Vega 56. As you can imagine, this brought about a whole slew of compatibility problems for macOS. The biggest and most devastating one was that the RTX 3080 is not supported in any way in any version of macOS. This means no Web Drivers on High Sierra either. One workaround I used for a while was using macOS with a single monitor in VESA compatibility mode with 1080p resolution thanks to the UEFI GOP, but this was far from ideal. VESA compatibility mode means that there is no acceleration and the CPU must handle rendering tasks. Aside from this making the entire OS feel *extremely* sluggish, it also means that I can't use any macOS apps that use Metal. I had to find another, better solution.

## Kepler and macOS
As I use macOS quite a bit on my PC, I wanted to have full resolution, multimonitor and acceleration in macOS. I started digging around my house and project workspace and managed to find an [MSI Twin Frozr GTX 770 GAMING 2GB](https://www.msi.com/Graphics-Card/N770-TF-2GD5OC/Specification). This was great news as Kepler GPUs are natively supported in macOS since Mac OS X 10.8 Mountain Lion. These Kepler GPUs are still supported in the latest macOS Big Sur in order to support older Macs that still use this GPU architecture, which is convenient for us hackintoshers.

The GTX 770 was in extremely poor condition with sticky residue all over it and it was missing its shroud and fans. I decided to clean it up, give it some care since it was going to be in my main PC. I gave all of it a thourough wash, let it sit overnight, then tested it in my test bench.

It worked fine in there, booted windows fine and ran some games. Since it worked, I decided to buy the extra PCIe power cables that I need for my modular PSU (they cost eight dollars lol) and those arrived within two days.

Now, it was as easy as throwing it in my pain PC and booting macOS, right?

Unfortunately, this is where I ran into some major problems.

## Dual GPU?
Now, I had some questions in the back of my mind about having an RTX 3080 and a GTX 770 in the same PC with the latest NVIDIA drivers. I ran the extra pcie power cables for the 770 (two eight pins) and got the GTX 770 all situated into my PC. ([You can see how it ended up looking here](https://twitter.com/thatsniceguy/status/1378241011621580802?s=21)).

Booting up Windows, everything surprisingly worked, including G-Sync on my main monitor. From here I decided to check out the 770 in Windows just to make sure I knew what I had and everything checked out fine as a proper GTX 770. I decided it was time for the moment of truth, so I shut down my PC, unplugged all the display cables from the 3080 and plugged them into the 770, and hit the power button.

*aaaaaaandd* my motherboard failed to POST...

## UEFI GOP and VBIOS
After checking the beep code from the little piezo I attatched to my motherboard (and looking at the BIOS debug LEDs), it was a GPU firmware initialization issue. I thought that this wouldn't be a problem as all GTX 770s shipped with a UEFI-capable "hybrid" driver, but something was obviously off. I shut down the PC again, plugged all my displays into the 3080 again, powered on, and it succesfully POSTed and I chose to boot to Windows.

From here, I decided that updating the VBIOS on the 770 would be my best course of action as maybe it was made more compatible or something. Next I downloded the latest x86_64 `nvflash` and [the latest VBIOS I could find for this card](https://www.techpowerup.com/vgabios/160720/msi-gtx770-2048-140530). Since I had two GPUs working in the system at the same time, I wasn't sure if `nvflash` would attempt to flash both cards, or randomly choose one of the two or something. 

I decided to RTFM which tought me about the `--list` and `-i` options, and here's what they do:
- `--list`
  - Lists the NVIDIA GPUs in your system
- `-i`
  - Enables you to choose what GPU to perform an action on

The output of `nvflash --list` on my PC ended up being something like this:
```
NVIDIA Firmware Update Utility <Version x.xxx>

NVIDIA display adapters present in system:
<0> GeForce GTX 770 (10DE,xxxx,xxxx,xxxx)
<1> GeForce RTX 3080 (10DE,xxxx,xxxx,xxxx)
```

Those numbers (`<0>` or `<1>`) before the GPU names are **index numbers**.