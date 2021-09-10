# Personal Raspberry Pi 4 and Pi-hole settings

- [Raspberry Pi OS Lite](https://www.raspberrypi.org/software/operating-systems/)
- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block) | [Commonly White List](https://github.com/anudeepND/whitelist)

## Raspberry Pi OS Lite

### Install

- Create a new empty file `ssh` inside `/boot` directory.

### Configuration

Disable all interfaces, sound, and video.

```console
sudo nano /boot/config.txt
```

```ini
# Disable Audio
dtparam=audio=off

# Disable WiFi and Bluetooth
dtoverlay=disable-wifi
dtoverlay=disable-bt

# Disable HDMI
# On the Raspberry Pi 4, setting hdmi_blanking=1 will not cause the HDMI output to be switched off,
# since this feature has not yet been implemented.
hdmi_blanking=1
max_framebuffers=0

# Give more RAM to the CPU
gpu_mem=16

# Disable Serial Port
enable_uart=0

# Disable External interfaces
dtparam=i2c=off
dtparam=i2s=off
dtparam=spi=off
```

```console
sudo nano /etc/sysctl.conf
```

```ini
# Disable IPv6
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.eth0.disable_ipv6 = 1
net.ipv6.conf.wlan0.disable_ipv6 = 1
```

---

## Pi-hole

### Install

```console
curl -sSL https://install.pi-hole.net | sudo bash
```

---

## pihole-updatelists

### Install

```console
sudo apt install php-cli php-sqlite3 php-intl php-curl && \
wget -O - https://raw.githubusercontent.com/jacklul/pihole-updatelists/master/install.sh | sudo bash
```

### Configuration

Clear all preinstalled Pi-hole ad-lists.

```console
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;" # disabled adlists
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;" # enabled adlists
```

Set new ad-lists.

```console
sudo nano /etc/pihole-updatelists.conf
```

```ini
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/adlist.txt"
WHITELIST_URL="https://raw.githubusercontent.com/EnergizedProtection/unblock/master/basic/formats/domains.txt https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist.txt"
REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist_regex.txt"
BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist.txt"
REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist_regex.txt"
```

---

## Update system

```console
sudo apt update && \
sudo apt upgrade && \
sudo apt clean && \
sudo apt autoclean && \
sudo apt autoremove && \
sudo pihole-updatelists --update && \
sudo pihole-updatelists && \
sudo pihole -up && \
sudo rpi-update && \
sudo reboot
```
