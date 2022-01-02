# WoeUSB-NG Patch

## What's this?

When I tried to create a bootable Windows 11 bootable USB disk, [WoeUSB-NG](https://github.com/WoeUSB/WoeUSB-ng) came to my plate and it seems pretty cool. However, when I started to use it, its progress bar really confused me and that write-delay is not suitable for the cases like writing data into a disk.

Then I read the source code of `core.py`. I figured out there are several glitches that caused the above confusion and potential data writing delay issue. So I patched the file to solve the issues I encountered.

This patch is to address / fix the following issues:

1. Removed pseudo progress bar feature. WoeUSB-NG uses a timer progress bar which actually does not show actual progress which is just a decoration.
2. Switched buffered write to sync-IO write. WoeUSB-NG uses buffered write to write Windows files to USB disk. This would cause a potential disk loss issue and would take a longer time when OS initiates write-back.
3. Added additional writing progress information to show the actual data writing process. This is useful for users to understand how much data is written already in the USB disk rather than pseudo progress data.

## How to use it?

Simple. Just copy this `core.py` and replace the original one in the WoeUSB-NG source code directory then use `pip3` to install the package.

## Side Effect

Because this patch uses sync-IO to write data, users will notice that the data copying part is taking a very long time, which is normal. IMHO, it's better to show actual data copy progress than pseudo progress and confused extreme long time consuming on data write-back process.

## Command Output Example

```
$ sudo ./woeusb --device  ~/Downloads/Win11_Chinese\(Simplified\)_x64v1.iso /dev/sda
WoeUSB v0.2.10
==============================
Mounting source filesystem...
Warning: File /media/woeusb_source_1641101580_372386/sources/install.wim in source image has exceed the FAT32 Filesystem 4GiB Single File Size Limitation, swiching to NTFS filesystem.
Refer: https://github.com/slacka/WoeUSB/wiki/Limitations#fat32-filesystem-4gib-single-file-size-limitation for more info.
Wiping all existing partition table and filesystem signatures in /dev/sda
/dev/sda: 2 bytes were erased at offset 0x000001fe (dos): 55 aa
/dev/sda: calling ioctl to re-read partition table: Success
Ensure that /dev/sda is really wiped...
Creating new partition table on /dev/sda...
Creating target partition...
Making system realize that partition table has changed...
Wait 3 seconds for block device nodes to populate...
Cluster size has been automatically set to 4096 bytes.
Creating NTFS volume structures.
mkntfs completed successfully. Have a nice day.
Mounting target filesystem...
Copying files from source media...
Writing 128/128 byte(s) to /media/woeusb_target_1641101580_372386/autorun.inf
Writing 436642/436642 byte(s) to /media/woeusb_target_1641101580_372386/bootmgr
Writing 2003272/2003272 byte(s) to /media/woeusb_target_1641101580_372386/bootmgr.efi
Writing 94680/94680 byte(s) to /media/woeusb_target_1641101580_372386/setup.exe
Writing 16384/16384 byte(s) to /media/woeusb_target_1641101580_372386/boot/bcd
Writing 3170304/3170304 byte(s) to /media/woeusb_target_1641101580_372386/boot/boot.sdi
...snip...
Writing 5242880/511408175 byte(s) to /media/woeusb_target_1641101580_372386/sources/boot.wim
Writing 10485760/511408175 byte(s) to /media/woeusb_target_1641101580_372386/sources/boot.wim
Writing 15728640/511408175 byte(s) to /media/woeusb_target_1641101580_372386/sources/boot.wim
...snip...
Writing 508559360/511408175 byte(s) to /media/woeusb_target_1641101580_372386/sources/boot.wim
Writing 511408175/511408175 byte(s) to /media/woeusb_target_1641101580_372386/sources/boot.wim
Writing 260656/260656 byte(s) to /media/woeusb_target_1641101580_372386/sources/bootsvc.dll
Writing 1976/1976 byte(s) to /media/woeusb_target_1641101580_372386/sources/cdplib.mof
...snip...
Source media seems to be Windows 7-based with EFI support, applying workaround to make it support UEFI booting
INFO: Detected existing EFI bootloader, workaround skipped.
Installing GRUB bootloader for legacy PC booting support...
Installing for i386-pc platform.
Installation finished. No error reported.
Installing custom GRUB config for legacy PC booting...
Unmounting and removing /media/woeusb_source_1641101580_372386...
Unmounting and removing /media/woeusb_target_1641101580_372386...
You may now safely detach the target device
Done :)
The target device should be bootable now
```
