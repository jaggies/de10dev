# A simple environment for de10-nano development
The Quartus tools are a nightmare for the first-come user.  They have their place, but I find it a lot quicker to development at the Verilog level using an automated build and a syntax-highligting editor like you can find for Eclipse.
## Building
The projects are set up to minimize effort. They currently program the device once after the build completes.

The best way to build is to cd to the individual project and build there. The top-level build can be used to build multiple projects in parallel (jam -jN) 

To build a project, cd to the directory and build it.  It will flash the device if present.
```bash
cd <subfolder>
jam
```
## Notes on board setup
This is currently wired to program the DE10-nano device #2. See Jamrules for details.
## Can't recognize hardware
Try this (add 'sudo'):
```bash
cat > /etc/udev/rules.d/40-usbblaster.rules <<EOD
# DE10-nano
SUBSYSTEM=="usb", \
ATTRS{idVendor}=="09fb", \
ATTRS{idProduct}=="6810", \
GROUP="plugdev", MODE="0666", SYMLINK+="usbblaster"
EOD
sudo udevadm control --reload-rules && udevadm trigger
```
Note the device numbers above can move around, especially if the driver hangs (it'll allocate the next number).
You can see what's recognized with the 'lsusb' command:
```bash
lsusb
...
Bus 003 Device 013: ID 09fb:6010 Altera 
```
The numbers in the above udev rule should match the ones listed above. If they don't, the flash script will fail. You can add additional rules to the above udev file. My rules look like this:
```bash
# DE0-nano (6001) DE10-nano(6010,6810)
SUBSYSTEM=="usb", ATTRS{idVendor}=="09fb", ATTRS{idProduct}=="6010", GROUP="plugdev", MODE="0666", SYMLINK+="usbblaster"
SUBSYSTEM=="usb", ATTRS{idVendor}=="09fb", ATTRS{idProduct}=="6010", GROUP="plugdev", MODE="0666", SYMLINK+="usbblaster"
SUBSYSTEM=="usb", ATTRS{idVendor}=="09fb", ATTRS{idProduct}=="6810", GROUP="plugdev", MODE="0666", SYMLINK+="usbblaster"
```
## List devices
```bash
jtagconfig
```
## Why jam?
Why not?
It's very simple to add rules and build new projects.
```bash
sudo apt-get install jam
```
## Example Jamfile
```jam
SubDir TOP myproject ;
QuartusProject myproject : myproject.qpf ;
QuartusSource myproject : top.v display.v leds.v cpu.v ;
```
Be sure to change all instances of 'myproject' to the new project and directory name you chose.
All spaces are important. Don't forget the one before the ';' tag.


