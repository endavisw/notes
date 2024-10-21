# SOW Review
## SOW Data
### In-Scope Areas
Project Governance
- Project Management
- Implementation Discovery

DDI Migration
- Internal Authoritative DNS
- Internal DHCP

IPAM
- Basic IPAM Assistance

Post Go Live Support

| Legacy Platform | DNS | # DNS Views or AD Forests | DHCP | # of Go-Live Events |
| --------------- | --- | ------------------------- | ---- | ------------------- |
| Microsoft       | Yes | 1                         | Yes  | 1                   |

### Basic IPAM Assistance
- Review customer IPAM spreadsheets
- Assist customer to define & create EAs/tags
- Assist customer w/ initial export of CSV Header Import File
- Provide customer w/ guidance on using CSV Import Tool
- Customer to perform testing in lab/test env
- Assist customer w/ issue resolution

### Assumptions
- Customer to input data in standardized format
- Customer responsible for data cleanup & transformation
- CSV import testing performed by customer in customer lab

## SOW Questions
* Who
  - Customer technical contact for project?
    - _Rob_
    - Do they have an Infoblox support account?
  - Does customer have internal PM?
    - _No_
  - _Web team (Tom & Sandra) often wants changes rolled back_
* What
  - Migration of Legacy Microsoft DDI system to Infoblox
    - Verify Platform, protocols, # of AD forests, # of anticipated go-lives
      - _Only have one forest, use dev/test (sub)zone temporarily_
      - _Dev/test will need Infoblox access later for zone spinup_
    - How many DCs? Ballpark number of zones, records?
      - _DC1 & DC2, both DNS & DHCP, used to have more_
      - **Ask for current technical diagram**
      - Be **sure** to ask for zone & record counts
      - _More than 10 zones_
      - _A lot of fixed addresses, unknown number of records_
    - Any WINS-integrated zones?
      - _Still have NetBIOS, due to one server needing it_
      - _Going to have to get rid of the system, eventually_
  - Collaborative effort to import IPAM data from CSV to Infoblox
  - Any issues with DNS/DHCP today?
    - _Protocol slowness_
  - **Proposed Design Diagram** from Infoblox
* Where
  - Remote, no tasks in Description of Services denote on-site
  - How would remote access be provided to customer environment?
    - _All through Teams & fileshare_
    - Class uses tempoary Dropbox for "customer" file exchange
* When
  - 2 days PM + 5 days consult
  - When is desired go-live date?
    - _By end of November_
  - When are available change windows?
    - Change control process & timelines?
    - _Change control meetings on Wednesdays, CAB the Wednesday before change_
    - _Friday 7PM - Monday 3AM Eastern_
    - _Other changes will be concurrent, every weekend_
* Why
  - Background not discussed in SOW
    - _AD improvement/consolidation project, wiped out DNS, wanting to de-couple_
  - History of AD DNS/DHCP environment?
    - _Inheirted existing system from multiple generations of staff_
  - What is driving migration to Infoblox?
  - Expected outcome of migration to Infoblox?
  - Existing Infoblox architecture?
    - _VMs, not stood up yet_
  - Admin rights/ability to change AD & network infrastructure?
    - _Rob has AD admin_


Does not give idea of existing systems & data

Customer should be encouraged to do to training before engagement
