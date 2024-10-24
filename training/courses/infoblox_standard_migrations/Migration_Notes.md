Restore Lab3_Initial_Config.bak to Grid

Data Management -> DNS -> Grid DNS Properties -> Basic -> Email for SOA RNAME
- set to 'noc@blox.corp'

Pre:
- Look for WINS, defunct NS, illegal characters
- Find DNS zones unique to ns2
- Find DNS records unique to ns2 zones
- Convert netsh output from UTF16 to UTF8
- Find DHCP scopes unique to ns2

DIW DNS Import
- Use grid email for RNAME
- UNCHECK remove A records for NS hosts
  - Otherwise no A records for dc1/dc2
- Do not import:
  - allow-transfer any
  - allow-update *
- Set member to Internal_NSG master
Look for other allow-update zones, see if reasonable

Validate counts for A, PTR, CNAME, MX, SRV, AAAA

DIW DHCP Import
- Do not import:
  - domain-name-servers with DC1/2
  - slp-directory-agent
  - fqdn
  - \>1 instance blox.corp domain
- Set member on subnets to ns1/ns2 IPs
- Set member on ranges to Internal_DHCP_FO group

After:
- allow-update ACL with DC1/2 IPs, and set at high-level
  - _only DCs should be able to update on their own_
  - DHCP will update everyone else
- forward-only zones only import set to 1 nameserver, need to add 2nd!
- grid-level DHCP option for domain-name-servers to ns1/ns2
- grid-level lease time = 7200 seconds
  - for roll-back purposes
- DHCP exclude ranges!
- change session timeout back to 1800 seconds

**Make sure ns1/2 are running DNS service after import**

_DHCP would be turned on **during** go-live_
