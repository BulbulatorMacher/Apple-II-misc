# Microsoft Premium SoftCard IIe
## MouseText
Apple II does not use standard ASCII encoding. Values 0x00-0x7F display inverse text, 0x80-0xFF display normal text.
As you can see from the folowing table, uppercase letters appear twice in the mapping.
Applications should use ranges 0x00-0x1F and 0x80-0x9F to display them, but some did not obey that and used the other range.
In 1985 Apple IIe Enhanced replaced the 0x40-0x5F block with graphics called Mousetext.
This broke applications using uppercase inversed letters.
| Apple II | ASCII | Notes |
| - | - | - |
| 0x00-0x1F | 0x40-0x5F | Uppercase letters |
| 0x20-0x3F | 0x20-0x3F | Digits and symbols |
| 0x40-0x5F | 0x40-0x5F | Uppercase letters |
| 0x60-0x7F | 0x60-0x7F | Lowercase letters |
## Fixing MouseText
Display routine is located at 0xF52 in the .dsk (0x1152 in the RAM) and is executed by the 6505.
For the patch, a block of memory has been identified which is loaded from disk (offset 0x10A0 in the .dsk) to the ram (at address 0x12A0) but remain unused and filled with 0x00.
```
; original routing starts with Apple II character in the accumulator, first statements are: tax, lda $78
; these were replaced with jump to the patch: JMP $12A0

0xAA           tax       ; backup accumulator
0x29 0x60      and #$60  ; check if accumulator is in range 0x40-0x5F (or 0xC0-0xDF)
0xC9 0x40      cmp #$40

0xD0 0x04      bne       ; not in range, go to the ending statements

0x8A           txa       ; shift range to the 0x00-0x1F (or 0x80-0x9F)
0x29 0x9F      and #$9F
0xAA           tax

0xA5 0x78      lda $78   ; last instruction of the original routine
0x4C 0x55 0x11 jmp $1155 ; go back to the original routine
```
## Keyclick
Each time a key is pressed, software produces audible speaker click by calling `2C 30 C0` (`BIT $C030`) twice.
This code is located at offsets `0x156` and `0x164` in the `.dsk` file.
To disable the keyclick, change both occurences to `2C 00 C0` - it will access the keyboard instead of the speaker, without side effects.
## Boot screen version text
Boot screen text is located ad offset `0x2240` in the `.dsk` file.
It is encoded in a plain ASCII text. Default version is `2.26B`