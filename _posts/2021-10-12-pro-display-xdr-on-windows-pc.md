---
title: Pro Display XDR on Windows PC
description: 6K 60Hz 10-bit HDR without Thunderbolt 3
tags: [Apple, Pro Display XDR, Windows, PC, Nvidia, Thunderbolt, DisplayPort, USB, DSC, HDR]
image: /assets/img/2021/10/12/pro-display-xdr-on-windows-pc/settings@2x.png
---

# Pro Display XDR over USB 3.2 Gen 2×2 with DSC

![Settings > Display > Advanced display]({{ page.image }}){: srcset="{{ page.image }} 2x" }

When Pro Display XDR is connected via the 40 Gbit/s Thunderbolt 3, it uses dual-link SST DisplayPort in High Bit Rate 3 (HBR3) mode.  A bandwidth of 36.64 Gbit/s is required for transmitting uncompressed 6K 60Hz 10-bit HDR video.  36.64 Gbit/s greatly exceeded the 20 Gbit/s of USB 3.2 Gen 2×2, thus it is not possible to transmit the video uncompressed over USB 3.2 Gen 2×2.

However, it is lesser-known that Apple's Pro Display XDR can actually run at its full `6016 × 3384, 60 Hz` with 10-bit HDR, using under 20 Gbit/s of USB 3.2 Gen 2×2.  [DSC (Display Stream Compression)](https://vesa.org/vesa-display-compression-codecs/dsc/), a _visually lossless_ compression standard, is the key technology that made it possible.

To deliver resolution higher than 4K 60Hz over USB 3.2 Gen 2×2, both the display and the video graphics card must support DSC. With 10-bit HDR (30 bpp) compressed to 12 bpp at 2.5:1 compression ratio, the bandwidth required to deliver compressed 6K 60Hz 10-bit HDR video is only 14.66 Gbit/s.

When Pro Display XDR is connected via USB 3.2 Gen 2×2, it uses single-link SST DisplayPort in High Bit Rate 2 (HBR2) mode.  The required bandwidth of 14.66 Gbit/s for transmitting compressed 6K 60Hz 10-bit HDR video is less than the 17.28 Gbit/s of HBR2.  USB-C DisplayPort Alternate Mode does not employ USB 2.0 lanes, so DisplayPort Alternate Mode can be used together with USB 2.0 for video and data transmission via a single cable.

## GPU

From [Pro Display XDR Technology Overview](https://www.apple.com/pro-display-xdr/pdf/Pro_Display_White_Paper_Feb_2020.pdf):

> Pro Display XDR requires a GPU capable of supporting DisplayPort 1.4 with Display Stream Compression (DSC) and Forward Error Correction (FEC), or a GPU supporting DisplayPort 1.4 with HBR3 link rate and Thunderbolt Titan Ridge for native 6K resolution.

## Adapter & Cable

The key to get Pro Display XDR to work with USB 3.2 Gen 2×2 is to use a correct adapter or cable.

For video graphics cards that have a built-in USB-C VirtualLink port, like Nvidia GeForce RTX 20 series or AMD Radeon RX 6000 series, full-featured USB-C to USB-C cables certified for 20 Gbit/s should work.  However, do not use active Thunderbolt cables like the Thunderbolt 3 Pro Cable which comes with Pro Display XDR, because active Thunderbolt cables are not backward compatible with USB 3.  Some video graphics cards like ProArt GeForce RTX 50 series have a built-in USB-C port without support for data transmission, that the display settings cannot be adjusted when connected via such port.

For video graphics cards without a built-in USB-C port, a **bidirectional** USB-C to DisplayPort adapter or cable is needed.  In additional, in order to use the USB hub on the back of Pro Display XDR, adjust brightness, or change display preset, an adapter or cable that supports USB 2.0 lanes is required.

The best DisplayPort to USB-C adapters and cables supporting both DisplayPort Alternate Mode and USB 2.0 channel are the following:

- Belkin Charge and Sync Cable for Huawei VR Glass (CAZ001)
- FIBBR TF-VRA
- Wacom Link Plus (ACK42819)

## Add-in Card

Given that verified cables and adapters are incresingly difficult to source, an alternative is to get a Thunderbolt or USB 4 add-in card that converts DisplayPort to Thunderbolt or USB.

Be careful that add-in cards have very limited montherboards compatibility.  For ASM4242 based USB 4 add-in cards, including the built-in ones on certain motherborads like ProArt X870E-Creator WiFi, make sure to update the add-in card firmware to the latest.

## Installing Apple Boot Camp

![Boot Camp Control Panel](/assets/img/2021/10/12/pro-display-xdr-on-windows-pc/boot-camp-control-panel@2x.png){: srcset="/assets/img/2021/10/12/pro-display-xdr-on-windows-pc/boot-camp-control-panel@2x.png 2x" }

Boot Camp drivers need to be installed to adjust brightness or change display preset under Windows.

1. [Microsoft Visual C++ Redistributable](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist) must be installed first, or the Boot Camp Control Panel would show "No supported external displays connected" due to `BootCampService.exe` crashing in the background.

2. The drivers can be extracted from the [BootCampESD.pkg](https://swcdn.apple.com/content/downloads/48/55/002-34411-A_GQNVCB6UOC/m67xlo3dipbdxmdeeocd39hmxka5o9dg2k/BootCampESD.pkg). If you don't have a Mac, use this pre-extracted archive [WindowsSupport.zip](https://github.com/BootCampESD/BootCampESD/releases/download/002-34411-A_GQNVCB6UOC/WindowsSupport.zip). Only the followings  inside `BootCamp/Drivers/Apple` are needed, everything else can be deleted before installation.
    - `AppleDisplayNullDriver`
    - `AppleProDisplayXDRUSBCompositeDevice`
    - `BootCamp.msi`

3. Normally Apple Boot Camp would refuse to install on non-Apple devices. To install Boot Camp on any Windows PC, start the installer from the Command Prompt as administrator:

    ``` sh
    msiexec /i BootCamp.msi
    ```

---

# Bottomline

With the right graphics card, the right cable, and right drivers, Pro Display XDR can deliver brilliant 6K 60Hz 10-bit HDR with its full functionality on any Windows PC.
