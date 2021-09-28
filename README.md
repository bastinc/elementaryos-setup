# elementaryos-setup

## Optimize laptop battery life

Install TLP

```
sudo add-apt-repository ppa:linrunner/tlp
sudo apt update
sudo apt-get install tlp tlp-rdw
sudo tlp start
```

Custom config for Thinkpad (see [tlp website](https://linrunner.de/tlp/installation/ubuntu.html#thinkpads-only))

```
sudo apt install  tp-smapi-dkms acpi-call-dkms
```

## Using a fingerprint reader

### Check if you  see your fingerprint reader

List usb devices

```
➜ lsusb
Bus 001 Device 003: ID 138a:0090 Validity Sensors, Inc. VFS7500 Touch Fingerprint Sensor
...
```

### Install requirements

```bash
sudo apt install fprintd libpam-fprintd
```

### For 138a:0090 and 138a:0097  chips only

```bash
sudo add-apt-repository -u ppa:3v1n0/libfprint-vfs0090
sudo apt install libfprint-2-tod-vfs0090
```

Initialize the device and pair it (thanks to  Marco Trevisan)[https://github.com/3v1n0/libfprint/#ubuntu-installation](https://github.com/3v1n0/libfprint/)

```
sudo snap install validity-sensors-tools

# Give it access to the usb devices
sudo snap connect validity-sensors-tools:raw-usb
sudo snap connect validity-sensors-tools:hardware-observe

# Initialize the device
sudo validity-sensors-tools.initializer

# Test the device
sudo validity-sensors-tools.led-test
```

### Enroll your finger

Example for your right index, 5 pass are needed

```
fprintd-enroll -f "right-index-finger" "$USER"
```

check if it's working 

```
fprintd-verify
```

### Activate authentication

You will be able to enter the password or type enter for blank input and use fingerprint

NOTE : The PAM stack is by design a serialised authentication, so it is not possible
 for pam_fprintd to allow authentication through passwords and fingerprints at
 the same time.

 See man page [pam_fprintd(8) — libpam-fprintd — Debian unstable — Debian Manpages](https://manpages.debian.org/unstable/libpam-fprintd/pam_fprintd.8.en.html)

For graphic apps , ligthdm, terminal

In */etc/pam.d/common-auth* add at the beginning of the file

```
auth       sufficient pam_unix.so try_first_pass likeauth nullok
auth       sufficient pam_fprintd.so timeout=10
```

At boot, you will still to unlock the gnome-keyring with your password.

## Development

### Handle multiple python version

With [Pyenv](https://github.com/pyenv/pyenv)

Get pyenv installer (to be able to update easily)

```
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```

Restart your shell so the path changes take effect:

```
exec $SHELL
```

To update

```
pyenv update
```

Dependencies to install a python version

```
sudo apt-get update; sudo apt-get install --no-install-recommends make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

List python versions available

```
pyenv install --list
```

Install a particular version

```
pyenv install 3.7.8
```

List installed versions

```
pyenv versions
```

Change default global version

```
nano ~/.pyenv/version
```
