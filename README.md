# openwrt-mi424wr-rev-i
OpenWrt snapshot for the Actiontec MI424WR Rev. I

To try running this, you're going to need to take the case off of your router and connect to the UART.
The UART pins are populated for you, so no soldering necessary.
The pin closest to the back of the router is ground, with TX next to it, and RX next to that.
Then, enter U-Boot by sending any keystroke over the UART as the router boots.
Next, set up a tftp server, and put `openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage` in its root.
Set a static IP address of 192.168.1.10 on the computer running the server.
Finally, connect the tftp server to the router via ethernet, and run `tftpboot openwrt-kirkwood-actiontec_mi424wr-initramfs-uImage; bootm`.
You should then boot into OpenWrt.

I haven't tried flashing to the NAND, but the other images should be sufficient for giving that a try, if you're feeling lucky.
Since this router has a native telnet interface, you may have some success flashing this to the nand through something like `mtd`.
I have not tried doing that, so if you have success with it, let me know.
There could also be a way to set U-Boot variables from the telnet prompt, which would eliminate the need to open up the router.
You can also set up usb booting if you don't feel like doing tftp.

To build these images for yourself, follow [this guide](https://openwrt.org/docs/guide-developer/quickstart-build-images), but before you run `make menuconfig`, put `kirkwood-mi424wr.dts` in `target/linux/kirkwood/files-5.4/arch/arm/boot/dts/`, and add the following to `target/linux/kirkwood/image/Makefile` where all the other devices are listed:

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
  * Crypto hardware doesn't work, resulting in warnings about random number generation.

If you find any other bugs, please let me know.

Note that since this is a snapshot, you will be able to download kmod packages on the day of release only.
I may at some point decide to host a repository of kmods, but for now you're going to have to recompile (or ask me to recompile) if you want extra/newer kmods.

Big, BIG thanks to bodhi at forum.doozan.com.
They did all the real work; all I did was stick the dts in the OpenWrt build system and compile.
