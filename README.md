# Personal Pi-hole adlists and settings

- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block)
- [Commonly White List](https://github.com/anudeepND/whitelist)

## Setup

```sh
sudo apt-get install php-cli php-sqlite3 php-intl php-curl
wget -O - https://raw.githubusercontent.com/jacklul/pihole-updatelists/master/install.sh | sudo bash
```

## Configuration

Clear all preinstalled Pi-hole lists

```sh
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sudo sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;" # disabled adlists
sudo sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;" # enabled adlists
```

Update lists on `pihole-updatelists` config

```sh
sudo nano /etc/pihole-updatelists.conf
```

```
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/adlist.txt"

WHITELIST_URL="https://raw.githubusercontent.com/EnergizedProtection/unblock/master/basic/formats/domains.txt https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist.txt"

REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist_regex.txt"

BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist.txt"

REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist_regex.txt"
```

Update lists

```sh
sudo pihole-updatelists
```

## Update system

```sh
sudo apt update && \
     apt upgrade && \
     apt autoclean && \
     apt clean && \
     apt autoremove && \
     pihole-updatelists --update && \
     pihole -up && \
     rpi-update && \
     reboot
```
