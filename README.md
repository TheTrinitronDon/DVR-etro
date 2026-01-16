# DVR-etro
Aiming to repurpose consumer/commercial-grade DVR systems with Embedded Linux SoCs. These systems have tremendous potential to be a flexible and affordable retro gaming platform, as the hardware offers solid image processing and memory management capailities. They have pretty generous SDRAM, H.264 Video CODEC, VGA/Analog RGB/CBVS Analog processing, built-in SATA HDD (typically 500GB-1TB), USB Mouse/Keyboard drivers, LAN/WLAN, and affordable price tag (I find them regularly for ~$10-20USD at thrift stores).

As a proof-of-concept, I intend to run DOOM on one or more of my DVRs. While I have modchipped/soft-modded retro game consoles, I have never created my own project or written much (or any) code. The intent of this project is for me to learn more about hardware hacking embedded devices, and - if successful - could be a fun project for others to do the same. I've done a fair bit of research and, now that I believe this to be feasible, will document my progress here.

# DVR System Compatability
My experience with video gear stems from an interest in analog glitch art, and I purchased my first security DVR with the intent of circuit bending the VRAM. While researching the hardware, I realized there was a dearth of detailed datasheets and seldom many service manuals. I did find, however, that many consumer-grade DVRs are white-labelled. So, when referring to a specific DVR, I will do my best to find the OEM and also keep a running list of units that are re-badged/re-cased.

# Pinetron PDR-XM300X | PDR-XM3004/PDR-XM3008
The PDR-XM3 is a standalone H.264 DVR unit available in 4-, 8-, and 16-channel models. (NOTE: While the PDR-XM3004 and PDR-XM3008 use the same motherboard, I cannot confirm if the 16-channel model will be compatable).

Also sold as:
True-H SSA-0412/SSA-0824
CCTV Star SSA-0412/SSA-0824
Nippon Security Systems NSD-3004/NSD-3008

Chipset/Hardware:
-Nextchip NVS2200F - ARMv9-Core NDVR SoC
-Nextchip NVR1114XM - 4-ch Video CODEC w/ 4-ch Audio
-i-MARV MDIN220 - Digital Video HD Upconverter
-Samsung K4H60838J-LCCC - 256Mb J-Die SDRAM (32Mbitx8 / DDR400)  
-Spansion S29AL016J70TFI01 - 16-Megabit Flash Memory (3V-Only, Top Boot Sector, 70ns access time)
-Marvell Devices 88SA8052-TBC2 - SATA/PATA Bridge
-Integrated Device Technology IDT1338 - RTC w/ Non-Volatile Battery-Backed RAM
-Hynix HY27US08121B - 512 MBit (32Bx64) DDR SDRAM NAND Flash
-Davicom DM9161AEP - 10/100Mbps Fast Ethernet Tx/Fx Single-Chip Transceiver

Kernel/Firmware Details:
Pinetron XM3 Firmware is available to download for free from from the Pinetron website (https://pinetron.ru/download/firmware/pdr-xm3000-firmware?tmpl=component). 

After extracting xm3k_all_oem01_11.08.09_v1.10.img, there's a binary file titled vmlinux.bin and xm3k_app.yaffs. I am unfamiliar with .yaffs filesystems, so I extracted vmlinux.bin. And, when extracted, there are two files: a CPIO Archive (application/x-CPIO) file titled 18A20, and a plaintext document titled 2495CC. Upon opening the latter in notepad, it's the complete male config file in plaintext. 

Here's what stood out to me, although I'm very new to this:

Linux kernel version 2.6.14
#
#System Type
#
CONFIG_ARM=y
CONFIG_ARCH_GM=y
CONFIG_PLATFORM_GM8180=y
CONFIG_USE_FRAMEBUFFER=y
#
#GM Platform Options
#
CONFIG_PLATFORM_AHBDMA=y
#CONFIG_AHB_DMA_TEST is not set
CONFIG_PLATFORM_APBDMA=y
#CONFIG_PLATFORM_FIQ_RELAY is not set
CONFIG_SYS_CLK=165888000
CONFIG_UART_CLK=18432000
CONFIG_SDRAM_SIZE=0x8000000
CONFIG_MACH_GM=y
#
#Boot options
#
CONFIG_ZBOOT_ROM_TEXT=0x0
CONFIG_ZBOOT_ROM_BSS=0x0
CONFIG_CMDLINE="mem=128M console=uart,shift,2,io,0xF9820000,115200 initcall_debug user_debug=31 initrd=0x2000000,6160384 init=/init root=/dev/ram0 rw"
#CONFIG_XIP_KERNEL is not set

#
#Generic Driver Options
#
#CONFIG_STANDALONE is not set
#CONFIG_PREVENT_FIRMWARE_BUILD is not set
#CONFIG_FW_LOADER is not set
#CONFIG_DEBUG_DRIVER is not set

Supporting Research:
In Late-2024, I purchased a DVR branded as a "TRUE-H H.264 DVR" The bottom badge listed the  model number as "SSA-0412," with research showing SSA-0812 model number for 8-channel units. Inside, I found my first clue that this was a white-label job: a sticker with the code "XM3K-PINETRON-1" on the touch-panel circuit. Also, "XM3008-MAIN V1.1" is silkscreened onto the top side of the motherboard by the alarm I/O. On the underside, there's a silkscreen around reistors RE4, RE5, and RE6 that points to depictions of the proper configuration for 4-channel and 8-channel models. Unused footprints were clearly for a second NVP1114MX 4-ch Video Processing IC, supporting passive components, and BNC inputs. 

This is why I am comfortable in saying that Pinetron is the OEM for the white-labelled DVRs listed above, and that they use the same motherboard layout for both the 4-channel PDR-XM3004 and 8-channel PDR-XM3008.
