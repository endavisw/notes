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
- **Document** the process
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
