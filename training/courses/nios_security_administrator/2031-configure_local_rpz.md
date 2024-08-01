# 2031: Configuring Local RPZ in NIOS

## 2031-01: Local RPZs

One of two types of RPZs available using NIOS DNS Firewall
* Provide local administrative control
* Block harmful domains, IP addresses, and hosts
* Redirect users to a walled garden
* Avoid false positives with allow-lists

RPZs are policy, written as specially-constructed DNS zone data
* Trigger = domain name pattern matching
* Action = block or allow (and more)
* Rules apply actions based on a trigger

Why use Local RPZ?
* RPZ feed provides generic threat intelligence
* Local RPZ is typically used for business-specific rules that are not covered by standard feed data

### Creating a Local RPZ
Data Management -> DNS -> Response Policy Zones -> Add

**Step 1:**

Add Local Response Policy Zone

**Step 2:**

Name - must conform to DNS syntax rules
* Recommended to indicate purpose

Policy Override - means overriding policy actions specified at the rule level
* Allows admins to overturn the actions of individual rules
* Log Only (Disabled)
* None (Given)
* Block (No Data)
* Block (No Such Domain)
* Passthru
* Substitute (Domain Name)

Severity - Enables you to get a Syslog severity level
* Critical, Major (default), Warning, Informational

Comment

Disable - disables policy while keeping the configuration

Lock - prevents unauthorized updates to the zone

Policy Override Comparison
| Override     | Syslog | Reporting | Action  |
| ------------ | ------ | --------- | ------- |
| Log Only     | Y      | N         | N       |
| Block        | Y      | Y         | Y       |
| Passthru     | Y      | Y         | N       |
| Substitution | Y      | Y         | Y       |
| None         | Y      | Inherit   | Inherit |

**Step 3 - Specify Name Servers:**

None

Use this Name Server Group
* Select Server Group

Use this set of name servers
* Select individual primary/secondary servers; can be external

**Step 4 - Extensible Attributes (optional):**

**Step 5 - Scheduling (optional):**

Schedule Change - Now (default), Later
* Start Date, Start Time, Time Zone, Your time


### RPZ Order
Policies applied sequentially
* First matched rule is applied
* New local RPZ added to the top

### RPZ Order Wizard
* Drag and drop items in the table to reorder the list, or
* Use arrows to move RPZ up or down in the list

### RPZ Best Practices
* Place local RPZs near the top for customized rules
* Use PASSTHRU for testing RPZs in the initial phase of implementation
* Use a non-existent TLD to name the local zone (.rpz)
* Configure RPZs on recursive server closest to clients

### Why Deploy RPZ Close to the Client?
* When RPZ is deployed closet to the clients, you can see which host triggered a violation
* If RPZ is enabled farther away from the client, through multiple layers of recursion, you only see the information of the next later of the DNS servers, not the original client


## 2031-02: Configuring RPZ Rules

### RPZ Policy Definitions
* RPZs are policies
* Policies are made up of rules
* Rules
    * Policy Triggers
        * Query source and destination can be examined
    * Policy Action
        * Evaluated before resolver replies to client

### RPZ Rule Actions
* Passthru - allow query resolved correctly, log if required
* Block (No Such Domain) - Return NXDOMAIN: domain does not exist
* Block (No Data) - Return an empty response: there is no record
* Substitute (Domain Name) - Replaces response with specified data
* Substitute (Record) - Replaces response with specified data

### Passthru Rules
* DNS Response forwarded to client without modification
* Used to create allowlist
    * Recommended to sort at top of policy sequence
    * Containing all corporate domains/networks
    * Alongside popular domains allowed to avoid false-positives
Can be used in troubleshooting since they also log and tag DNS queries

Passthru Domain Name Rule
* Triggered by query
Passthru IP Address Rule
* Triggered by response
Passthru Client IP Address Rule
* Triggered by client IP making query

Data Management > DNS > Response Policy Zone > (policy) > + icon > Passthru Rule

Adding Domain Name
* Name (incl. Zone), DNS View, Policy, Comment, Disable

Adding Domain IP Address or Client IP Address
* IP Address or Network (incl. Zone), DNS view, Policy, Comment, Disable 
    

### Blocking Rules
Used to create a deny- or block-list for domains, networks, or IP addresses
* Block (No Such Domain Rule) - Sends NXDOMAIN response
* Block (No Data) - Sends NODATA response

'NODATA' is not a flag in DNS. Client will receive a response with NOERROR flag, but with no records in it

Three types for each rule - by Domain Name, (Domain) IP Address, or Client IP Address

Data Management > DNS > Response Policy Zone > (policy) > + icon > Block (*) Rule

Adding Domain Name
* Name (incl. Zone), DNS View, Policy, Comment, Disable

Adding Domain IP Address or Client IP Address
* IP Address or Network (incl. Zone), DNS view, Policy, Comment, Disable 

Block rule for a domain does not include subdomains. '*.' can be used to match subdomains


### Substitute Rules
* Substitute Domain Name (Domain Name) Rule
* Substitute Domain Name (IP Address) Rule
* Substitute Domain Name (Client IP Address) Rule
    
Adding Domain Name
* Name (incl. Zone), DNS View, Policy, Substituted Name, Comment, Disable

Adding Domain IP Address or Client IP Address
* IP Address or Network (incl. Zone), DNS view, Policy, Substituted Name, Comment, Disable 
