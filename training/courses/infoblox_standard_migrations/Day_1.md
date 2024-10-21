# 1 - Introduction
## Instructor
Rob Nagy - rnagy@infoblox.com - Deep-Dive Networks

## Course Co-Authors
- Michael Smith - IB West / LATAM PS
- Matt Richard

launchpad.education.infoblox.com
- Courses
 - Partner Learning
  - Professional Services Training
   - Services Certifications - Standard Migrations

Remote Lab Access: `245X7GPF`

The password to access the lab is: `Cold-523-Flower`


# 2 - Project Overview
Repeatable process. Migrate several times in-lab before go-live

## Managing the Project
Need to find out what the customer actually knows
- Institutional knowledge
- Technical skills/experience
  - Customer will need to do things during the go-live

Project kickoff meeting, technical kickoff meeting

## SOW Review
* Who
  - Actual technical contact
* What
* Where
  - On site? Remote?
* When
  - When can it happen
  - When do they want it done by
  - When are change windows
* Why

MS DNS Zone
- WINS Integration
  - Windows client NetBIOS node type may try WINS before DNS
  - Integrated zones will attempt WINS resolution if DNS not found,
    and then create DNS record
  - Latest Windwos Server does not support this any more
- Best to disable on per-zone basis
- **CANNOT** proceed with migration with a WINS-integrated zone

How many Domain Controllers? Forests? Zones? Records?

Don't base go-live on what you _**think**_, base only on what you _**know**_.

Validate customer claims in their data.

SOW should be outcome-based w/ guardrails
- Number of zones
- Number of sources
- Number of migration events/go-lives
- If customer data doesn't match/is larger, let them know that's not scoped
  and they need to choose which things they want

## Process Timeline
1. Kick off call
2. Platform deployment & data migration
3. Go-live prep call
4. Final Export/Import/Validation & go-live
5. Troubleshoot & follow-up

## Platform Deployment
Deployment guides are your friend
- Find guide for scenario that you like _and bookmark it_

Infoblox has ongoing effort to get all deployment guides into docs.infoblox.com

### Sanity Check Data
- How many NS?
- How many distinct SRV targets
- Zone types
- Zone counts

### Lab Testing of Data Parsing and Import
- Parse the data & find reality
- Any issues importing?
  - Issues?
  - Clean up, start over
- Validate
  - Have same records as exported from AD?
- Quickly repeatable?
  - Will basically have 24 hours in go-live
- **Document* the process
  - Document in class
  - Document for customer
  - If exporting to CSV, know:
    - How many lines
    - If errors, what they are & why
    - Import times
     - Clean lab, reimport CSVs

### Go-Live Prep
- Have (the call)
- Review
  - Lab findings
- Discuss (the plan)
- Prep
  - Helpers
  - Lease times
  - etc

### Final Expot / Import / Validation
- Compressed time
- Change Freeze / Change Tracking
  - What has changed?

### Go-Live
- Stay on task
- Be the voice of calm & reason
  - Know the plan, know the rollback plan
- _Be the conductor_ as customer makes their changes

### Document the Go-Live
- Work performed
  - Start/end times
- All testing results
- Any exceptions/errors
- Steps taken to resolve issues
- Any open issues or known gaps
- Any big successes
  - Stand-out customer actions
- Get sign-off on completion
  - Get in writing.

### Follow Up
- Tshoot
- Documenting
- KT
- Prod follow-up:
  - DHCP Helper cleanup
  - DDNS settings
    - "ISC Transitional" setting
  - Monitoring MS traffic
- Close the project


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

