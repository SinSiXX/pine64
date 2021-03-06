Binaries
========

---

**NOTE**

These binaries here are no secret blobs, but are just here provided for
convenience. You can always rebuild them, as described below.

---

This directory here provides built versions of some firmware components,
because:

- You might run into issues when some binaries require certain toolchains.
ATF for instance is known for some peculiarities.
- You just can't be bothered with cloning and building just another piece
of software.
- You might want to troubleshoot a firmware build, and want to isolate the
culprit by replacing some components with known good builds.
- Some binaries are not fully mainlined (FEL capable SPL builds).

ARM Trusted Firmware builds
---------------------------
- `bl31-a64-h5.bin`: default debug build of the ARM Trusted Firmware binary
for boards with an Allwinner A64 or H5 SoC. This is from the official
mainline ATF branch.
- `bl31-h6.bin`: default debug build of the ARM Trusted Firmware binary
for boards with an Allwinner H6 SoC. This is from the official mainline
ATF branch.

Files with a "-legacy" prefix are based on the outdated Allwinner based branch
and are just provided for the sake of completeness.

To rebuild those binaries, fetch the master branch from the official
[ATF](https://github.com/ARM-software/arm-trusted-firmware) repository:

    $ export CROSS_COMPILE=aarch64-linux-
    $ make PLAT=sun50i_a64 DEBUG=1 bl31

The resulting binary is `build/sun50i_a64/debug/bl31.bin`. For a non-debug
build (which is smaller and does not generate any output on the console), use
`DEBUG=0` in the command line above and use the binary at
`build/sun50i_a64/release/bl31.bin`.
This build covers H5 SoCs as well. For the H6, simply use the "sun50i_h6" build
target and copy the file from the respective build directory.

FEL capable SPL builds
----------------------
- `sunxi-a64-spl32-ddr3.bin`: 32bit build of a mainline based U-Boot SPL. Can be
used for FEL booting. This version is for boards with the Allwinner A64 SoC
and DDR3 DRAM (*not* LPDDR3), like the original Pine64 or the BananaPi M64.
- `sunxi-a64-spl32-lpddr3.bin`: 32bit build of a mainline based U-Boot SPL.
Can be used for FEL booting. This version is for boards with the Allwinner A64
SoC and LPDDR3 DRAM, like the SoPine or the Pinebook.
- `sunxi-h5-spl32-ddr3.bin`: 32bit build of a mainline based U-Boot SPL.
Can be used for FEL booting. This version is for boards with the Allwinner H5
SoC and DDR3 DRAM, like the OrangePi PC2 or the NanoPi Neo 2.
- `sunxi-h6-spl32-lpddr3.bin`: 32bit build of a mainline based U-Boot SPL.
Can be used for FEL booting. This version is for boards with the Allwinner H6
SoC and LPDDR3 DRAM, like the OrangePi One Plus or the Pine H64.

To FEL boot a board, connect your host computer to the OTG port of a board.
On the Pine64 you need a special USB-A <-> USB-A cable connected to the upper
USB port. Then use the sunxi-fel program from the sunxi-tools [1] repository:

    $ sunxi-fel -v spl sunxi-a64-spl32-ddr3.bin

After that you can load further firmware components into DRAM, if required:

    $ sunxi-fel -v -p write 0x4a000000 u-boot.bin write 0x44000 bl31-a64-h5.bin reset64 0x44000

To rebuild those binaries, fetch the sunxi64-fel32 branch from
[this U-Boot](https://github.com/apritzel/u-boot/commits/sunxi64-fel32)
repository:

    $ git checkout sunxi64-fel32
    $ export CROSS_COMPILE=arm-linux-gnueabihf-
    $ make sun50i-a64-ddr3-spl_defconfig
    $ make

You can pick up the SPL binary by just copying spl/sunxi-spl.bin.
Alternative defconfigs provided are:
- `sun50i-a64-ddr3-spl_defconfig`: A64 with DDR3 DRAM (Pine64, BananaPi M64)
- `sun50i-a64-lpddr3-spl_defconfig`: A64 with LPDDR3 DRAM (Pine64 LTS, Pinebook)
- `sun50i-h5-ddr3-spl_defconfig`: H5 with DDR3 DRAM (OrangePi PC2)
- `sun50i-h6-lpddr3-spl_defconfig`: H6 with LPDDR3 DRAM (Pine H64)
