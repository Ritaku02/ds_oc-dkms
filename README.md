# ds_oc-dkms

Kernel module for overclocking the Sony DualSense controller on XHCI (USB 3.0 or higher) controllers, forked from [Jiang-lai/ds-oc-kmod](https://github.com/Jiang-lai/ds-oc-kmod) and modified to install through makepkg and dkms.

## Building

The package is configured for install through makepkg on Arch Linux and derivatives. Download either release, extract the contents to a folder of your choosing, open a terminal inside the extracted `ds_oc-dkms` folder, and run `makepkg -i` to build and install the kernel module. Afterwards, restart your computer, and the module should be loaded. A good tool to test if the module is working can be installed from the AUR with your favorite AUR helper; [gamepadla-polling](https://aur.archlinux.org/packages/gamepadla-polling). However, it seems to hit a limit at 1ms (correct me if I'm wrong), so if you have a WebHID-enabled browser (I recommend Ungoogled Chromium), another test you can use is at https://controllertest.io/latency-test

Thanks to dkms, the module should be automatically rebuilt every time your kernel updates, so there should be no reason to rebuild it manually for new kernel versions.

## Changing the polling rate
## UPDATE ##
I discovered why the below instructions "weren't working for me". They actually were, it's just the DualSense with an up-to-date firmware handles polling rate in an odd way to ochieve a theroetical '8000Hz'. I switched from the Gamepadla polling rate tester to an online WebHID tester, and found that when the DualSense is given an interval of 1ms, it actually switches to an interval of 0.125ms, or 8000Hz. While sounding good on paper, I found that Steam Proton (at least on my system) seems to have issues with handling such a high polling rate, causing games to either run slow or have high input lag. Also, while this makes it seem like the DualSense is dividing the configured interval by 8, it's somehow more complicated than that. Here's what I found;

- configured interval = 1; actual DualSense interval = 0.125 (1/8)
- configured interval = 2; actual DualSense interval = 0.250 (1/8)
- configured interval = 4; actual DualSense interval = 1ms (1/4)
- configured interval = 6; actual DualSense interval = 4ms (6/1.5) (DualSense default over USB)
- configured interval = 8; actual DualSense interval = 16ms (x2) (also, wut? O_o)

Polling rate is set according to the `bInterval` value in the USB endpoint descriptor. Normally, the value sets the polling rate in milliseconds, but as established above, the DualSense handles it quite differently.

You can by using the kernel parameter `ds_oc.rate=n` (if installed) or going into `/sys/module/ds_oc/parameters` and using `echo n > rate` (replace `n` with the value you desire) to change the value. Just follow the above list to find the configured interval value for the actual interval you desire.
