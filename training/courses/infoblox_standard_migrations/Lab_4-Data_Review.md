# Lab 4 - Review the Data in “Lab-4-bloxcorp_dns_dhcp_datasets” Data Set

The data sets are on your Linux Desktop in your lab environment, in the “ps-nios” folder you copied
to your desktop

## Task 1: Sanity Checking
Open Data Set 1 and using any tools you wish answer the following;

### 1. Does the number of Zones match the SoW?
54 "DB" files in each ADC.

Customer noted "more than 10, less than 100" zones.

### 2. Does the number of Legacy DNS Servers match the SoW?

Two reverse zones have additional NS servers
150.10.in-addr.arpa & 253.10.in-addr.arpa reference srs002 & srs008 NS

Customer states srs002 & srs008 are from old project, should not be serving
anything.

ha.blox.corp delegated to F5 GTM from blox.corp.

Otherwise all zones NS are dc1 & dc2. Customer noted "Only DC1 & DC2"

### 3. Any concerns with the Legacy DNS Server information?

WINS record in blox.corp. Forwards to WINS on same ADCs

60 non-AD records with underscores. No records with % or $ characters

\ characters in 1 A record in wl.blox.corp: `dans\302\240bb`

/ characters in 6 records:
- `1.10.192.10.in-addr.arpa IN PTR den-7500-eth0/0.12-int.mgt.denver.blox.corp`
- `1.10.194.10.in-addr.arpa IN PTR dal-7500-eth0/0.18-int.mgt.dallas.blox.corp`
- `1.10.195.10.in-addr.arpa IN PTR mia-7500-eth0/0.12-int.mgmt.miami.blox.corp`
- `den-7500-eth0/0.12-int.mgt.denver.blox.corp IN A 10.192.10.1`
- `dal-7500-eth0/0.18-int.mgt.dallas.blox.corp IN A 10.194.10.1`
- `mia-7500-eth0/0.12-int.mgmt.miami.blox.corp IN A 10.195.10.1`

### 4. Does the number of Networks, Ranges and Fixed-Address match the SoW?

53 scopes on DC1, 50 on DC2. 53 unique scopes (confirmed netsh & dhcpconfig
dumps)

DIW - DC1
- 53 subnets, 53 ranges. 678 fixed addresses
- 17,190 dynamic IPs
- Custom Options
  - Microsoft_Options.Microsoft_Disable_Netbios_Option
  - Microsoft_Options.Microsoft_Release_DHCP_Lease_On_Shutdown
  - Microsoft_Options.Microsoft_Default_Router_Metric_Base
  - Microsoft_Windows_2000_Options.Microsoft_Disable_Netbios_Option
  - Microsoft_Windows_2000_Options.Microsoft_Release_DHCP_Lease_On_Shutdown
  - Microsoft_Windows_2000_Options.Microsoft_Default_Router_Metric_Base
  - No custom or problem options are in use.
- Used Options
  - TFTP_for_VoIP_Phones
    - 15 instances
  - bootfile-name
    - 9 instances set to "kbox2000.0" - PXE clients?
  - dhcp-lease-time
    - Only "172800", set to Import to Grid
  - domain-name
    - "blox.corp" 1 instance - Do not import
    - "blox.corp" 22 instances - Import to Grid
    - verify that "blox.corp" can be considered the "default" domain name
  - domain-name-servers
    - DC1 & DC2 instances - Do not import
    - Google DNS - Ask Customer
  - fqdn
    - Values "0" and "7" - What is this? Ask customer
  - netbios-name-servers
    - Used on 1 subnet - ask customer
  - netbios-node-type
    - Used on 1 subnet - ask customer
  - routers
  - slp-directory-agent
    - 15 instances set to "false" - ask customer
    - Option _should_ be array of IPs
    - _Customer states this can be ignored_
    - Do not import
  - tftp-server-name
    - 9 instances set to "10.204.17.55" - PXE clients?
    - Option format is text, probably OK
- Issues
    - Subnet 10.201.194.0/24 - Router option must be in subnet
    - Fixed IP 10.150.207.243 has invalid MAC: 00-00-00-00-20-72-43
- Select all Subnets
  - Change Serving Nodes to 10.100.0.105 & 10.200.0.105
- Select all Ranges
  - Change Serving Nodes to Internal_DHCP_FO
- FAILURES
  - 15 subnets
  - 16 ranges
  - 28 fixed addresses

DIW - DC2
- 50 subnets, 50 ranges. 667 fixed addresses
- 16,508 dynamic IPs
- Custom Options
  - Microsoft_Options.Microsoft_Disable_Netbios_Option
  - Microsoft_Options.Microsoft_Release_DHCP_Lease_On_Shutdown
  - Microsoft_Options.Microsoft_Default_Router_Metric_Base
  - Microsoft_Windows_2000_Options.Microsoft_Disable_Netbios_Option
  - Microsoft_Windows_2000_Options.Microsoft_Release_DHCP_Lease_On_Shutdown
  - Microsoft_Windows_2000_Options.Microsoft_Default_Router_Metric_Base
  - No custom or problem options are in use.
