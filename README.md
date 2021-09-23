# elementaryos-setup

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
 the same time. See man page [pam_fprintd(8) — libpam-fprintd — Debian unstable — Debian Manpages](https://manpages.debian.org/unstable/libpam-fprintd/pam_fprintd.8.en.html)

For graphic apps and terminal

In */etc/pam.d/common-auth* add at the beginning of the file

```
auth       sufficient pam_unix.so try_first_pass likeauth nullok
auth       sufficient pam_fprintd.so timeout=10
```
For greeter/login screen

In */etc/pam.d/lightdm*  used for as greeter on elementaryos, add at the beginning of the file

```
auth       include common-auth
```

