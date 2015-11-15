#Create Raspberry Pi Kiosk on Raspbian Debian Jessie on Raspberry Pi 2#
This *will not* work on any Raspberry Pi model 1 (A, A+, B, B+).  This is only supported by the Raspberry Pi 2 which has the necessary ARM7 processor.

With this tutorial you can create a 'fullscreen kiosk' which shows a webpage. This webpage will run in chromium. For the Epiphany Browser read [this article](https://github.com/elalemanyo/raspberry-pi-kiosk-screen#epiphany-browser) 
## Install Raspbian Debian Jessie ##

The first step is to install Raspbian on the Raspberry Pi. There are 2 ways to do this.

- via the Raspberry Pi NOOBS image. (http://www.raspberrypi.org/help/noobs-setup/) Install the NOOBS image and install Raspbian via the NOOBS install manager
- install directly the Raspbian image (http://www.raspberrypi.org/documentation/installation/installing-images/README.md)

At the end you need a fresh Raspbian Debian Jessie installed on your Raspberry Pi.

##Configure Raspbian Debian Jessie##

When starting Raspbian for the first time you will see the Raspi Config tool. 
If it doesn't start automatically, you can do it manually with `sudo raspi-config`.
There are a few things you need to configure in raspi-config:

- Update the Raspi config tool (Advanced Options)
- Enable SSH when you want to proceed the configure process threw SSH (Advanced Options) This is optional.
- Disable overscan. This option tries to adjust the resolution for your Raspberry Pi. But it will only f**k things up :) (Advanced Options)
- Change your boot to desktop environment. This will start-up the GUI instead of the CLI and automatically will login to user 'pi'. 

Now reboot your machine. Normally Raspi-config ask to reboot, but if it doesn't you can reboot with `sudo reboot`

After rebooting the GUI environment will automatically startup and you will see the LXDE desktop environment. Now start the terminal and update your system:

```
sudo apt-get update
sudo apt-get upgrade
```
Now we install the tools we need to perfectly show the webpage. The next command will install x11 server utils and unclutter (to hide the cursor from the screen)

```
sudo apt-get install x11-xserver-utils unclutter
```

Now we need to install Chromium which is not available directly from Raspbian.  Instead, we are going to use the packages created by Ubuntu for ARM7 processors.

```
wget http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-browser_45.0.2454.101-0ubuntu0.15.04.1.1183_armhf.deb
wget http://ports.ubuntu.com/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra_45.0.2454.101-0ubuntu0.15.04.1.1183_armhf.deb

dpkg -i chromium-codecs-ffmpeg-extra_45.0.2454.101-0ubuntu0.15.04.1.1183_armhf.deb
dpkg -i chromium-browser_45.0.2454.101-0ubuntu0.15.04.1.1183_armhf.deb
```

The tools are installed. When the GUI starts up chromium needs to boot in kiosk-mode and open the webpage we filled in. In the next file we can add lines what needs to be executed at startup.

```
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
```

The autostart files needs to look like this

```
@xset s off
@xset -dpms
@xset s noblank
@sed -i 's/"exited_cleanly": false/"exited_cleanly": true/' ~/.config/chromium/Default/Preferences
@chromium-browser --noerrdialogs --kiosk http://www.page-to.display --incognito
```

The @xset options will disable the energy-options from the X-server so the screen won't be shut down after a x amount of time.
The @sed line will prevent errors to be shown.
And finally Chromium will be startup in kiosk mode (with no error messages). 
Change the url to the url you want it to show. Local files are also possibly.

Now Chromium will start after the raspberry pi is booted up and will show the webpage.

## Good to know ##
- default Raspbian login: user: pi pass: raspberry
- the GPU of the Pi isn't that powerful. So animations in the browser don't really run smoothly. 


## Sources ##

- http://www.raspberrypi-spy.co.uk/2014/05/how-to-autostart-apps-in-rasbian-lxde-desktop/
- http://www.danpurdy.co.uk/web-development/raspberry-pi-kiosk-screen-tutorial/
