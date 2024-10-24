# 4 - Data Migration

## Data Considerations by Type

Microsoft vs ISC

Standard DNS names caan include alphanumeric characters and dash.

MS allows almost anything.

MS DHCP will accept invalid characters for MAC addresses

## Performing Multiple Parsings

Sanity
- High level pass

Initial Lab
- Intensive, repetitive, optimize, document

Final Lab
- Quick and thorough
- Leverages Initial Lab testing

## Data Processing Flow
* DIW tool can parse and import to Grid
* Import to Grid and then do final data manip
* Export to CSV
* Test import the CSV to a lab Grid

## Using the Data Import Wizard (video)
- Walkthrough
- Import
- Export CSV
- Analyze

Build of 9.0.0 mentioning Lucent QIP and Nortel NetID is _wrong_.
Builds of 9.0.0 without those in the intial help are fixed.

**Do not close Log window**

Tool can modify session timeout, get customer OK before doing this.
**And change back when done**

MS tools now export to BIND format, so choose BIND rather than Microsoft for
legacy format.

Copy DNS config summary, very useful in report. In Log Window, which has "Save"
and "Clear" buttons.

named.conf import overview will show which zones have parse errors, in what
file and where in their file

If a zone has 'allow-update' and GSS-TSIG, that's how the AD export tool
denotes that a zone is AD integrated. _This is an indicator that dynamic_
_updates methodology should be discussed with the customer_
* May be easier to allow DHCP to update client records
* Remember: GSS-TSIG allows _clients_ to create to records!

Can select multiple zones and apply the same import options to them

Zones will turn green if they're able to be created in NIOS. The "Importable"
checkbox becoming unchecked shows it's fully been imported into NIOS.

Open Diff can be a useful tool for side-by-side comparisons to validate
- Kompare is on the lab machines

### Identifying Issues and Finding Missing Data

Fix it now or after go-live? Better now if possible
- Bring cleaner data in, easier validation

### Data Validation
Multiple ADCs will have different records due to being in multi-master mode
and each receiving different dynamic DNS updates

Zone transfer?

Compare DIW count to Grid count

Review DIW logs - some record drops **do not** count as failures!
- Error
- Failed
- Warn
- Alarm
  - Often duplicate PTRs from DDNS updates

**Customer needs record of illegal records that will be dropped**

Validate DHCP option types, especially Option 43 config

## When to Request New Data
After large material changes, after customer cleans up data, absolutely
before go live. **Do not go live with old data!**

## DNS Data Parsing/Conversion Process (video)
For each DC:
- CSV with metadata for all zones
- Generated named.conf for server - **Use this for DIW**
- Bind-load named.conf for loading to bind to attempt Zone transer
  - _This is incompete, only primary zones_
  - Do not use for DIW
- Zone TXT file - information about zone
- Zone DNS files - MS format? Has aging info, doesn't diff right
- Zone DB files - _Edit these when doing cleanup_

Import data from Primaries first. If a DC is Secondary for a zone for which the
Primary is also being imported, go with the Primary version

Advanced options - ignore RNAME (admin email) in SOA
- Likely going to be out of date/incorrect anyway

allow-tranfer = NStab
- MS proprietary, do not import

**Always save raw versions of customer data that won't be edited**

Common issues
- Illegal characters in RR names
- WINS
  - WINS record: forward
  - WINSR reverse
- Duplicate zones
  - Created as delegate but then try to import primary

Save DIW Log to file for easier parsing in text editor, grep, whatever


## DHCP Data Parsing/Conversion Process (video)
For each DC:
- CSV with metadata for all scopes
  - Leased used counts at time of dump
  - Useful for verifying active scopes after go-live
- netsh (deprecated) data dump
- XML data dump
  - This is what the DIW loads
  - Easier to change values in DIW rather than XML

Infoblox Fixed Address = Microsoft IP Reservation

Import format: Microsoft (XML)
- Microsoft (netsh) is deprecated and potentially incomplete

Can select __multiple__ XML files for import.
- May result in failures to import duplicate networks

Red options (top left) means MS using options in nonstandard way
- Useful for troubleshooting scope imports though

Most common version of options (ex, lease time), import to grid rather than
individual scopes. Override others as needed.

Want to change subnets' serving node to DHCP FO Group, rather than single member
- Subnets: add multiple members
- Ranges: after subnets, then can add FO group

Range without network is an error, need to fix.

Router outside of network is an error, need to fix.

DIW is "temporary" - moving to web-based portal
- MS XML format likely permanent
- Keep DIW around
  - Many customers require an airgap-capable tool like this.

## Keep the Project Docs Update
- Files
- Timing
- Exceptions
- Errors
- Process
- What informs what will need to be done/seen during go-live

## GSS-TSIG
- Each DNS Primary member **must** have its own Keytab file
  - Regenerate regularly due to customer org password policy
- Can only configure GSS-TSIG or IP based (w/ TSIG?) per zone

## MS Split Scope
DIW will import first server's exclusions only
- In reality, mutually-exclusive exclusions should be ignored
- IPs/ranges excluded by _both_ should be kept

Import DC1, export exclusions. Same for DC2. Compare exclusions

## DDNS
**DO NOT CHECK AD INTEGRATION on a zone**
- Auto creates some _ records/subzones
- Unchecking afterward torches all of that, breaks many things
- Manually create _tcp, _udp, etc stuff

## CSV Format for Exports
One File vs Many Files

One File
- Multiple headers
- Order MATTERS

Many Files
- Potentially less confusing
- Easy to locate errors
- Keep different versions of specific data
- Might be easier for customer to understand

CSV import CPU specs
- More cores, higher clock better
- Customer environment may differ, need to test against a standard host there

## Verification Tools/Methods
What does "the same" need to mean?
- NS changes
- DDNS differences between ADCs
- DHCP nameserver option changes
- Understand what _will_ be different!

## Commonly Seen Issues
* Some Arubas will DDNS with colons in records
  - Aren't working today in reality. Aruba config can change to dash
* MS supports DHCP FO, but probably not in use
* Clean up DDNS records or not?
  - Yes, they _should_ be recreated
  - Customer might have (critical?) services in the middle of DHCP ranges
  - In reality too dangerous
  - Just monitor queries to see what's stale

Compare # of As before to As & PTRs now.

## Lab Time
- Getting DIW up and running
- Parsing MS DHCP
- Importing MS DHCP
- Verifying MS DHCP
- Parsing MS DNS
- Importing MS DNS
- Verifying MS DNS

## Who's allowed to update zones?
- ADCs
  - Globally, or just main zone & underscore zones
- Key servers
- MS clustering
- Potentially _SRV targets
