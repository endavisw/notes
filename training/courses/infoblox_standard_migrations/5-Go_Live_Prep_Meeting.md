# 5 - Go Live Prep Call
- Review lab findings
- Review go-live runlist
  - Lease time step-down
  - IP helper addresses
  - Firewall rules
- Overview of go-live
  - Change freeze
  - Final Data Export / Import / Verficiation
  - Moving DHCP
  - Moving DNS
  - Moving back if need be
- Timing of week leading up to go-live event, crucial events, milestones, tasks
  - Lease time step-down
  - IP helper addresses
  - Firewall rules
- Final Go / No-Go Checklist
  - 2 hour lease times set?
  - IP helpers set?
- Post Go-Live tasks
- Specific roles & responsibilities
  - Make sure names of participants are known what they'll be working on

## DHCP Prep
- Lease times set to 2 hours
- Routers & clients need IP address to new DHCP servers
- _Add_ **do not delete** IP helper addresses
- netsh: `netsh dhcp server scope 10.0.0.0 set optionvalue 51 DWORD 3600`
- PowerShell: `Set-DhcpServerv4Scope -ComputerName "dc1.example.com" -ScopeId 10.10.10.0 -LeaseDuration 8.00:00:00`

## DNS Prep
- Can legacy forwarders send to Infoblox?
- Are there grid-external secondaries?
  - Can get transfers from Infoblox?
- Can Infoblox get zone transfer from external primaries?

### DNS Test Plans
- Pre-tests
  - All test performed and results recorded prior to go-live
  - Help eliminate false negative
    - Perhaps something has always been broken?
- Go Live tests
  - Use dig to query each ADC verify new SOA MNAME
  - net logon stop, net logon start, net ip register
    - or delete some SRVs, net login stop/start to ensure recreation
- **Tests requested by customer much have pre-go-live result recorded**

## Functional Groups
- Product teams know their applications and DNS sensitivity
- Test determine what they need to test
- Need pre- and post-testing
- Pre-testing close to go-live if possible
- Allow time in go-live for teams/groups to test

## Final Data Export
- Day of go-live or day before
- Changes must be frozen after export
- Undocumented changes will not be in Infoblox

## Asking Questions
Ask if anything's bothering them, even they're unsure why

## Change Control
Ensure everything's passed a week before Go-Live

## Final Go-Live Review
- Review all steps
- Emphasize/review all timing
  - No lag between steps after start
- Communicate responsible parties for each step
  - _Name_ specific members
- Review rollback plan
- Ensure all members for all critical teams are present
  - Network
  - Security / Firewall
  - Active Directory / Systems
  - Others
- Communication plan + all mobile numbers for involved members
  - Dependent on relationship with customer

## Common Issues
- Client IP changing, when really they should've had a fixed IP for security
  purposes
