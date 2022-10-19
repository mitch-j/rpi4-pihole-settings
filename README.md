# Personal Raspberry Pi 4 and Pi-hole settings

- [Raspberry Pi OS Lite](https://www.raspberrypi.org/software/operating-systems/)
- [log2ram](https://github.com/azlux/log2ram)
- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block) | [Commonly White List](https://github.com/anudeepND/whitelist)
- [DNSCrypt](https://github.com/DNSCrypt/dnscrypt-proxy/) (Optional)

## 🔹 Raspberry Pi OS Lite

### Install

Download and install [Raspberry Pi Imager](https://www.raspberrypi.com/software/):

- Select **Raspberry Pi OS Lite**
- Select storage
- Click `Settings` and set up
  - [x] Disable overscan
  - [x] Set hostname
  - [x] Enable SSH
  - [x] Set username and password
  - [ ] Configure wifi
  - [x] Set locale settings
  - [ ] Play sound when finished
  - [x] Eject media when finished
  - [ ] Enable telemetry
- Click `Write`

### Configuration

Connect to your device:

```shell
ssh username@raspberrypi.local
```

Set up Raspberry Pi OS:

```shell
sudo raspi-config
```

- set locale as `en_US.UTF-8 UTF-8`

Disable all interfaces, sound, and video:

```shell
sudo nano /boot/config.txt
```

```ini
# Enable audio (loads snd_bcm2835)
dtparam=audio=off

# Automatically load overlays for detected cameras
camera_auto_detect=0

# Automatically load overlays for detected DSI displays
display_auto_detect=0

# WiFi and Bluetooth
dtoverlay=disable-wifi
dtoverlay=disable-bt

# HDMI
# On the Raspberry Pi 4, setting hdmi_blanking=1 will not cause the HDMI output to be switched off,
# since this feature has not yet been implemented
hdmi_blanking=1
max_framebuffers=0

# RAM to the CPU, only for console using
gpu_mem=1

```

Disable IPv6:

```shell
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

## 🔹 log2ram

### Install

```shell
sudo apt install rsync && \
echo "deb [signed-by=/usr/share/keyrings/azlux-archive-keyring.gpg] http://packages.azlux.fr/debian/ bullseye main" | sudo tee /etc/apt/sources.list.d/azlux.list && \
sudo wget -O /usr/share/keyrings/azlux-archive-keyring.gpg https://azlux.fr/repo.gpg && \
sudo apt update && \
sudo apt install log2ram
```

### Configuration

```shell
sudo nano /etc/log2ram.conf
```

For generates many logs (like a Pi-hole), you needed to increasing this to a larger amount, such as 256M.

```ini
SIZE=256M
```

Deletion of old "archived" logs can be fixed by adjusting a setting:

```shell
sudo nano /etc/systemd/journald.conf
```

```ini
SystemMaxUse=20M
```

---

## 🔹 Pi-hole

### Install

```shell
curl -sSL https://install.pi-hole.net | sudo bash
```

### Configuration

Use wizard and set up.

---

## 🔹 pihole-updatelists

### Install

```shell
sudo apt-get install php-cli sqlite3 php-sqlite3 php-intl php-curl && \
wget -O - https://raw.githubusercontent.com/jacklul/pihole-updatelists/master/install.sh | sudo bash
```

### Configuration

Clear all preinstalled Pi-hole ad-lists:

```shell
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;"  # disabled adlists
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;"  # enabled adlists
```

Set new ad-lists:

```shell
sudo nano /etc/pihole-updatelists.conf
```

```ini
ADLISTS_URL="https://raw.githubusercontent.com/mitch-j/rpi4-pihole-settings/master/adlist.txt"
WHITELIST_URL="https://raw.githubusercontent.com/EnergizedProtection/unblock/master/basic/formats/domains.txt https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt https://raw.githubusercontent.com/mitch-j/rpi4-pihole-settings/master/whitelist.txt"
REGEX_WHITELIST_URL="https://raw.githubusercontent.com/mitch-j/rpi4-pihole-settings/master/whitelist_regex.txt"
BLACKLIST_URL="https://raw.githubusercontent.com/mitch-j/rpi4-pihole-settings/master/blacklist.txt"
REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/mitch-j/rpi4-pihole-settings/master/blacklist_regex.txt"
```

---

## 🔹 DNSCrypt (Optional)

### Install

Copy URL to latest **ARM** release from [this page](https://github.com/DNSCrypt/dnscrypt-proxy/releases):

```shell
sudo wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.1/dnscrypt-proxy-linux_arm-2.1.1.tar.gz
sudo tar -xvzf ./dnscrypt-proxy-linux_arm-2.1.1.tar.gz
sudo rm dnscrypt-proxy-linux_arm-2.1.1.tar.gz

sudo mv ./linux-arm ./dnscrypt-proxy
sudo cp ./dnscrypt-proxy/example-dnscrypt-proxy.toml ./dnscrypt-proxy/dnscrypt-proxy.toml
```

### Configuration

Set servers and port for service:

```shell
sudo nano ./dnscrypt-proxy/dnscrypt-proxy.toml
```

```ini
server_names = ['cloudflare-family', 'cloudflare']

# don't use 53 or 5353 port
listen_addresses = ['127.0.0.1:54']
```

Install and start service:

```shell
sudo ./dnscrypt-proxy/dnscrypt-proxy -service install && \
sudo ./dnscrypt-proxy/dnscrypt-proxy -service start
```

Set service as custom DNS on Pi-hole admin panel - Settings > DNS:

```ini
Custom 1 (IPv4):
127.0.0.1#54
```

---

## 🔹 Update system and reboot

```shell
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
