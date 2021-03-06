# Adb Instructions

`Adb`, short for Android Debug Bridge, is a versatile command line debugging tools, which has a variety of functions, such as tracking system logs, upload and download files, install and uninstall applications.

## Preparation

In order to use `adb`, you need to:

1. Use the male to male USB cable to connect host PC with the lower USB OTG port of the board:

   ![](img/hw_board_usbconn.png)

2. In Android running on the board, select `Settings` -> `USB`, and check on `Connect to PC` option.
3. Install adb driver and command, depending on your OS.

### Adb Installation in Windows

1. Install [Rockusb Driver].
2. Download [adb.zip](http://adbshell.com/upload/adb.zip), then unzip it to `C:\adb` for convenience.

Open a `cmd` window and run:

    C:\adb\adb shell

You are entering the adb shell if everything goes well.

### Adb Installation in Ubuntu

1. Install adb tool：

    ``` shell
    sudo apt-get install android-tools-adb
    ```

2. Add device ID:

    ``` shell
    mkdir -p ~/.android
    vi ~/.android/adb_usb.ini
    # add the following line:
    0x2207
    ```

3. Add udev rules for non-root user use：

    ``` shell
    sudo vi /etc/udev/rules.d/51-android.rules
    # add the following line：
    SUBSYSTEM=="usb", ATTR{idVendor}=="2207", MODE="0666"
    ```

4. Reload udev rules:

    ``` shell
    sudo udevadm control --reload-rules
    sudo udevadm trigger
    ```

5. Restart adb with normal user:

    ``` shell
    sudo adb kill-server
    adb start-server
    ```

The next you can invoke adb directly, for example:

``` shell
adb shell
```

## Frequently Used Adb Commands

### Connection Management

List all connected devices and their serial numbers:

``` shell
adb devices
```

If there are multiple connected devices, you need to use the serial number to distinguish them:

``` shell
export ANDROID_SERIAL=<device serial number>
adb shell ls
```

Also adb can be connected via the tcp/ip network.

``` shell
adb tcpip 5555
```

Adb will restart on the device side and listen on TCP port 5555.
The USB cable can be disconnected from now on.

If the IP address of the device is 192.168.1.100, you the following command to connect:

``` shell
adb connect 192.168.1.100:5555
```

Once connected, you can run adb command as usual:

``` shell
adb shell ps
adb logcat
```

Until you explictly disconnect adb:

``` shell
adb disconnect 192.168.1.100:5555
```

### Debug

#### Getting System Log

Usage:

``` shell
adb logcat [option] [Application label]
```

For example:

``` shell
# View all logs
adb logcat

# View only part of the log
adb logcat -s WifiStateMachine StateMachine
```

#### Gathering Bug Report

`adb bugreport` is used for error reporting, which gathers useful system information.

``` shell
adb bugreport

# Save to local, make it easy to use editor view
adb bugreport >bugreport.txt
```

### Running shell

Open an interactive shell:

``` shell
adb shell
```

Run shell command:

``` shell
adb shell ps
```

### Apk Management

#### Install Apk

```text
adb install [option] example.apk

options:
-l forward-lock
-r Reinstall the application to retain the original data
-s Install to SD card instead of internal storage
```

For example:

``` shell
# install facebook.apk
adb install facebook.apk

# upgrade twitter.apk
adb install -r twitter.apk
```

If install fails, check the common reasons below:

- `INSTALL_FAILED_ALREADY_EXISTS`: Try to add `-r` parameter to install again.
- `INSTALL_FAILED_SIGNATURE_ERROR`: APK signature is inconsistent, and it  may be due to the different version of the signature and debug version.  If you confirm the APK file signature is normal, you can use the adb uninstall command to uninstall the old application, and then install again.
- `INSTALL_FAILED_INSUFFICIENT_STORAGE`: There is not enough storage space.

#### Uninstall Apk

``` shell
adb uninstall apk_name
```

For example：

``` shell
adb uninstall com.android.chrome
```

The name of the apk package can be listed with the following command:

``` shell
adb shell pm list packages -f
...
package:/system/app/Bluetooth.apk=com.android.bluetooth
...
```

The apk file path and package name are separated by `=`.

[Getting Started]: started.md
[FAQ]: faq.md
[Serial Debug]: debug.md
[Building Linux Root Filesystem]: linux_build_rootfilesystem.md
[Contact]: resource.md#community
[Raw Firmware]: started.md#raw-firmware-format
[RK Firmware]: started.md#rk-firmware-format
[Partition Image]: started.md#partition-image
[SDCard Installer]: flash_sd.md#sdcard-installer
[Etcher]: flash_sd.md#etcher
[dd]: flash_sd.md#dd
[SD Firmware Tool]: flash_sd.md#sd-firmware-tool
[AndroidTool]: flash_emmc.md#androidtool
[upgrade_tool]: flash_emmc.md#upgrade-tool
[rkdeveloptool]: flash_emmc.md#rkdeveloptool
[Rockusb Mode]: flash_emmc.md#rockusb-mode
[Maskrom Mode]: flash_emmc.md#maskrom-mode
[Rockusb Driver]: flash_emmc.md#rockusb-driver
[ROC-RK3328-CC]: http://en.t-firefly.com/product/rocrk3328cc.html "ROC-RK3328-CC Official Website"
[Download Page]: http://en.t-firefly.com/doc/download/34.html
[Forum]: http://bbs.t-firefly.com/
[Facebook]: https://www.facebook.com/TeeFirefly
[Google+]: https://plus.google.com/u/0/communities/115232561394327947761
[Youtube]: https://www.youtube.com/channel/UCk7odZvUrTG0on8HXnBT7gA
[Twitter]: https://twitter.com/TeeFirefly
[Shop]: http://shop.t-firefly.com/
[USB Serial Adapter]: http://shop.t-firefly.com/goods.php?id=32
[5V2A US Adapter]: http://shop.t-firefly.com/goods.php?id=68
[eMMC Flash]: http://shop.t-firefly.com/goods.php?id=69
[Storage Map]: http://opensource.rock-chips.com/wiki_Partitions#Default_storage_map
