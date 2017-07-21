# PoC for SVE-2016-7930 : multiple buffer overflows in Samsung Galaxy bootloader
SVE-2016-7930 is a bug in Download mode (ODIN) of Samsung Galaxy phones. ODIN is a proprietary customization implemented by Samsung in the open-source LK bootloader. The bug allows to run arbitrary code on devices with Secure Boot enabled (locked bootloader). Technical details have been released at SSTIC 2017 conference (https://www.sstic.org/2017/presentation/attacking_samsung_secure_boot/).

## Disclaimer
YOU'RE THE ONLY RESPONSIBLE FOR ANY DAMAGE CAUSED TO YOUR HARDWARE/SOFTWARE/WARRANTY/DATA/CAT/ETC...

## Prerequisites
* Samsung Galaxy S5 G900A with firmware G900AUCS4DQB1
* Blank SD-card (**data will be erased !!!**)
* Latest Heimdall source code that includes T-Flash feature (https://github.com/Benjamin-Dobell/Heimdall)
* TWRP image (https://dl.twrp.me/klte/twrp-3.1.0-1-klte.img.html)

## Firmware
**Flashing new firmware or OTA update will erase user data**

If your device doesn't run G900AUCS4DQB1, you should flash full firmware G900AUCS4DPH4, then apply OTA updates G900AUCS4DPH4-to-U4DQA1 & G900AUCU4DQA1-to-S4DQB1).

OTA updates can be downloaded directly from AT&T servers:
```
$ wget --user-agent="SAMSUNG-Android" "https://xdmd.sl.attcompute.com/agents/42925/1488/SS-G900AUCS4DPH4-to-U4DQA1-UP"
$ wget --user-agent="SAMSUNG-Android" "https://xdmd.sl.attcompute.com/agents/42925/1488/SS-G900AUCU4DQA1-to-S4DQB1-UP"
```
## Procedure
0) Unzip *G900AUCS4DQB1_boot_usb.img.gz*
```
$ gunzip G900AUCS4DQB1_boot_usb.img.gz
```
1) Apply *heimdall-increase_fileTransferSequenceMaxLength.patch* on Heimdall source tree & build it
2) Insert blank SD-card in your phone
3) Boot in Download mode (Hold Volume Down + Home buttons while booting)

4) Prepare the SD-card with Heimdall
```
$ heimdall flash --tflash --verbose --repartition --pit ./KLTE_USA_ATT.pit --BOOT ./G900AUCS4DQB1_boot_sdcard.img --RECOVERY ./twrp-3.1.0-1-klte.img
```
5) Reboot in Download mode again
6) Trigger the exploit
```
$ heimdall flash --tflash --verbose --no-reboot --BOOT ./G900AUCS4DQB1_boot_usb.img
```
This PoC will boot the recovery image flashed on the SD-card at step 4. This recovery image can eventually be replaced with a custom Android boot image.