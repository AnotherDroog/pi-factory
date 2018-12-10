# variant-alpine

This repository contains everything necessary to bootstrap a LNCM box for [Raspberry Pi](https://www.raspberrypi.org) versions 0-3B+ based on Alpine Linux.

*[Alpine](https://alpinelinux.org) is a security-oriented, lightweight Linux distribution based on musl libc and Busybox.*

Alpine [wiki](https://wiki.alpinelinux.org/) holds further information related to system administration.

## Instructions

1. Download [Etcher](https://www.balena.io/etcher/).
2. Download latest [lncm-box.img.zip](
https://github.com/lncm/pi-factory/releases/download/v0.2.1/lncm-box-v0.2.1.img.zip)
3. Run Etcher and follow instructions to burn lncm-box.img.zip to SD card

**Experienced users:** Alternatively, use `dd` to burn the lncm-box.img to SD card

## Advanced usage

For manual installation and auditing:

1. Fetch official Alpine armhf [tar.gz](http://dl-cdn.alpinelinux.org/alpine/v3.8/releases/armhf/alpine-rpi-3.8.1-armhf.tar.gz) for Raspberry Pi.

1. (if not already present) Create FAT32L partition on SD card (fdisk type 0x0C), make partition bootable.

1. Create FAT32 volume using `dosfstools` package, e.g. `mkfs.vfat -F 32`

1. Extract tarball to SD card, e.g. `tar xvzpf alpine-rpi-3.8.1-armhf.tar.gz -C /Volumes/PI`

1. Extract latest lncm-box.tar.gz from releases page to SD card.

1. Optionally, create box.apkovl.tar.gz from source and place in SD card root, to ship your own modifications before first boot.

## Automated build

Use `make_img.sh` to create lncm-box.img automatically

## Access

**Note:** First boot will take some time as ssh host keys are generated.

### Authentication
- **username**: lncm
- **password**: chiangmai
- **root password**: chiangmai

**Note:** `sudo` is not installed, use `su` instead

### Using ssh
`ssh lncm@box.local`

**Note:** if no internet is available at boot, `cache` directory with avahi-daemon and dbus must be provided to enable `box.local` access. Alternatively, the IP address can be used. MAC addresses have a distinct Raspberry Pi Foundation prefix.

Using `nmap` you can find your Raspberry Pi on local subnets like so,
`sudo nmap -v -sn 192.168.0.0/24 | grep -B 2 "Raspberry Pi Foundation"`

### Using serial 
(serial TTY via TTL on uart)

Connect cable to *GND*, *RX*, *TX* pins, make sure you are using 3.3V and **not** 5V to prevent damage! With some devices RX & TX may have to be crossed.

Add `enable_uart=1` to `config.txt` on SD card FAT partition. (may not be necessary on older models)

e.g. `screen /dev/tty.usbserial-XYZ 115200`

### WiFi hotspot

The box can provide it's own WiFi hotspot to ease access and configuration.

- **WiFi name** (SSID): "LNCM-Box"
- **WiFi password**: "lncm box"

## Customizations

### Settings

**Note:** By default Alpine will not persist user changes upon reboot. Remember to commit all changes with `lbu commit`.

#### Networking
If you have console access:
- Use `wpa_passphrase` tool to set wifi settings
`wpa_passphrase "WiFi Name" "Password" >> /etc/wpa_supplicant/wpa_supplicant.conf`
- Or, run `setup-interfaces` if you have access to a running box.

In order to ship correct wifi configuration:
- Edit settings in `etc/wpa_supplicant/wpa_supplicant.conf`, re-create apkovl and copy to SD-card.

##### IOTWIFI Configuration

After connecting to "LNCM-BOX" you can tell the box to connect to your own home wifi network by issueing the following command from your own machine thats connected to the lncm network.

```bash
curl -w "\n" -d '{"ssid":"YOUR-SSID-NAME", "psk":"YOUR-PASSWORD"}' \
    -H "Content-Type: application/json" \
    -X POST http://192.168.27.1:8080/connect
```

#### Package management

- `apk update` Update repositories 
- `apk upgrade` Upgrade packages
- `apk add` Install package 
- `apk del` Uninstall package 

#### Init system

- `rc-update add docker boot` Start docker at boot
- `rc-update del docker boot` Remove docker from boot
- `rc-update` show startup services

Installation of LNCM specific components belongs in `etc/init.d/lncm`. The script is [OpenRC](https://wiki.gentoo.org/wiki/OpenRC) compatible and must be executable, without a file name extension.

`etc/apk/world` contains all apk packages to be installed by LNCM's install script.

- `service -l` list available services
- `service docker start` start docker now
- `service docker stop` stop docker now

The boot sequence is logged to `/var/log/rc.log` by default.

More information in OpenRC [user guide](https://github.com/OpenRC/openrc/blob/master/user-guide.md)

#### Misc

There are various configuration tools included to help you customize to your needs:

- `setup-hostname` 
- `setup-timezone` 
- `setup-keymap` 
- `setup-dns`

### Committing changes to SD card

**Important!** **Note:** By default Alpine will not persist user changes upon reboot. *The system is mounted read-only!*

Use `lbu commit` to persist changes. Add `-v` to see what is being committed.

`lbu status` will show changes to be committed.

**Note:** By default `lbu commit` only applies to *some* directories.

### Re-creating apkovl.tar.gz from source

Make sure you are in variant-alpine directory, e.g. `cd variant-alpine`

Set `export COPYFILE_DISABLE=true` to prevent MacOS from adding resource forks to tarballs.

`tar cvzpf box.apkovl.tar.gz --exclude ‘.DS_Store’ etc home`

### Unpacking apkovl from lncm-box.tar.gz

`tar xvzpf box.apkovl.tar.gz`

## Creating new apkovl

`lbu pkg /path/to/tar.gz` will produce a tarball of current system state.

*Important notes for distributing fresh apkovl*
 
**Remove unique and security sensitive files**
 
`rm etc/machine-id`

`rm etc/docker/key.json`

`rm etc/ssh/ssh_host_*`

Rewrite `/etc/resolv.conf` to be network independent.

Be mindful of passwords you set.
