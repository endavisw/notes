# Deploying DHCP

Verify DHCP service is enabled

Manage > On-Prem Hosts > Select On-Prem Host > Edit
* Verify IP Space association


Verify client got a lease from the correct server
* `ipconfig /all` on Windows

Manage > IPAM/DHCP > Address Spaces > Select Subnet
* Verify active lease in subnet

## Troubleshooting

Basic problems
* Connectivity
  - Routing, firewall, ACL issues
  - Can client reach the On-Prem Hosts
  - Verify router IP Helpers
* Can capture from On-Prem Host
  - Verify getting traffic from client

DHCP Config Issues
* Option requirements
* Check subnet
* Check DHCP config profile

Allow lease requests from unknown clients:

Manage > IPAM/DHCP > DHCP Config Profiles > Select Profile > Edit
* DHCP Configuration
  - Leases
    - Allow Leases for Unknown Clients -> checked
    - Allow Leases for Unknown IPv4 Clients -> checked
    - Ignore UID when issuing IPv4 DHCP Leases
