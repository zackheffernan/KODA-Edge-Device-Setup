# KODA-Edge-Device-Setup
A full walkthrough of the steps needed to setup a Raspberry Pi-based edge device on your local network using a Samba fileshare. This guide will walk you through a "headless" setup, meaning you won't need a monitor, keyboard or mouse. Instead we'll be setting up Samba on the Pi from a laptop or desktop you aready have, by using SSH to do everything remotely.

## What you'll need:
- Raspberry Pi w/ WiFi (Raspberry Pi 3A+, 3B, 3B+, 4B, or Zero W).
- MicroSD card with at least 16GB of storage.
- USB MicroSD card adapter.
- USB C to USB A cable.

## Preparing your desktop
1. You'll need the latest version of Raspberry Pi's default operating system, Raspian. To get it, download the [Raspberry Pi Imager](https://www.raspberrypi.org/software/), which we'll use to combine the download, install, and disk imaging process later.

2. If you're using Windows you'll need to install [PuTTY](https://www.puttygen.com/download.php?val=4) This is what we'll be using to connect and give commands to the Pi via SSH.

3. Windows Only: Install [Bonjour](https://support.apple.com/kb/DL999?locale=en_US) from Apple. This will help us identify the Raspberry Pi on our local network later. Windows doesn't recognise the Pi's address by default, but MacOS does. Installing Bonjour Print Services is the lightest way of tricking Windows to recognize the Pi's network address.

That's all you'll need to install on your desktop!

### Installing Raspbian on the Raspberry Pi
1. Grab your microSD card and plug it into your computer, either using a USB SD card reader or directly into the laptop if you have the port for it. 

2. Next, we'll install the Raspbian OS disk image onto our SD card. Doing this erases and reformats the card, so make sure you've transferred any important files off of it first.
 
   Open up Raspberry Pi Imager and select **"Raspberry Pi OS LITE (32-bit)"**. This is the "headless" version of Rasbian.
   
   Next, select the SD card.
   
   Finally, select "Write" and then confirm it. Flashing the SD card with Raspbian should only take a couple of minutes. 
   
   After it's finished, **leave the SD card in!** We'll need to edit some files on it during the next step.
    
   ![3](https://user-images.githubusercontent.com/36873627/129684528-95200e20-91dc-42e6-af24-a90da204333d.JPG)


## Connecting the Raspberry Pi to your local network
1. Your SD card should now be called "boot". If you can't see it, a simple fix is to pull it out and plug it back in again.

2. You'll need to enable SSH access to the Pi. To do this, open up any text-editor (I used Notepad), create a new file, and save it as "**ssh**". This file doesn't need to contain anything - on boot, Raspbian will just check if it exists.

    Make sure the "**ssh**" file you just created doesn't have a file extension. If it saved as a .txt file, you can simply rename it       and delete the suffix.
    
    Save and close the file.
    
    ![4](https://user-images.githubusercontent.com/36873627/129679894-8d7a3b16-7848-4e9a-b41d-a4439b3d22be.JPG)
  
3. Next, create another new file with Notepad. Paste in the following code, replacing the network details with your own (without the quotation marks):

      ```
      country=AU
      ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
      update_config=1

      network={
          ssid="NETWORK-NAME"
          psk="NETWORK-PASSWORD"
      }
      ```

   Save the file as "**wpa_supplicant.conf**". In this case, .conf should be the only extension. Close the file.
   
   ![5](https://user-images.githubusercontent.com/36873627/129681029-3530d1b8-3656-4e56-8a48-15f64e073471.JPG)

4. Eject the microSD card safely by right-clicking on it in File Explorer and selecting the **eject** option. It's now safe to remove    from your PC.

Congrats! The network setup for your Raspberry Pi is done!

## Power up and boot your Raspberry Pi
1. Slot the microSD card into the port on the bottom of the Pi, and plug in a USB-C cable to power it on. 

   A red light indicates power, and a green light means it's reading/writing from the SD card. A flickering green light is a good        sign that the Pi is booting up for the first time.
   
   Wait until the green light has a consistent, short blink before continuing to the next step. This means that bootup is complete.

## Connect to the Raspberry Pi via SSH.
Before we start this section, make sure your laptop/desktop is connected to the same WiFi network you just configured the Pi for.
For future reference, the default username and password for the Raspberry Pi is **pi** and **raspberry**, respectively.

1. If you've following the steps up until now, SSH should be enabled on your Pi. To test if it's connected to the WiFi network, run PuTTY, and enter the following:
   - Set the **Host Name** to **raspberrypi.local**.
   - The default port should be **22**.
   - 
   
