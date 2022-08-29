# Klipper KP3S

![alt text](https://github.com/Upcycle-Electronics/Klipper-KingRoon-Printers/blob/main/pic/banner.png?raw=true)

<details><summary> *Meta </summary>

- Authors: Georgiy N., Jake L.
- Last Modified: August 2022
- User OS: Linux, macOS, Windows
- Raspberry Pi Imager V1.7.2
- Mainsail OS v0.7.0 (Release: 2022-07-29)
- Tested with: Kingroon KP3S 3.0 (PCB: V1.3 w/GD32F303-VET6)

</details>

### Hardware

Minimum recommended hardware: Raspberry Pi 2, 3, 4, Zero 2, Orange Pi Zero 2, or Makerbase MKS PI

### Operating system

This guide is for Mainsail OS. There are other guides online for FluiddPi. As of August 2022, the FluiddPi project is not actively developed and its future is unknown to this author.

### SD card imaging

Install Raspberry Pi Imager software for Linux (Flatpak, distro, [direct), Windows, or macOS](https://www.raspberrypi.com/software/)

## Source

The "My Guide" section below is a summary of information contained in the [Mainsail OS manual available here](https://docs.mainsail.xyz/setup/mainsailos/pi-imager). Refer to this manual for more up to date information and a better illustrated guide.  When the Mainsail OS manual ends and refers you to the Klipper documentation, return here to the section below titled Overview. The steps I used to create a Mainsail OS image along with the steps I took to configure a working system are still described below. This is intended to help diagnose any issues that may arise over time as the software improves and this guide ages.



<details>
<summary>My Guide</summary>

### Install Mainsail OS

Open the Raspberry Pi Imager and select "Choose OS." In the drop down menu scroll down and choose "Other specific-purpose OS" then "3D printing" then "Mainsail OS." Select your SD card under "Choose Storage" then stop and read the next step. Do not write yet.

### Setup OS options before creating SD card image

First click on the gear wheel on the lower right of the Raspberry Pi Imager. This option is only available after selecting an OS image.

In the advanced options, it is a good idea to change the hostname to something unique that you will remember and understand. Write down the name you enter, you will need this later.

You must enable SSH. Securing SSH is a complicated subject and is beyond the scope of this article. Using a password for SSH is a bad idea. This is the type of thing that will get attacked online by both people and bots. Password crackers can break most passwords a user will imagine in just a few seconds. It is better to use a public/private ssh-key instead of a password. You will need to look up how to create and use ssh-keys for your operating system.

On Fedora and other Linux distros, open a terminal and enter the command ```$ ssh-keygen -t rsa```. It is not a bad idea to enter a password when prompted at this point and write it down. This will create two hidden dot files with the public/private keys saved in your home directory. Open the public key you just created using a text editor. Copy and paste the entire text contents into the "Set authorized_keys for 'pi'" section with the "Allow public-key authentication only" selected.

Next, this is important, note that there is a plan in Mainsail OS to change this in the future, but as of this writing, DO NOT change the default username. It must be left as "pi." You should change the default password (and write it down). If you change the username to anything other than "pi" the web server interface on the Pi will not generate and you will need advanced Linux admin knowledge to get the image working, otherwise you will need to start over with a fresh image on the SD card with the default user name.

If you want to connect your machine to WiFi, set the SSID and password here along with any locale settings you would like to change, then save and select "Write"

### Power the Pi

Insert your SD card and power the Pi. If your hardware supports a monitor you can plug one in to watch the boot cycle and see when the device is ready. This Mainsail OS image is not equip with a default graphical user interface so the only reason to connect a monitor is to see when the boot is complete. Do not get out a keyboard, mouse, and monitor thinking this will be used like a desktop. The only way to access this OS on the Pi is either through the web page served by the Pi on your local network or by connecting to the command line terminal via SSH. If you do not want to plug in a monitor or can't, note that the first boot cycle takes a few minutes. Just wait for 5-10 minutes and it will be done. It should only take a couple minutes, but this depends on the hardware speed and the size of the SD card. Larger cards will take longer to initiate and expand the file system for the first time.

### Network connection

Your computer must be on the same network as the Pi. The easiest way to ensure that both machines can connect is if they are both connected to the same router or network switch with ethernet cables. They must go through a switch/router. Don't directly connect them, nothing will happen. If someone in your house has created a custom network setup, ask them how to connect your devices where they can communicate directly across the network. If you have a simple router that came with your internet service, likely almost any device can freely connect to any other device on your network.

The next step is to login to the web server hosted on the Pi from your computer. We need to update the system from within the Mainsail web interface. Open a web browser and enter the hostname you created in the advanced settings of the Raspberry Pi Imager before writing the SD card. Add ".local" to the end of your hostname. If you are lucky the Mainsail OS page will load and you can skip to the next section.

If nothing loads, make sure that your browser allows connections to "http://" addresses. You may need to turn off browser settings/add-ons that force 'HTTPS Everywhere' or create an exception for your "(your-hostname).local." If you are on a Linux machine, open a terminal and use the command ```$ ping -c 1 (your-hostname).local``` This should give you the IP address of the Pi. Last method if you are having trouble finding the IP address of the Pi you will need to log into your router and find the IP address that it assigned to the Pi. This will be a number like "192.168.0.123." Enter the IP address of your Pi into the address bar of your computer's browser and the Mainsail page should load.

### Update Mainsail

Now that you have the Mainsail interface connected, go to the menu bar on the left side of the page that loaded and click on the "(wrench image) Machine" tab. On the right side of the page you should see a section called "Update Manager" refresh this and update everything here. It is important to update the system from the web server page and not with the command ```$ apt-get``` once logged into SSH.

#### Updated Metadata for this article

- Klipper: v0.10.0-557
- Crowsnest: v3.0.1
- Moonraker: v0.7.1-651
- Sonar: v0.0.2
- MainSail: v2.2.1

### Login with SSH

Now use the terminal command (Windows/macOS: Putty) ```$ ssh pi@(your-Pi's-IP-address-goes-here)``` so like ```$ ssh pi@192.168.0.123```. If you added a password to your ssh-key, you will be asked to enter this first. If asked to fingerprint the connection select yes. Then you will be prompted to enter the password you added to the advanced settings of the Raspberry Pi Imager that went with the default username "pi." Now you should be logged into the Pi.

</details>



### Overview

At this point you are connected with a web server interface, with SSH, and the Pi is running updated Mainsail OS. What you don't have: Mainsail has no configuration telling it what printer you want to connect, and your printer does not have the Klipper firmware it needs to communicate with Mainsail. 

The firmware you will need for the printer is something we will generate next using Klipper on the Pi to create the file. The control board for the printer has a bootloader preinstalled. The bootloader automatically searches any SD card that is loaded before the machine is turned on, and looks for a ".bin" file. If the SD card contains a binary file, the printer will assume this is firmware to update and will overwrite the old firmware with the new firmware file. It is important that the correct file is created and added.

### Firmware build

Using your SSH terminal type the command ```$ cd ~/klipper``` to navigate to the klipper folder. Next we are going to call a script that initiates a simulated configuration window for options needed for firmware. Type the command ```$ make menuconfig```. At this point you need to know what version of microcontroller you have on your control board and match it to the label/image below with all of the settings shown.

***STM32F103***

![alt text](https://github.com/Upcycle-Electronics/Klipper-KingRoon-Printers/blob/main/pic/stm32.png?raw=true)

***GD32F103 / F303***

![alt text](https://github.com/Upcycle-Electronics/Klipper-KingRoon-Printers/blob/main/pic/GD32.jpg?raw=true)

After you are done with these options hit "Q" to save and quit this menu. In the terminal type ```$ make``` to start the series of scripts that will create an incomplete version of printer firmware. This isn't quite done yet. There is a python script that needs to make some changes to the file. Run the following to modify the binary with LCD screen enabled ```$ ./scripts/update_mks_robin.py out/klipper.bin out/Robin_nano.bin```. Alternatively, if you want to disable the LCD where you can remove the LCD board/cable run the following to modify the binary ```$ ./scripts/update_mks_robin.py out/klipper.bin out/Robin_nano43.bin```. Finally you need to make the firmware binary avaialble through the web server interface using the command ```$ cp out/Robin_nano.bin ~/mainsail/```

### Flash printer firmware

Now use your web browser to visit ```“http://your-hostname.local/Robin_nano.bin”``` You will get a bin file download. Put it into a SD card. You are ready to flash the Klipper firmware to the printer. With the printer off, place the SD card into the printer's card slot, and then power the printer on. It should immediately flash the firmware. There is a status bar that comes up on screen while it is updating but disappears as soon as the job is complete. This is very fast and easy to miss.

### Connect the printer to the Pi

Connect the printer with the Pi using a USB cable. Power the printer, then go to the SSH terminal and enter the command ```$ ls /dev/serial/by-id/*``` Copy the output and paste it into a note or somewhere you will have access for use in the main printer configuration file in the next step.

### Printer configuration files

If you are running the board without modifying the stepper driver configuration, then copy the files listed in the "Basic configuration" section below. These files are hosted in this github repo for your printer. Download, then add them to the list in the Mainsail webpage server under the left panel Machine tab > Config Files. If you do not plan to do any modifications in the future, simply use the upload tool in the Config Files window to add what is needed. 

Alternatively, create a new folder in Config Files named something like "KP3S-Orig." Now add all of the files listed here to this folder. After you have all of these files in the folder, go back to the main section by clicking on the "(Folder icon) .." Now add a new file called "printer.cfg" and add the following line to it ```[include KP3S-Orig/printer.cfg]``` save and close this file. Now, if you want to test configuration changes or find someone else's configuration you want to try, all you need to do is put the new config in its own folder, comment out the line you added to printer.cfg on the main page and create a reference to the new folder instead.

Now in the Mainsail web interface open the printer.cfg file you got from this github repo. Navigate to the ```[MCU]``` section and look for the line ```Serial:``` Use the output you copied earlier at the end of the "Connect the printer to the Pi" section in place of what is already in the file. Then save and close this file. You are now done with installation.

### Basic configuration

```bash
 -printer.cfg
 -stepper.cfg
 -extruder.cfg
 -bed.cfg
 -fan.cfg
 -thermistor.cfg
 -macros
    macros.cfg
    printing.cfg
```

### Modifed stepper drivers for UART mode (soldering required)

The TMC2225 datasheet has a box at the top of page 16 that says, "The UART line must be logic high during idle state..." The KP3S does not ship with stepper motor controllers' UART mode enabled. There is a 20k resistor (marked "30C") for the TMC2225 pin 17 (PDN_UART) that is connected to ground. This resistor must be removed to enable UART mode. The picture below highlights the resistor for each driver. There is a solder jumper pad near each of the drivers that needs to be bridged (aka apply a blob of solder or low value SMD resistor connecting both pads) to enable UART mode. The following is a list of each driver, the pin number of the micro controller and the port number for that pin.

- X Axis = p30 / PA5
- Y Axis = p7 / PC13
- Z Axis = p64 / PC7
- E0 Axis = p68 / PA9
- E1 Axis = p67 / PA8



![alt text](https://github.com/Upcycle-Electronics/Klipper-KingRoon-Printers/blob/main/pic/KP3S-PCB-UART-mod.jpg?raw=true)

### Advanced configuration for UART mod

```bash
 -printer.cfg
 -stepper.cfg
 -bltouch.cfg
 -tmc.cfg
 -extruder.cfg
 -bed.cfg
 -fan.cfg
 -thermistor.cfg
 -macros
    macros.cfg
    printing.cfg
```

### Other possible mods not yet emplimented

The hotend fan could be moved from the JST connectors with permanent 24V power to the HE1 screw terminals. This could enable the fan to be turned on only when the hotend is hot or a print is running. It could also enable tuning the fan speed with PWM and perhaps run the fan quieter.



If you have more questions the main source of information is here: https://github.com/nehilo
