# Personal Pi-hole adlists and settings

- [Pi-hole](https://github.com/pi-hole/pi-hole)
- [pihole-updatelists](https://github.com/jacklul/pihole-updatelists)
- [Energized Protection](https://github.com/EnergizedProtection/block)
- [Commonly White List](https://github.com/anudeepND/whitelist)


## Main

Clear all preinstalled pi-hole lists

```
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=0;" # whitelist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=1;" # blacklist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=2;" # regex whitelist
sqlite3 /etc/pihole/gravity.db "delete from domainlist where type=3;" # regex blacklist

sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=0;" # disabled adlists
sqlite3 /etc/pihole/gravity.db "delete from adlist where enabled=1;" # enabled adlists
```

Set lists for pihole-updatelists

`nano /etc/pihole-updatelists.conf`

```
; Remote list URL containing list of adlists to import
ADLISTS_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/_adlist.txt"

; Remote list URL containing exact domains to whitelist
WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/_whitelist.txt"

; Remote list URL containing regex rules for whitelisting
REGEX_WHITELIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/_whitelist_regex.txt"

; Remote list URL containing exact domains to blacklist
BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/_blacklist.txt"

; Remote list URL containing regex rules for blacklisting
REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/denis-g/pihole-adlists/master/_blacklist_regex.txt"

```

Update lists

`pihole-updatelists`