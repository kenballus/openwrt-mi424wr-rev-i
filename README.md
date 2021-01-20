# openwrt-mi424wr-rev-i
OpenWrt snapshot for the Actiontec MI424WR Rev. I

To try running this, you're going to need to take the case off of your router and connect to the UART.
Then, get into U-Boot by sending any keystroke over the UART as the router boots.
Next, set up a tftp server on your computer, and put `openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage` in its root.
Set a static IP address of 192.168.1.10 on that tftp server.
Finally, you can connect your computer to the router via ethernet, and run `tftpboot openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage; bootm`.
You should then boot into OpenWrt.

I haven't tried flashing to the NAND, but the other images should be sufficient for giving that a try, if you're feeling lucky.
Since this router has a native telnet interface, you may have some success flashing this to the nand through something like `mtd`.
I have not tried doing that, so if you have success with it, let me know. There could also be a way to set U-Boot variables from the telnet prompt, which would eliminate the need to open up the router.

To build these images for yourself, follow [this guide](https://openwrt.org/docs/guide-developer/quickstart-build-images), but before you run `make menuconfig`, put `kirkwood-mi424wr.dts` in `target/linux/kirkwood/files-5.4/arch/arm/boot/dts/`, and add the following to `target/linux/kirkwood/image/Makefile` just below the last target (which is the ZyXEL NSA325, for now):

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
  * Rebooting doesn't work.
  
Both of these seem to be issues with `kirkwood-mi424wr.dts`.
If it's clear to you what those issues are, please let me know.
If you find any other bugs, please let me know as well.

Note that since this is a snapshot, you will be able to download kmod packages on the day of release only.
I may at some point decide to host a repository of kmods, but for now you're going to have to recompile (or ask me to recompile) if you want extra/newer kmods.

Big, BIG thanks to bodhi at forum.doozan.com.
They did all the real work; all I did was stick the dts in the OpenWrt build system and compile.