- Used Options
  - TFTP_for_VoIP_Phones
    - 12 instances
  - bootfile-name
    - 7 instances set to "kbox2000.0" - PXE clients?
  - dhcp-lease-time
    - Only "172800", set to Import to Grid
  - domain-name
    - "blox.corp" 1 instance - Do not import
    - "blox.corp" 20 instances - Import to Grid
  - domain-name-servers
    - DC1 & DC2 instances - Do not import
    - Google DNS - Ask Customer
  - fqdn
    - Values "0" and "7" - What is this? Ask customer
  - netbios-name-servers
    - Used on 1 subnet - ask customer
  - netbios-node-type
    - Used on 1 subnet - ask customer
  - routers
  - slp-directory-agent
    - 15 instances set to "false" - ask customer - did not import
    - Option _should_ be array of IPs
    - _Customer states this can be ignored_
    - Do not import
  - tftp-server-name
    - 7 instances set to "10.204.17.55" - PXE clients?
    - Option format is text, probably OK
- Issues
    - Fixed IP 10.150.207.243 has invalid MAC: 00-00-00-00-20-72-43
- Select all Subnets
  - Change Serving Nodes to 10.100.0.105 & 10.200.0.105
- Select all Ranges
  - Change Serving Nodes to Internal_DHCP_FO
- FAILURES
  - 0 failures


Many networks are /16 - is this correct?

### 5. Does the number of Legacy DHCP Servers match the SoW?

Received DHCP exports from DC1 & DC2. Customer noted "Only DC1 & DC2"

### 6. Any concerns with the Legacy DHCP Server information?

### 7. Would you request a follow up call with the customer?
Yes


### DIW Notes
NIOS session timeout is 1800. Too small, letting DIW change it to 86400


## Found Issues
### DNS
WINS record record on blox.corp zone
- _Customer states it can be removed from export_

150.10.in-addr.arpa & 253.10.in-addr.arpa using srs002 & srs008 NS

60 records with underscores in name

1 record with \ character in name
- _Customer states these can removed but wants a list_

6 records with / character in name
- _Customer states these can removed but wants a list_

Apex PTR Records in in-addr.arpa zones?


### DHCP
Subnet 10.201.194.0/24 - Router option must be in subnet
- Router set to 10.201.193.1, should be 10.201.194.1
- Corrected DC1 dhcpconfig XML, does not exist in DC2

Fixed IP 10.150.207.243 has invalid MAC: 00-00-00-00-20-72-43
- _Customer states this can be ignored but would like a list_

`slp-directory-agent` Option, 15 instances set to "false", should be bool & array of IP
- _Customer states this can be ignored_
- MS data files have more than first byte for boolean, DIW does not parse correctly?

Google DNS server 8.8.8.8 & 8.8.4.4 configured on
- 10.150.208.0 - Chicago Guest
- 10.150.224.0 - guestwifi
- 10.192.51.0 - Denver Guest
- 10.194.51.0 - Los Angeles Guest/guestwifi
- 10.195.51.0 - Miami Guest
- 10.254.0.0 - BYOD
- _Customer states this is fine_

## Data Corrections
### DNS
`blox.corp.db`
- Commented out lines for WINS record based on customer input

`wl.blox.corp`
- `dans\302\240bb`
- _Customer states this can removed but wants a list_
- Delete from db files, DIW seems to still see record despite comment

/ characters in 6 records:
- `1.10.192.10.in-addr.arpa IN PTR den-7500-eth0/0.12-int.mgt.denver.blox.corp`
  - Also esd0030.mgt.sjc.blox.corp.
- `1.10.194.10.in-addr.arpa IN PTR dal-7500-eth0/0.18-int.mgt.dallas.blox.corp`
- `1.10.195.10.in-addr.arpa IN PTR mia-7500-eth0/0.12-int.mgmt.miami.blox.corp`
- `den-7500-eth0/0.12-int.mgt.denver.blox.corp IN A 10.192.10.1`
- `dal-7500-eth0/0.18-int.mgt.dallas.blox.corp IN A 10.194.10.1`
- `mia-7500-eth0/0.12-int.mgmt.miami.blox.corp IN A 10.195.10.1`
- _Customer states these can removed but wants a list_
- Delete from db files, DIW seems to still see record despite comment

Do Not Import:
- `allow-transfer` from Any
- `allow-update` from GSS-TSIG or NStab

### DHCP
Subnet 10.201.194.0/24 - Change router from 10.201.193.1 to 102.201.194.1

Fixed IP 10.150.207.243 - Change MAC to 00-00-00-20-72-43

Import to Grid:
- Only 1 lease time used
- 1 instance of blox.corp domain-name

Do Not Import:
- `slp-directory-agent` Options
- `fqdn` Options
  - MS uses these for DDNS purposes but not in the standard/ISC way
  - https://www.carlwebster.com/the-mysterious-microsoft-dhcp-option-id-81/
- `domain-name-servers` Options with ADCs