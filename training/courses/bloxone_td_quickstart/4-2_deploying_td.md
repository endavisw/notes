# Deploying Threat Defense

## Enable the Service
Manage > On Prem Hosts > Select Host > Edit
* Name
* API Access Key
* Description
* Serial Number
* IP Space
* Tags
* Applications & Services
  - Access Authentication
  - Anycast
  - Data Connector
  - DHCP
  - DNS
  - DNS Forwarding Proxy -> Enabled
  - MS AD Sync
* DNS Local Resolver IP Settings
* Time Settings
* Proxy Settings
* Docker Bridge Settings

After enabling, needs to be attached to Security Policy

Policies > Security Policies > Security Policies > Select Policy > Edit
* General
  - Name
  - Description
  - Precedence
  - Geolocation
  - Safe Search
  - Local On-Prem Resolution
* Network Scope
  - Add Source
    - External Networks
    - DNS Forwarding Pro(xy)
      - Available
      - Selected
    - Endpoint Groups
    - User Groups
    - IPAM
* Policy Rules
  - Default Action
* Bypass Codes
* Summary
  - Save & Close

Save > Save & Close after selecting DFP On-Prem Host

Could take several to 20 minutes to enable DFP service & apply policy.

Manage > On Prem Hosts > Select Host > Bottom-Right Status

## Internal Domains

Manage > Internal Domains

## Get Started Quickly without DFP

Define DMZ & NAT ranges in External Networks, where traffic could originate from.

Could change forwarders in an MS DNS environment to use B1 TD.

Manage > External Networks

## Go Live
### Agentless Deployment

Could change On-Prem Host IP to that of previous forwarder, no changes to client config to use.

Or change client config to use new On-Prem Host for resolution.

### With Agent
Configure one or more Endpoint Groups
* Manage > Endpoints > Endpoint Groups > Create

Attach Endpoint Group to Policy:

Policies > Security Policies > Security Policies > Select Policy > Edit
* General
  - Name
  - Description
  - Precedence
  - Geolocation
  - Safe Search
  - Local On-Prem Resolution
* Network Scope
  - Add Source
    - External Networks
    - DNS Forwarding Pro(xy)
    - Endpoint Groups
      - Available
      - Selected
    - User Groups
    - IPAM
* Policy Rules
  - Default Action
* Bypass Codes
* Summary

Save > Save & Close after selecting Endpoint Group
* Very similar to attaching DFP OPH to Policy

## Testing
Use client & tools like dig/nslookup to generate traffic to the OPH.
* Use "badsite.com" for test to avoid alerts
* Avoid going to real potentially-malicious site

## Closing
DNS Logs & Reports

Reports > DNS Activity (or Security Activity)

Way to verify that B1 is being used for lookups
