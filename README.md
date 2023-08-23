# Acorn A680

Roughly 20 years ago, when I was involved in the Cambridge University
Computer Preservation Society, I was given three
[A680](https://www.computinghistory.org.uk/det/16155/Acorn-A680/) main
boards by an ex-Acorn employee.

![A680 main board](./photos/a680_small.jpg)

I've not done much with them, and thought I should document these
boards a little! The machine itself is pretty well-documented on
[Chris's
Acorns](http://chrisacorns.computinghistory.org.uk/RISCiXComputers.html#A680).

As the A680 was a prototype-only machine, I'm not entirely sure if
these boards ever had a case - they've got a little plastic foot on
the back to allow them to rest on a surface without being mounted in a
case.

They're quite clearly prototype-y - they've all got bodge wires on
them, pretty much everything's socketed, EPROMs for ROMs, etc. One of
them has a little daughterboard to adapt the surface-mount CPU to the
socket, and one of them is missing the CPU. It looks like the internal
model number is 0174 or 0274 (I don't know much about Acorn model
numbers).

Photos [here](./photos)!

## Schematics

Chris's Acorns contains [some
schematics](http://chrisacorns.computinghistory.org.uk/docs/Acorn/Manuals/Acorn_A680TRM_Drawings.zip)
as part of the official documentation, but I managed to get another
copy via a different route:

Back when I first got the boards, I mentioned this to another CUCPS
person, and they told me they could get me schematics, which might be
useful if I were to play about with the boards... and they did! It
appears they came from a different source, because they have different
engineering metadata stuff around the edge of the schematics, which is
kind of interesting, but I suspect the schematics from the above link
will be better for most users.

I have scanned (and reassembled the dodgy photocopying)
[here](./schematics).

## ROMs

The [roms](./roms) directory contains the 4 EPROM images that each
provide a byte of the 32-bit data the CPU needs. The `a680.bin` file
interleaves the ROMs to produce the image seen by the processor. The
ROMs contain little-endian ARM code, which I've done a little
reversing of in Ghidra.

The EPROMs are half-empty, having 256kB of contents in 512kB total
EPROMs (4x128kB). They are labelled "0274,20[0-3]-C BOOT ROM [0-3] (C)
ACORN 1988".

They appear to run the self-tests 1-7 as shown on the hexadecimal
display, only doing the follow-up tests if the NVRAM has a bit set to
run these extra tests.

I have tried to avoid reversing the whole ROM, concentrating on just
the self-diagnostics, but from the strings involved it looks like it
has the following modules:

| Name          | Help             | Version                     |
|---------------|------------------|-----------------------------|
| Acorn ADFS    | ADFS             | 1.02140 DEVELOPMENT VERSION |
| Debugger      | Debugger         | 1.00 (11 Sep 1987)          |
| FileCore      | FileCore         | 1.02117 DEVELOPMENT VERSION |
| FileSwitch    | FileSwitch       | 1.29 (19 Jul 1988)          |
|               | UNIX BOOT system | 0.32 (19 Jul 1988)          |
| UNIXCLI       | UNIX boot CLI    | 0.02 (18 Jul 1988)          |
| UtilityModule | CLI utilities    | 0.32 (19 Jul 1988)          |

If you want to poke around further, you can open up the Ghidra image!
It also looks like some parts of the `OS_CLI` were removed to make
space - the ROM contains "Removed for UNIX version"." I have not dug
further.

I have exported my reversing so far as `a680.bin.gzf`, from Ghidra
10.1.3.

TODO: Cross-checking contents between EPROM sets.

## GALs/PALs

TODO: The board has 16 GALs on it. I have yet to try dumping them, and
don't know if they're protected.

## Resources

 * [Chris's
   Acorns](http://chrisacorns.computinghistory.org.uk/RISCiXComputers.html#A680)
   includes the Technical Reference Manuals, which are pretty good for
   technical details.
 * Data sheets for the Acorn custom ICs are available on
   [BitSavers](http://www.bitsavers.org/pdf/acorn/).
 * The [ARM instruction
   set](https://iitd-plos.github.io/col718/ref/arm-instructionset.pdf)
   was very helpful for understanding the bits of assembly that are
   not well-supported by Ghidra.
 * When trying to reverse the more Acorn-y parts of the ROM,
   [Archimedes Operating System: A Dabhand
   Guide](https://www.pagetable.com/docs/Archimedes%20Operating%20System.pdf)
   by the van Somerens was a helpful introduction, while the [RISC OS
   Programmer's
   Manuals](http://www.riscos.com/support/developers/) provided
   detailed information.
 * [Theo Markettos' Acorn technical
   documents](https://www.chiark.greenend.org.uk/~theom/riscos/techdocs.html)
   gave another view.

## Misc technical details

It looks like the -5V rail is *only* used to pass through the -5V
supply to the backplane.

TODO: Board-powering options?
