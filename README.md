# Z/IP Gateway SDK

## LICENSING

Z/IP Gateway is covered by one of several different licenses.
The default license is the [Master Software License Agreement (MSLA)](https://www.silabs.com/about-us/legal/master-software-license-agreement), which applies unless otherwise noted. 
Refer to [LICENSE](./LICENSE) for more details.


## HOW TO USE Z/IP GATEWAY

Reference setup, is RaspberryPi on rasbpian-9 OS (EoL)

Dump image to sdcard from:

- http://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2019-04-09/2019-04-08-raspbian-stretch-lite.zip

Prepare system with required dependencies:

```sh
sudo sed -e 's|raspbian.raspberrypi.org|legacy.raspbian.org|g' \
            -i /etc/apt/sources.list \
            -i /etc/apt/sources.list.d/*.list

sudo apt-get update
sudo apt-get install -y etckeeper # Will track changes in /etc
```

Setup a Z-Wave NCP controller, firmware should be flashed using tools from:
[simplicity studio](https://www.silabs.com/developers/simplicity-studio).

The alternative option is to download firmware
["zwave_ncp_serial_api_controller-${board}-${region}.hex"](https://github.com/SiliconLabs/gecko_sdk/releases#demo_application.zip) 
and deploy it using 
[SimplicityCommander-Linux](https://www.silabs.com/documents/public/software/SimplicityCommander-Linux.zip)

Then connected to USB port, it should be detected, you can check using:

```sh
ls /dev/serial/by-id/usb-Silicon_Labs_*
```

Download zipgateway deb package from release page:

- [https://github.com/SiliconLabs/zipgateway/releases](https://github.com/SiliconLabs/zipgateway/releases)

Copy it to target device and install it along its dependencies and configure daemon accordingly.

```sh
sudo dpkg -i zipgateway-*-Linux-armhf.deb || sudo apt install -f # To install missing deps
sudo apt --fix-broken install # Will resume installing package if needed
sudo dpkg -L zipgateway # List server and client and other utilities
cat /usr/local/etc/zipgateway.cfg # To check configuration file
```

Use zipgateway using example libzwaveip's reference_client 
(now part of the package previously installed)

```sh
# Check if daemon is running
$ systemctl status zipgateway # should report service active
$ tail -F /usr/log/zipgateway.log # To see traces

# Connect to daemon using client, and use interactive shell:
$ reference_client # Will report usage

reference_client -n1 
(ZIP) help
(ZIP) setdefault
(ZIP) list
(ZIP) addnode
...
```

## HOW TO BUILD Z/IP/GATEWAY

As said above, only 32 bits OS are currently supported. 

Reference OS is currently debian-9 (EoL), native building is supported (using cmake),
and you can rely on helper script to setup system and pass tests to generate a debian package 
ready to be installed (see above) (This script can be also run from docker or github)


### NATIVE BUILD ON TARGET DEVICE 

It should take less than 20 min to build and run tests.

```sh
sudo apt install make
./helper.mk help
./helper.mk setup/raspbian
./helper.mk
```

For the record, dependencies are listed in helper.mk, 
compilation relies on cmake using standard directives:

```sh
mkdir build
cmake ..
cmake --build .
```

Feel free to tweak env, 
debugging on board using gdb can be helpful too.


### BUILD ON HOST

To speed up the build, native build can be deported to host using 
different containerization techniques (docker, systemd. chroot, qemu, binfmt),
check DevTools directory for more.


## MORE

Extra documentation is available in doc folder or online:

- https://github.com/SiliconLabs/zipgateway/
- https://www.silabs.com/wireless/z-wave/specification
- https://docs.silabs.com/z-wave/1.0.1/version-history 
- https://www.silabs.com/wireless/z-wave
- https://www.silabs.com/documents/public/release-notes/SRN14932-1C.pdf
- https://community.silabs.com/s/topic/0TO1M000000qHcQWAU/zwave
- https://z-wavealliance.org/
- https://github.com/Z-Wave-Alliance/
- https://www.silabs.com/support
