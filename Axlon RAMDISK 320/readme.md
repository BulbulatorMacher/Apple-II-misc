# Axlon RAMDISK 320
## Introduction
Axlon RAMDISK 320 is an early SSD for Apple II. It has been introduced in 1982.
It contains 320k RAM, enough to store contents of two floppy drives.
It retains contents of RAM when powered off using built-in power supply and battery.

Product had extensive manual, but it has not been archived.
Some of the details have been included in the review from 1982:
https://txbobsc.com/aal/1982/aal8207.html#a6

Disk images and photos of the unit are available at:
https://yesterbits.com/2017/01/22/axlon-ramdisk-320/

## Theory of operation
### Hardware
Controller card does not containg typical firmware ROM. Instead it contains (besides 320k data RAM) 2k firmware RAM. Firmware is copied from floppy disks.
| Address range | Usage |
| - | - |
| C0n0      | bits 0-3 - sector number (0 to 15); bit 4 - disk 1 if 0, disk 2 if 1 |
| C0n1      | track number (0 to 39) |
| CnXX      | Firmware RAM, Read-Write (256 bytes) |
| C800-C8FF | Sector data, Read-Write |
| C900-CFFF | Frimware RAM, Read-Write (remaining 1792 bytes) |

Address ranges C0n0, C0n1 and C800-C8FF work independently of firmware RAM. They could be used at any time even without loading firmware.
Although called "disk", "track", "sector" and "sector data" it's just fancy bank switching scheme designed to mimic floppy disk layout.

### Software
Controller card does not containg typical firmware ROM. Instead it contains 2k RAM. Firmware is provided on a floppy disks.
When executed it copies firmware to card RAM and makes a sector-by-sector copy of a floppy to the RAMDISK RAM.
Firmware is compatible with DOS 3.3, ProDos, Pascal and CP/M (Microsoft SoftCard).

Card wil auto-boot if placed in slot 7 and loaded with firmware and DOS or ProDos floppy.
When duplicating DOS 3.3 or ProDos floppy to RAMDISK, it is copied without any modifications yet can be booted.
This suggests that RAMDISK-specific code is contained within firmware RAM and it attaches itself to the RWTS routine during boot, without the need for modifying disk image.
### Compatibility with SoftCard
Card will work with SoftCard when RAMDISK is placed in slot 4 (as C: and D:) or 5 (E: and F:). This leaves slot 7 for Microsoft SoftCard.
Software doesn't work with PCPI Applicard nor Microsoft Premium SoftCard IIe - card specific software has not been found.
Softcard is activated by writing to slot ROM - most cards will ignore that, but for RAMDISK it garbles its firmware.
**It has not been determined if there is some write-protection for firmware or data.**
For compatibility place RAMDISK in slot with lower number than SoftCard.
## Using disk images
Master disk images are by default configured to have Floppy disk in slot 6 and `RAMDISK` in slot 4 and to automatically copy floppy contents to RAMDISK on boot.
Following are instructions to autoboot vanilla `DOS 3.3` or `ProDOS` using the `RAMDISK`.
The same effect could be achieved using only the master disk without need to use vanilla system disks or swapping disks between two drives
### DOS 3.3
- insert `RAMDISK` card into slot 7
- insert vanilla `DOS 3.3` to slot 6 disk 1
- boot vanilla `DOS 3.3` using `PR#6` (allowing it to autoboot could boot `RAMDISK` instead)
- insert `Axlon Ramdisk320 DOS System Master` disk into slot 6 disk 1
- `RUN SELECT`, follow on-screen instructions to select source and destination slots, disks. Choose to save the changes
- a new file named `RAMDISK1-6171` will appear. Its suffix corresponds to the options chosen in the previous steps
- move `Axlon Ramdisk320 DOS System Master` to slot 6 disk 2
- insert vanilla `DOS 3.3` to slot 6 disk 1
- reboot using `PR#6` as `SELECT` tool from the previous step can mess up with disk numbers
- `BRUN RAMDISK1-6171,D2` this will copy floppy from slot 6 disk 1 into `RAMDISK` slot 7 disk 1
- remove floppies, reboot Apple II
- Apple II boots `DOS 3.3` located on the `RAMDISK` automatically
### ProDOS
Follow the same steps as in the `DOS 3.3` but use `ProDOS` disk, `SELECTPRO` and `RAMDSKPRO1` tools located on the `DOS 3.3 System Master Version 2.0 with ProDOS files` disk
### CP/M
CP/M manual is provided on the disk file named `MANUAL.DOC`.
Its text is provided in `CPM MANUAL.txt` file in this repo

