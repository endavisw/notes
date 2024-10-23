# 3 - Platform Deployment & Initial Data Export
## Architecture Review
Have a diagram from SA

Understand what was sold, BOM, etc.

Deployment guides - docs.infoblox.com

DNS Traffic Control - YouTube videos by Ross Gibson

Only enable SSH if allowed by customer security policy

### Hardware
Hardware needs portfast or no-STP ports

### VM
VMware VSS - VM virtual NIC requirements
- forged transmit
- MAC address changes
- HA will come up but not work without

### Cloud
Each provider has different limits for functions, model, etc

Force DC diversity

Centralized DHCP in Azure will have rate limits (used to not work)

## Deploy SA's Design & Initial Grid Config
`set network` has better interface than `set membership`

- Ask customer for standard security banner
- Disable GUI redirect from member
- Disable HTTP to HTTPS redirect

Use ibadmin to configure local user for self to work on engagement

DNS resolver

Customers should have individual superuser accounts

Configure 30 minute session timeout post-go-live

## DNS/DHCP, Secure, and Optimized Configs
### DNS
DNS Views

RFC 6303 & 7793 defined zones (also 6509?)

Record & Zone Monitoring

Recursive Timeout = 10 seconds

Recursive Clients = dependant by HW platform
- X6 hardware is more capable, 10k probably OK

Name Server Groups
- Similar naming to views

### DHCP
FO Groups

Grid Lease Time = 4 hours

Authoritative
- Opt 6 - NS Servers
- Opt 15 - Domain

Target Check Only mode post-golive

Updaete On Renew
- Do not use in normal circumstances
- Risky but helpful
- Disable after successful go-live
- Clients will likely be renewing during go-live, rather than discovering

**All clients need to have a lease time of 4 hours or less at go-live**
- 4 hours > grid lease time > 2 hours

Ratchet lease time down
- 2 days, then 4 hours
- Wait for timer1 to expire and force renewal to bring new lease time down

Add new DHCP servers to router IP Helper/Relay devices
- Do not remove existing
- **Systems that can't support that many, change them during go-live**


### Admin rights
Local admins
- Dedicated account for project
- Emergency account for access
- SAML guides for Okta & Duo oldest, likely most complete

Local should auth first
- Too many external AD servers would take longer to timeout than GUI session timeout

### Other
Firewalls & ACLs
- Specific Grid ports for replication
- Logging
- SNMP
- NTP

DNS access, DHCP access

## Legacy Data Export
### Microsoft
Powershell scripts - Must be ran as Administrator
- DNS Admin rights

Exports to BIND9 zone format, not not quite be legal

Used to be `net sh` command

`extract-ms-dns.ps1 -DiscoverServers 1`
- Will discover all domain-joined servers in forest with DNS Role
- Output in `dns_export` subdirectory

`extract-ms-dhcp.ps1 -DiscoverServers 1`
- Will discover all domain-joined servers in forest with DHCP Role
- Output in `dhcp_export` subdirectory

### ISC BIND
Include all in /var/named (/var/lib/bind, etc)
- All db files
- All included files

`ps aux` helpful to find running config file path

`named-checkconf` will output whole config with includes

Sanity check with Data Import Wizard
- Checks for presense of all required files

### DHCPD
Get dhcpd.conf and includes

### Base Line Sanity checks

named-checkconf, named-checkzone, named-check*
- rrchecker is new?

Quick verification of
- NS sets
- SRV targets
- Network counts
- Range counts

DIW in parsing/browsing mode is helpful
