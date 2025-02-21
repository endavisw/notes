# Deploying DNS

## Tests

Verify:
* Authoritative records in internal DNS resolve
* External recursive lookup test
* Use dig or nslookup

Manage > On-Prem Hosts > Select Host
* Bottom-right status section should be green

Manage > On-Prem Hosts > Select Host > Edit
* Applications & Services
  - DNS -> Enabled
* Save & Close

On-Prem Host needs to have a DNS Config Profile assigned

Manage > DNS > DNS Config Profiles > Select Profile > Edit
* On-Prem Hosts

## Services Not Running

Manage > On-Prem Hosts > Select Host
* Bottom-right status section
  - Platform Management
  - Application Management
  - DHCP
  - DNS
* Green = services are up

## Verification

DNS Activity Report in the CSP
- History of all DNS queries received by Black Swan DNS service
- About 4 minutes from real-time

## Troubleshooting

Outbound firewalls should allow TCP & UDP 53 to Internet
- No limits on packet size or session
- Only limit number of outbound queries

Testing with client gives best end-to-end verification

### Testing tool within On-Prem Host
Manage > On-Prem Hosts > Select Host > On-Prem Host (button)
- Copy API Access Key
- Approve
- Deny
- Disconnect
- Refresh Status
- Replace
- Remove
- Troubleshoot
  - Traceroute
  - DNS Test
    - Domain Name
    - DNS Test Results
  - Traffic Capture
  - NTP Test
  - DNS Configuration F(ile?)
  - DHCP Configuration (File?)
  - Restart HOst
  - Download DNS Cach(e)
  - Clear DNS Cache
  - GSS-TSIG
- Local Access

Troubleshoot menu runs commands locally On-Prem Host.

## Services

Manage > On-Prem Hosts > Select Host > Service (button)
* DHCP
  - Start
  - Stop
* DNS
  - Start
  - Stop
* Service Logs

Administration > Logs > Service Logs
* Step 1 - Select the option to view live logs or download logs
  - View Logs
  - Download Logs
* Step 2 - Select the service logs
  - DHCP
  - DNS
  - DNS Forwarding Proxy
  - NIOS Grid Connector
* Step 3 - Select the host you would like service logs for
* Step 4 - Select a timeframe for the service logs you would like to view
  - 5 minutes
  - etc
* Step 5 - View service logs
  - View Logs (button)
