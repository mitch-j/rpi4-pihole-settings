# Personal Pi-hole adlists and settings

- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block)
- [Commonly White List](https://github.com/anudeepND/whitelist)


## Main

Clear all preinstalled Pi-hole lists

```sh
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist

sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;" # disabled adlists
sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;" # enabled adlists
```

Update lists on `pihole-updatelists` config

`sudo nano /etc/pihole-updatelists.conf`

```
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/adlist.txt"

WHITELIST_URL="https://raw.githubusercontent.com/EnergizedProtection/unblock/master/basic/formats/domains.txt https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist.txt"

REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist_regex.txt"

BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist.txt"

REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist_regex.txt"

```

Update lists

`pihole-updatelists`