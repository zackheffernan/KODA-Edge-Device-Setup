# KODA-Edge-Device-Setup
A full walkthrough of the steps needed to setup a Raspberry Pi-based edge device on your local network using a Samba fileshare. This guide will walk you through a "headless" setup, meaning you won't need a monitor, keyboard or mouse. Instead we'll be setting up Samba on the Pi from a laptop or desktop you aready have, by using SSH to do everything remotely.

We'll be installing a variant of Raspbian - Pi's default OS - called Nymea. This includes all of the features of regular Rasbian, but adds the ability to configure new WiFi networks using Bluetooth from your phone. This saves time and means you don't have to mess with the configuration files manually.

## What you'll need:
- Raspberry Pi w/ WiFi (Raspberry Pi 3A+, 3B, 3B+, 4B, or Zero W).
- MicroSD card with at least 16GB of storage.
- USB MicroSD card adapter.
- USB C to USB A cable.
- Smartphone (either Android or iOS)

## Preparing your desktop
1. You'll need the latest version of Nymea's disk image. To get it, click [here](https://downloads.nymea.io/images/raspberrypi/latest). Save and extract the .zip file somewhere easy to access, like your desktop.

2. You'll also need a software that can reformat and flash the disk image onto the microSD card. [Etcher](https://www.balena.io/etcher/) is an open-source, lightweight way to do this.

3. If you're using Windows you'll need to install [PuTTY](https://www.puttygen.com/download.php?val=4). This is what we'll be using to connect and give commands to the Pi via SSH.

4. While you're at it, download the **BerryLAN** app on your smartphone, from either the App Store or Google Play Store. This is what we'll be using to connect via Bluetooth to the RaspberryPi and change it's network configuration.

That's all you'll need to install!

## Preparing your desktop

### Installing Raspbian on the Raspberry Pi
1. Grab your microSD card and plug it into your computer, either using a USB SD card reader or directly into the laptop if you have the port for it. 

2. Next, we'll install the Nymea image onto our SD card. Doing this erases and reformats the card, so make sure you've transferred any important files off of it first.
 
   Open up Etcher and select the disk image file you download earlier. The file extension is **.iso**.
   
   Next, select the SD card.
   
   Finally, select "Flash" and confirm it. Flashing the SD card with Nymea should only take a few minutes.
      - If you get a "Flash Failed" message, try running Etcher as administrator, or skipping the validation step.
   
    
   ![3](https://user-images.githubusercontent.com/36873627/129815716-d4207c58-f2ac-4ffa-b717-2c27a511dcc5.JPG)


   You can now safely eject the microSD card.

## Connecting the Raspberry Pi to your local network
1. Slot the microSD card into the port on the underside of the Raspberry Pi. Connect your Pi to power using the USC-C to USB-A cable. It should be fine drawing power from your laptop or desktop's USB ports while we set it up.

   *Let the Pi boot for a minute or two. A solid red LED means it has power, and a flickering green LED means it's reading/writing to     microSD card. Let it do it's thing until the green LED stops flashing, which is a good indicator that the boot sequence has finished.*

   *At this point, the Raspberry Pi isn't going to connect to the network because it doesn't know our WiFi name or password. This is where Nymea and BerryLAN come in. In the Nymea version of Rasbian, when the Pi can't find a network to connect to it will automatically begin listening for a Bluetooth connection from the BerryLAN app on your phone.*

2. Open the BerryLAN app. You should see one device called **BT WLAN**. This is the Raspberry Pi.

   ![6](https://user-images.githubusercontent.com/36873627/129817256-2f8bf219-9b45-4da4-b52d-1dd39bce300a.png)

3. Click on the device and follow the prompts to connect it to your WiFi network. After it's finished, BerryLAN will show you the IP address of the Raspberry Pi. **Write this down for later**. *(Your IP address will be different to the one in the image below)*.

   ![7](https://user-images.githubusercontent.com/36873627/129817396-94af6f1e-399d-4c2b-9a71-d572eea154bb.png)

Congrats! The network setup for your Raspberry Pi is done! The Nymea and BerryLAN method is far simpler than digging around in the Raspberry Pi's configuration files.

## Connect to the Raspberry Pi via SSH.
Before we start this section, make sure your laptop/desktop is connected to the same WiFi network you just configured the Pi for.
For future reference, the default username and password for the Nymea version of Raspbian is **nymea** and **nymea**. Creative.

1. If you've following the steps up until now, SSH should be enabled on your Pi. To test if it's connected to the WiFi network, run PuTTY, and enter the following:
   - Set the **Host Name** to the **IP address** that BerryLAN gave us.
   - The default port should be **22**.
   - The connection type should be **SSH**.

   ![Putty](https://user-images.githubusercontent.com/36873627/129817991-5b2e05cb-c884-4144-b760-38d6b37f5743.JPG)


2. Once you've clicked through some warnings about authenticity, you should see the screen below. Enter the default login and 
   password (**nymea** and **nymea**), and you're done!
   
   *You can change the default Raspberry Pi password using the ```passwd``` command*.
   
   ![nymea](https://user-images.githubusercontent.com/36873627/129818213-181cc7db-443a-4ad0-8bfc-fad7b1412ad1.png)


## Installing Samba

We'll be using the PuTTY terminal to send commands to the Raspberry Pi via SSH (Secure SHell). You can copy and paste the code snippets below directly into the terminal.

1. The first step is making sure that all our default Raspberry Pi packages are up to date. We can run the following two commands back-to-back to do this.

   ```sudo apt-get update```
   ```sudo apt-get upgrade```
   
2. Now that everyhting's updated, we can install the Samba packages.

   ```sudo apt-get install samba samba-common-bin```

3. Before we set up network storage, we'll need a folder to share on the network. Here, we'll create a folder called **shared** inside the default user **nymea**'s home directory.

   ```mkdir /home/nymea/shared```
   
4. Next, we need to modify the Samba config file to define permissions for our new shared folder. Type in the following command, and the config file should look like this:

   ```sudo nano /etc/samba/smb.conf```

   ![Capture](https://user-images.githubusercontent.com/36873627/129819802-c03f1ac1-3bbd-4118-9d2e-278e39d92215.PNG)

   Scroll way down to the bottom of the config file, and add the following lines:
   
   ```
   #===============
   [EdgeDeviceShare]
   comment = Edge Device Share Folder
   path = /home/nymea/shared/
   public = yes
   writable = yes
   browsable = yes
   create mask = 0777
   directory mask = 0777
   only guest = no
   guest ok = yes
   valid users = nymea
   ```
   
   Save the changes by pressing **Ctrl-X**,followed by **y**, followed by **Enter**.
   *At this point only the default user **nymea** will have access to this folder.*
   
5. Next we can add all the users that we want to be able to read, write, and share the folder.

   By default, the new user is granted the same permissions as the default user. The config file can be changed to reflect varying permissions for different users if need be. For    now, let's just assume every new user has full access.
   
   
 
   
   
   


