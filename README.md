# openwrt-mi424wr-rev-i
OpenWrt snapshot for the Actiontec MI424WR Rev. I

NOTICE: There are 2 models of the Actiontec MI424WR Rev. I. One uses stock firmware versions 4x.xx and the other uses 5x.xx. This build is for the 4x model; it is untested on the 5x model.

To try running this, you're going to need to take the case off of your router and connect to the UART.
The UART pins are populated for you, so no soldering necessary.
The pin closest to the back of the router is ground, with TX next to it, and RX next to that.
Then, enter U-Boot by sending any keystroke over the UART as the router boots.
Next, set up a tftp server, and put `openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage` in its root.
Set a static IP address of 192.168.1.10 on the server.
Finally, connect the tftp server to the router via ethernet, and run `tftpboot openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage; bootm`.
You should then boot into OpenWrt.

Since this router has a native telnet interface, so it may be possible to set U-Boot variables from the telnet prompt, which would eliminate the need to open up the router.
You can also set up usb booting if you don't feel like tftp.
This allows for a persistent enough operating system without potentially bricking the router by flashing to the NAND.
Although if your router does get bricked, it does support kwboot, so all hope is not lost.

To build these images for yourself, follow [this guide](https://openwrt.org/docs/guide-developer/quickstart-build-images), but before you run `make menuconfig`, put `kirkwood-mi424wr.dts` in `target/linux/kirkwood/files/arch/arm/boot/dts/`, and add the following to `target/linux/kirkwood/image/Makefile` where all the other devices are listed:

```make
define Device/actiontec_mi424wr
  DEVICE_VENDOR := Actiontec
  DEVICE_MODEL := MI424WR
  DEVICE_PACKAGES := kmod-ath9k
  SUPPORTED_DEVICES += mi424wr
endef
TARGET_DEVICES += actiontec_mi424wr
```

You should be able to follow the guide from there, and your images will be in `bin/targets/kirkwood/generic`.

KNOWN BUGS:
  * Ethernet doesn't work.
  * Crypto hardware doesn't work.

If you find any other bugs, please let me know.

Note that since this is a snapshot, you will be able to download kmod packages on the day of release only.
I may at some point decide to host a repository of kmods, but for now you're going to have to recompile (or ask me to recompile) if you want extra/newer kmods.

Big, BIG thanks to bodhi at forum.doozan.com.
They did all the real work; all I did was stick the dts in the OpenWrt build system and compile.

## What's the point of running OpenWrt if ethernet doesn't work?

NAS. Fun. Not sure if there are more reasons.

