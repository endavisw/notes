# Configuring DHCP

## IP Spaces
* Set of data for DHCP
* One or more IP spaces
  - Assigned to different servers

Manage > IPAM/DHCP > Address Spaces > Create > IP Space
* Name
* Description
* Tags
* DHCP Configuration
  - IPv4 Automated Scope Management
  - Leases
  - DHCP Options
  - DDNS
  - Filters
* Save & Close

## Enable DHCP for On-Prem Host
* Manage > On-Prem Hosts > select host > Edit
* Assign Host to an IP Space
* Applications & Services
  - DHCP -> Enabled
* Save & Close

## DHCP Inheritance Model
* Global DHCP Configuration
  - Top-right of IPAM/DHCP screen
* Most changes should be done in Config Profiles or Option Groups
* Address Blocks
* Subnets
  - Also most practical for changes, with Config Profiles
* Ranges
* Fixed Addresses

## Config Profiles
Manage > IPAM/DHCP > DHCP Config Profiles > Create DHCP Config Profile
* Name
* Description
* Tags
* Leases
  - IPv4 Lease Time
  - IPv6 Lease Time
  - Allow Leases for Unknown Clients -> checked
  - Allow Leases for Unknown IPv4 Clients -> checked
  - Ignore UID when issuing IPv4 DHCP Leases
  - Ignore Lease Requests From:
* DHCP Options
 - Commonly used options like DNS & NTP
* DDNS
* Filters
- Save & Close

May want to override lease time on per-subnet basis if needed
- "Fast-moving" networks like wifi, etc

## High Availability
("HA" here appears to mean ISC Kea's HA modes, which are basically DHCP-FO)

* Active-Active
  - Like split-scope
  - May need to size networks larger
* Active-Passive
* Advanced Active-Passive
  - Generally recommended for most implementations
  - Future flexibility
  - Easier for future hub & spoke model for additional servers
  - Passive node may need to be larger to handle multiple downed Active nodes

Manage > IPAM/DHCP > HA Groups >  Create HA Group
* Name
* Description
* HA Configuration
  - Active / Active
  - Active / Passive
  - Adanced Active / Passive
  - Anycast
* (Advanced AP) Active On-Prem Host 1
* (Advanced AP) (Active|Passive) On-Prem Host 2
* Peer Connections
* Tags

## Build Out IP Space

Inside of Address Space under IP Space:

Manage > IPAM/DHCP > Address Spaces > Create > Subnet > (IPv4|IPv6) Subnet
* Address
  - Network & prefix-length/CIDR
  - Get next available
* Quantity
* Name
* Description
* IP Space
* On-Prem Host
  - Host
  - HA Group
* Tags
* DHCP Configuration
  - "Save" to load inherited options
  - (IPv4 Automated Scope Management)
  - Leases
  - DHCP Options
  - DDNS
  - Filters
* Save & Close


## Option Groups
Group options commonly used together
* Ex., per campus, per site, etc

Manage > IPAM/DHCP > Option Groups > Create > (IPv4|IPv6) Option Group
* Name
* Description
* DHCP Option Codes
  - Type
  - Space
  - Name
  - Option Type
  - Value
* Tags
* Save & Close

Manage > IPAM/DHCP > Address Spaces > Select Subnet > Edit
* DHCP Option Codes
  - Add > Option Group
  - Name
* Save & Close

## Ranges
Manage > IPAM/DHCP > Address Space > Create > Range
* Subnet (selected)
* Start
* End
* Name
* Description
* On-Prem Host
* Tags
* DHCP Configuration
  - "Save" to load inherited options
  - DHCP Options
  - DHCP Exclusion Ranges
* Save & Close

More information on https://docs.infoblox.com
