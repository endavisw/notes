# 2030: Describing NIOS DNS Firewall

## 2030-01: Infoblox DNS Firewall

DNS Firewall is Infoblox's implementation of RPZ

RPZ are a way for you to control what your queries can and can't look up using a recursive DNS server

DNS Firewall RPZ feeds are updated regularly with up-to-date Threat Intelligence. This is a curated list of known malicious domain names.

When an infected device is brought onto the network, it tries to communicate with its C2 domain using DNS

The DNS query from the infected endpoint is sent through the recursive DNS servers with the DNS Firewall feature enabled on them. The DNS server then checks the query against its policies, applying a policy action.

This can be administrator-defined action, such as:
* Blocking the communication for the endpoint
* Redirecting traffic to landing page or walled-garden site

DNS Firewall also reports on the malicious activity with Infoblox Reporting & Analytics. You can find out the user associated with the device attempting the malicious activity.  All blocked communications are sent to the Reporting Server, which can be turned into alerts. You can also find out the endpoint IP address, MAC address, and device type.


## 2030-02: Response Policy Zones (RPZ)

DNS Firewall is built using RPZs

RPZs are DNS zones containing policies that trigger actions
* Substituting data, such as changing A or AAAA records in response
* Redirecting traffic to landing page or walled-garden site
* Blocking the traffic by replying either with:
    * NXDOMAIN flag
    * NODATA flag
* Allowing traffic to pass through

Types of RPZ:
* Local - Policies and rules created locally
* Feed - Downloaded from BloxOne Threat Defense cloud (or other cloud services)

RPZs are configured on recursive DNS Servers, as close as possible to clients


## 2030-03: Setting up the grid for RPZ

Before we configure RPZ we need to:
* Verify NIOS version and DNS service state
* Apply RPZ License to Grid Members acting as DNS Servers
* Enable Recursive Queries on Grid Members acting as DNS Servers
* Configure RPZ Logging
* Configure Reporting Server (optional)

### Step 1 - Verification
* NIOS 6.6 or later
* DNS licensed and enabled on members hosting RPZ

### Step 2 - RPZ Licenses
* Applied per-appliance or Grid-wide
* Cannot be applied without DNS License

### Step 3 - Enable Recursion
* Can be enabled at Grid, Member, or DNS View level
* Make sure Grid Members are not open resolvers

### Step 4- Logging
* Enable additional logging at Grid or Member level
* RPZ logging is not enabled by default
* Syslog can be configured to send logging data to other systems

### Step 5 - Editing the Reporting Server (optional)
* Enable Security category in the index
* Correlate RPZ & IPAM data
* Compliance & audits are simplified
* Analytics based on Splunk

### RPZ Logs
Admin > Logs > Syslog > (member) > Quick Filter: "\[S\] - RPZ Incident Logs"

### Common Event Format (CEF)

CEF:0 - Version 0 of CEF
* Infoblox|NIOS|(version)
* RPZ-* - RPZ event
* Description - NXDOMAIN, etc
* Severity - number
* app=DNS dst=* src=* spt= view= qtype=(A, AAAA, etc)
* msg=(description of what happened)
* CAT= - category

What domain triggered policy, action taken, and rpz name

## Lab 251 - Setting up NIOS DNS Firewall

### Task 1 Solution: Apply the RPZ license to ibns1.techblue.net and ibns2.techblue.net

In this task, you apply the RPZ license file stored on the Jump-desktop. The license file is called RPZ.txt and is located in the /home/training/Documents/Licenses folder.
1. Navigate to Grid → Licenses.
2. Click the plus (+) symbol to add the RPZ license.
3. Select the Upload License File radio button. Click Select File.
4. Navigate to shared Drive/Licenses. Click the RPZ.txt file and select Open.
5. Click Save License(s).
6. Click Filter On and use a quick filter to search for RPZ licenses. There are two licenses associated with ibns1.techblue.net as this member is part of an HA pair.
    
### Task 2 Solution: Create RPZ Local NSG Name Server Group

In this task, you create the RPZ Local NSG Name Server Group. This name server group will be used by the local RPZs you create in a later lab. The members of RPZ Local NSG are ibns1.techblue.net and ibns2.techblue.net. You must choose DNS Zone Transfers for the Update Zones Using method. You get an error if you try and add a name server group with Grid Replication as the Update Zones Using method to an RPZ.
1. Navigate to Data Management → DNS → Name Server Groups.
2. Click the drop-down arrow next to the plus (+) symbol to add a new Authoritative Name Server Group.
3. Type RPZ Local NSG in the Name box. Click the drop-down arrow next to the plus (+) symbol and select Grid Primary.
4. Click Select.
5. Choose ibns1.techblue.net from the Member Selector. Click OK.
6. Click Add in the Add Name Server Group wizard to add ibns1.techblue.net to the RPZ Local NSG group.
7. Click the drop-down arrow next to the plus (+) symbol and select Grid Secondary.
8. Ensure Update Zones Using is set to DNS Zone Transfers. Click Select.
9. Choose ibns2.techblue.net from the Member Selector.
10. Click Add to add ibns2.techblue.net to the Name Server Group RPZ Local NSG. Click Save & Close to save the configuration.
    
### Task 3 Solution: Confirm DNS Recursion enabled on Name Servers
In this task, you confirm that DNS recursion is enabled for the name servers ibns1.techblue.net and ibns2.techblue.net.
1. Navigate to Data Management → DNS → Members.
2. Select ibns1.techblue.net and ibns2.techblue.net.
3. Click Start on the toolbar, and accept the notification message by clicking Yes.
4. Click the hamburger icon or the Edit button to edit the Member DNS Properties.
5. Select Queries in the Properties list.
6. Scroll down until you see Allow Recursion. You might need to click the Override button to enable recursion and select Named ACL Company Internal Subnets.
7. Repeat the steps to confirm that Recursion is enabled for ibns2.techblue.net then restart the services when prompted.
    
### Task 4 Solution: Enable RPZ Logging
In this task, you enable RPZ logging. This ensures that RPZ events are logged to syslog.
1. Select Grid DNS Properties from the toolbar. Toggle Advanced Mode. Select Logging.
2. Click the check box for rpz.
3. Click Save & Close.
4. Restart Services if prompted.

### Task 5 Solution: Confirm the Reporting Server Configuration
In this task, you check the Reporting Server Security category is enabled and has an index % value.
1. Navigate to Administration → Reporting.
2. Select Grid Reporting Properties from the Toolbar.
3. Ensure the Security Category is selected, and the Index% field has a value, you can leave as the default value.
4. Click Cancel or Save & Close.
5. Restart Services if prompted

From <https://docs.education.infoblox.com/lab/2541?inAppHelp=true> 


