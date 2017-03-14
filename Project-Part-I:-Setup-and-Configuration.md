## Instructions
1. Figure out which USB device is the board by looking at the windows device manager while plugging it in
2. Capture that USB port using virtualbox ([more information](https://www.virtualbox.org/manual/ch03.html#idm1631))
3. Figure out which USB device inside the virtualbox using `dmesg -w` in the terminal and capturing/uncapturing the USB device in virtualbox
4. Install avrdude on the virtualbox: `sudo apt install avrdude`
5. Check if AVRDude can connect to the board using: `avrdude -c arduino -p m328p -P /dev/ttyUSB_DEVICE -b 115200 -vv`

## Questions, Issues and Problems

> It works well (if I edit permissions) : "avrdude: AVR device initialized and ready to accept instructions" It is connected to port dev/ttyUSB0. I just wanted to know if you have a command for me to safely remove it? Or do I just unplug it without caring? 

1. For the permissions, the correct way to do this in Linux is to add your user to the `dialout` group, you will need to log-out and log-in afterwards for it to take effect.
2. You can just unplug it, un-mounting is only required when the USB device has a file system (like a USB stick or hard drive)

