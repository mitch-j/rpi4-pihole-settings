# Personal Pi-hole adlists and settings

- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block)


## Main

Clear all preinstalled pi-hole lists

```
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;"
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;"
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;"
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;"

sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;"
sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;"
```

Set lists for pihole-updatelists

`nano /etc/pihole-updatelists.conf`

```
; Remote list URL containing list of adlists to import
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/adlist.txt"

; Remote list URL containing exact domains to whitelist
WHITELIST_URL="https://github.com/EnergizedProtection/unblock/raw/master/basic/formats/domains.txt https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist.txt"

; Remote list URL containing regex rules for whitelisting
REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/whitelist_regex.txt"

; Remote list URL containing exact domains to blacklist
BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist.txt"

; Remote list URL containing regex rules for blacklisting
REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list https://raw.githubusercontent.com/denis-g/pihole-adlists/master/blacklist_regex.txt"

```

Update lists

`pihole-updatelists`