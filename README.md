# Personal Raspberry Pi 4 and Pi-hole settings

- [Raspberry Pi OS Lite](https://www.raspberrypi.org/software/operating-systems/)
- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [DNSCrypt](https://github.com/DNSCrypt/dnscrypt-proxy/)
- [Energized Protection](https://github.com/EnergizedProtection/block) | [Commonly White List](https://github.com/anudeepND/whitelist)

## 🔹 Raspberry Pi OS Lite

### Install

- Create a new empty file `ssh` inside `/boot` directory.

### Configuration

Disable all interfaces, sound, and video:

```shell
sudo nano /boot/config.txt
```

```ini
# disable Audio
dtparam=audio=off

# disable WiFi and Bluetooth
dtoverlay=disable-wifi
dtoverlay=disable-bt

# disable HDMI
# On the Raspberry Pi 4, setting hdmi_blanking=1 will not cause the HDMI output to be switched off,
# since this feature has not yet been implemented.
hdmi_blanking=1
max_framebuffers=0

# give more RAM to the CPU
# only for console using
gpu_mem=1

# disable Serial Port
enable_uart=0

# disable External interfaces
dtparam=i2c=off
dtparam=i2s=off
dtparam=spi=off
```

Disable IPv6:

```shell
sudo nano /etc/sysctl.conf
```

```ini
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.eth0.disable_ipv6 = 1
net.ipv6.conf.wlan0.disable_ipv6 = 1
```

Set time zone:

```shell
sudo timedatectl set-timezone Europe/Minsk
```

---

## 🔹 Pi-hole

### Install

```shell
curl -sSL https://install.pi-hole.net | sudo bash
```

---

## 🔹 pihole-updatelists

### Install

```shell
sudo apt install php-cli php-sqlite3 php-intl php-curl && \
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
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/adlist.txt"
WHITELIST_URL="https://raw.githubusercontent.com/EnergizedProtection/unblock/master/basic/formats/domains.txt https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist.txt"
REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist_regex.txt"
BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist.txt"
REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist_regex.txt"
```

---

## 🔹 DNSCrypt

### Install

Copy URL to latest **ARM** release from [this page](https://github.com/DNSCrypt/dnscrypt-proxy/releases):

```shell
sudo wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.0/dnscrypt-proxy-linux_arm-2.1.0.tar.gz
sudo tar -xvzf ./dnscrypt-proxy-linux_arm-2.1.0.tar.gz
sudo rm dnscrypt-proxy-linux_arm-2.1.0.tar.gz

sudo mv ./linux-arm ./dnscrypt-proxy
sudo cp ./dnscrypt-proxy/example-dnscrypt-proxy.toml ./dnscrypt-proxy/dnscrypt-proxy.toml
```

### Configuration

Set servers and port for service:

```shell
sudo nano ./dnscrypt-proxy/dnscrypt-proxy.toml
```

```ini
server_names = ['scaleway-fr']

# don't use 53 or 5353 port
listen_addresses = ['127.0.0.1:54']
```

Install and start service:

```shell
sudo ./dnscrypt-proxy/dnscrypt-proxy -service install && \
sudo ./dnscrypt-proxy/dnscrypt-proxy -service start
```

Set service as custom DNS on admin panel - Settings > DNS:

```ini
Custom 1 (IPv4):
127.0.0.1#54
```

---

## 🔹 Update system

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
