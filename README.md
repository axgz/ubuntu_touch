# ubuntu_touch

Details of what happened

## Choosing the right hardware

I purchase a refurbished Pixel 3a XL as suggested by the supported devices on ubports.

https://devices.ubuntu-touch.io/

## Installing tools

I used a `Ubuntu 23.10` with kernel `6.5.0-15-generic` as my PC when completing the steps below.

**Installing adb and fastboot**

1. `apt-get update`
1. `apt-get install adb fastboot`

**Installing UBports**

1. Download the .deb file from: https://devices.ubuntu-touch.io/installer/deb
1. Install using `dpkg -i <package path>`

## Downgrading Android before installing ubuntu touch

As per the device prerequisites for this hardware I needed to downgrade to the latest version of Android 9. Currently the latest version of Android 12 was installed.

I attempted to use the OTA image however this approach does not support downgrades.

I reverted to unlocking the device bootloader and flashing with the required older version.

**Downloading the older factory images**

1. Image 9.0.0 (PQ3B.190801.002, Aug 2019) available here: https://dl.google.com/dl/android/aosp/bonito-pq3b.190801.002-factory-61a2836d.zip
1. Extract everything to `~/android/pixel-3a-xl/FLASH/bonito-pq3b.190801.002`

**Unlock bootloader:**

1. Within the current Android OS, enable dev tools by finding `Settings > About > Build Number` and tapping on it 7 times.
1. The go into `Settings > System > Developer Tools` and enable `USB Debugging` and plug the phone into the computer.
1. The phone didn't prompt me to accept the USB connection for my PC so I tried unplugging and plugging it in a few times.

    > Important: If the prompt on the phone to allow the PC doesn't appear or isn't accepted then the following error is produced when trying to run the adb commands: `error: insufficient permissions for device: missing udev rules?`.

1. Run `adb reboot bootloader` and wait for the phone to enter the bootloader screen.
1. Run `fastboot flashing unlock` and follow the prompts on the phone to unlock the bootloader.

https://source.android.com/docs/core/architecture/bootloader/locking_unlocking

**Flash the factory image**

1. Move to the folder when the factory image was extracted: `cd ~/android/pixel-3a-xl/FLASH/bonito-pq3b.190801.002`.
1. Run `adb reboot bootloader` and wait for the phone to enter the bootloader screen.
1. Run `./flash-all.sh` and be patient as the image is applied.

> Important: Normally you would re-lock the bootloader after the image has been applied, but as we are going to install Ubuntu Touch, locking the bootloader will prevent UT from booting as it is not signed like the official Android images are.

## Installing Ubuntu Touch

This was quite pain free but be sure to pay close attention to the steps shown with the UBports installer. The first step requires the device to be in the `Bootload` screen, but the second step requires the device to be in the `Recovery Mode` screen. If you are receiving error like `` make sure you are in the right spot on the device.

## Configuring UT

Nothing really needed here, all seems to work well and no tweaks used so far.

I do enable the ssh service occasionally as needed, for example to install WayDroid or Libertine.

**Enabling ssh**

The UBports docs for enabling shell access via SSH are great and steps worked without error.

https://docs.ubports.com/en/latest/userguide/advanceduse/ssh.html

**Using SSH**

After keys are installed, etc. the service will need to be started each time the device is rebooted.

1. On the device use the terminal app to start the ssh service `sudo service ssh start` and retrieve the current ip address `hostname -I`
1. Connect the device using ssh from the PC `ssh phablet@10.0.0.20 -i ~/.ssh/ubuntu_touch/id_rsa`

## Installing apps

The Open Store (app store) works great, there is just nothing in there.

- Android applications can be installed using WayDroid.
- Desktop applications can be install using Libertine.

To test the differences I installed Firefox using both.

## Firefox - via WayDroid

I used https://play.google.com/store/games?hl=en&gl=US to search for the Firefox app and then pasted its url into https://apkcombo.com to get a downloadable .apk file.

The UBports docs for install WayDroid are great and steps worked without error.

> Tip: use ssh to connect to the device.

1. Todo
1.  Add
1.   Steps

https://docs.ubports.com/en/latest/userguide/dailyuse/waydroid.html

Launching Firefox worked but it was a little slow, and WayDroid would regularly crash.

## Firefox - via Libertine

The UBports docs for install Libertine are good and steps worked without error. There is a missing warning that when you create the Libertine container it takes a long time (obviously hardware dependant) but also, you cannot close or switch out of the setting app (when using the device GUI to install) or the process with halt/fail.

I eventually used ssh to connect to the device from my PC Libertine in order to successfully create a container.

> Tip: use ssh to connect to the device.

1. Todo
1.  Add
1.   Steps

Launching Firefox worked however some tweaks were needed to set the resolution and to enable touch scrolling.

**Enable touch scrolling**

1. Open `about:config` in a new firefox tab. Then search for `dom.w3c_touch_events.enabled` and set its value to `1`.
1. Use your PC to ssh to the device.
1. Connect to the libertine container using `libertine-container-manager exec -i lxc -c /bin/bash`
1. Update Firefox's launch script `/usr/lib/firefox/firefox.sh` by adding `export MOZ_USE_XINPUT2 DEFAULT=1` under the other exports (somewhere near the top).

**Adjust screen resolution**

1. Open `about:config` in a new firefox tab. Then search for `layout.css.devPixelsPerPx` and set its value to `2`.
