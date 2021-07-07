# Personal Raspberry Pi 4 and Pi-hole settings

- [Raspberry Pi OS Lite](https://www.raspberrypi.org/software/operating-systems/)
- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block) | [Commonly White List](https://github.com/anudeepND/whitelist)

## Raspberry Pi OS Lite

### Install

- Download and burn SD card.
- Create a new empty file named `ssh`, without any extension, inside the boot directory.

### Configuration

```console
sudo nano /boot/config.txt
```

```ini
# Disable Serial Port
enable_uart=0

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

# Disable External interfaces
dtparam=i2c=off
dtparam=i2s=off
dtparam=spi=off

# Give more RAM to the CPU
gpu_mem=16
```

---

## Pi-hole

### Install

```console
curl -sSL https://install.pi-hole.net | bash
```

---

## pihole-updatelists

### Install

```console
sudo apt-get install php-cli php-sqlite3 php-intl php-curl
wget -O - https://raw.githubusercontent.com/jacklul/pihole-updatelists/master/install.sh | sudo bash
```

### Configuration

Clear all preinstalled Pi-hole lists.

```console
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;" # disabled adlists
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;" # enabled adlists
```

Update lists on `pihole-updatelists` config.

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
     apt upgrade && \
     apt clean && \
     apt autoclean && \
     apt autoremove && \
     pihole-updatelists --update && \
     pihole -up && \
     rpi-update && \
     reboot
```
