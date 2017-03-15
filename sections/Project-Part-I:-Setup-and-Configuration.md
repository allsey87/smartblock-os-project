## Instructions
1. Figure out which USB device is the board by looking at the windows device manager while plugging it in
2. Capture that USB port using virtualbox ([more information](https://www.virtualbox.org/manual/ch03.html#idm1631))
3. Figure out which USB device inside the virtualbox using `dmesg -w` in the terminal and capturing/uncapturing the USB device in virtualbox
4. Install avrdude on the virtualbox: `sudo apt install avrdude`
5. Check if AVRDude can connect to the board using: `avrdude -c arduino -p m328p -P /dev/ttyUSB_DEVICE -b 115200 -vv`

