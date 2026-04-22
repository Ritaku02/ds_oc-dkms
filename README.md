# Introduction

# ds_oc-dkms

Kernel module for overclocking the Sony DualSense controller on XHCI (USB 3.0 or higher) controllers forked from [Jiang-lai/ds-oc-kmod](https://github.com/Jiang-lai/ds-oc-kmod) and modified to install through makepkg and dkms.

## Building

The package is configured for install through makepkg on Arch Linux and derivatives. Download either release, extract the contents to a folder of your choosing, open a terminal inside the extracted `ds_oc-dkms` folder, and run `makepkg -i` to build and install the kernel module. Afterwards, restart your computer, and the module should be loaded. A good tool to test if the module is working can be installed from the AUR with your favorite AUR helper; [gamepadla-polling](https://aur.archlinux.org/packages/gamepadla-polling). During the test, the polling rate should mostly read 1ms, though it may vary slightly at times.

Thanks to dkms, the module should be automatically rebuilt every time your kernel updates, so there should be no reason to rebuild it manually for new kernel versions.

## Changing the polling rate

Polling rate is set according to the `bInterval` value in the USB endpoint descriptor. The value sets the polling rate in milliseconds, for example: an interval value of 4 equals 250 Hz.

You can attempt to change the rate by using the kernel parameter `ds_oc.rate=n` (if installed) or going into `/sys/module/ds_oc/parameters` and using `echo n > rate` (replace `n` with the value you desire) to change the value. However, changing the polling rate may not take effect. In that case, you would need to edit ds_oc.c, and near the top of the file, change the value of `configured_interval` to the value you desire, then run `makepkg -i` again.
