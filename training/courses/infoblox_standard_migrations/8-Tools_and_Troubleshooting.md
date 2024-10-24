# 8: Tools and Troubleshooting

## CLI
set expertmode
- Now dig is fully-featured
- traceroute
- tcpdump
  - capture with pre-cap filters

set maintenancemode
- show network_connectivity
  - This is nmap

show dns * commands:
- Regex need to be in / chars
  - show dns log /regex/

Check community.infoblox.com
- One big thread for tshooting commands for grid-member communication

## Service Configs

named & dhcpd config

Easy to compare differences in networks, or view matching logic in named

## CSV

## Support bundles
Some logging only available here

audit.log

syslog (/var/log/messages, etc)

infoblox.log
- last 100 MB
- full of errors, all the time

**WE ARE NOT SUPPORT**
- and do not have their tools
- do not be afraid to engage them

## nslookup
Awful tool, do not use it


## ISC list of tools with dig
https://www.isc.org/dns-tools

delv is newer tool from ISC, better DNSSEC support

## ISC named-check* tools
- named-checkconf
- named-checkzone
- named-compilezone
- named-rrchecker

Look at named-tools man page

## WAPI Overview
Script pack from CDWS course available in collateral

Postman collection available from community.infoblox.com

