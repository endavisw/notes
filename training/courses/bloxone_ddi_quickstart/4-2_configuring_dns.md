# Configuring DNS

## IP Space
Entitlements
- Keep track of customer's licensing
- Everything licensed by IP
- Proper association between DNS Views & IP Space will allow platform to properly represent license usage

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

Manage > DNS > Select View (likely 'default' )> Edit
* Name
* Description
* IP Space
  - Select matching IP Space
* Disable for DNS Protocol
* Match recursive queries only
* Tags
* Match Clients
* Queries
* Zone Transfers
* Updates

Default view is OK for simple deployments

For multiple sets of data, multiple environments, consider default is the first/core internal-DNS one, and create others.

## Inheritance
* Global Level
  - Infoblox recommends minimal amount of changes to Global
  - Applies to everything
* DNS Config Profiles
  - Most changes made here
  - Inherited by DNS Servers
  - One for each environment, ie: Internal DNS, IoT, Lab
* DNS Server Level
  - Specific DNS Server changes, like forwarders

## Configuration Profiles
Things like allowing queries, allowing recursion, etc.

Most customers allow queries & allow recursion from anywhere for Internal DNS

Manage > DNS > DNS Config Profiles > Create DNS Config Profile
* Name
* Description
* Match recursive queries only
* Tags
* Queries
  - Use Minimal Responses
  - Allow Queries From
    - Type
      - Any Address/Network
      - IPv4 Address
      - IPv4 Network
      - Named ACL
      - TSIG Key
    - Value
    - Permission
      - Allow
      - Deny (assumed)
* Zone Transfers
* Updates
* Recursion
  - (i) If DNS and DFP are installed and enabled on the same host, DNS will apply recursion settings and DFP will apply forwarding settings.
  - Allow Recursion
  - Resolver query timeout
  - Max Cache TTL
  - Max Negative Cache TTL
  - Allow Recursive Queries From
    - Type
      - Any Address/Network
      - IPv4 Address
      - IPv4 Network
      - Named ACL
      - TSIG Key
    - Value
    - Permission
      - Allow
      - Deny (assumed)
* Logging
* DNSSEC
  - Enable DNSSEC
  - Enable Validation
  - Accept Expired Signature
  - Trust Anchors
    - Zone
    - Secure Entry Point
    - Algorithm Type
    - Public Key
* Server
* EDNS Client Subnet Configuration
  - Enable Recursive EDNS Client Subnet
  - Enable EDNS Client Subnet Forwarding
  - Query Zone Permissions
    - Zone
    - Permission
  - IPv4 Source Prefix (length)
  - IPv6 Source Prefix (length)
* Zone Settings Defaults
* Save & Close

### Notable Config Profile Settings
* DNSSEC
  - If customer is forwarding to MS DNS, turn off DNSSEC
  - Rare that customer has done DNSSEC forwarding internally
  - MS DNS servers do not have DNSSEC chain of trust
  - Defaults to on
* EDNS
  - EDNS has the ability to provide client IP subnet & IP address
  - Very important for BloxOne Threat Defense
  - Logging in B1 CSP will show client IP addresses
  - Important to have this turned on
  - Defaults to on

## Enabling DNS Servers
Manage > On-Prem Hosts > Select Host > Edit
* Applications & Services
  - DNS -> Enabled
* Save & Close

Manage > DNS > DNS Servers
* Server will now appear in list > Edit
* Name
* Description
* DNS Config Profile
* DNS Name
* Tags
* GSS-TSIG Configuration
* Save & Close

If you don't define a DNS Name for the server, the member won't show up as a name server within the zone. Needs to be an FQDN. Trailing dot added automatically.

## DNS Server Groups
### Forward Name Server Group
Consistent way to apply forwarding settings, ie., if forwarding to MS DNS

Manage > DNS > DNS Server Groups > Create > Forward DNS Server...
* Name
* Description
* DNS Servers
  - Available
  - Selected
* Forwarders
  - Type
    - Forward to address
    - Forward to DNS Server Group
    - Forward to On-Prem Host
  - Name
    - Optional, but important in all cases
    - Best practice to populate
    - May be auto-populated when forward zone created
  - IP Address
* Save & Close

### Authoritative Name Server Group
Every zone created the On-Prem Host is secondary, BloxOne itself is primary.

On-Prem Host has 2 containers:
* Primary receives updates from BloxOne
* Secondary is exposed to end-users

Manage > DNS > DNS Server Groups > Create > Authoritative DNS S...
* Name
* Description
* BloxOne DNS Primary Servers
  - Name
  - Address
  - TSIG Key Name
  - TSIG Key Algorithm
  - TSIG Secret
* BloxOne DNS Secondary Servers
  - Available
  - Selected
* Save & Close

## Zones & Records
### Zones
Manage > DNS > Zones > Select View > Create
- Primary Zone
- Forward Zone
- Secondary Zone
- Zone Deledation

Manage > DNS > Zones > Select View > Create > Primary Zone
* DNS View
* Name
* Description
* Disable for DNS Protocol
* Notify External Secondary DNS Servers
* Tags
* Authoritative DNS Servers
  - BloxOne DNS Authoritative Servers
    - Available
    - Selected
  - DNS Server Groups (Secondaries Only)
    - Available
    - Selected
* Zone Setting Defaults
* Queries
* Zone Transfer
* Updates
* Save & Close

### Records
Select Zone

Manage > DNS > Zones > Select View > Select Zone > Create
- Zone
  - Primary Zone
  - Forward Zone
  - Secondary Zone
  - Zone Delegation
- Record
  - A Record
  - AAAA Record
  - CAA Record
  - CNAME Record
  - MX Record
  - NAPTR Record
  - NS Record
  - PTR Record
  - SRV Record
  - TXT Record
  - Generic Record

Manage > DNS > Zones > Select View > Select Zone > Create > Record > A Record
* DNS View
* Name
* Select Zone
* IP Address
* Description
* Create associated PTR record (Reverse Mapping Zone required)
  - Create A record only if PTR record successfully created
* TTL
* Disable for DNS Protocol
* Tags
* Save & Close

Part of the QuickStart Customer Engagement is to show them how to create zones & records

## Differences between IPv4 and IPv6
IPv6 interface is not required to serve IPv6-related records
