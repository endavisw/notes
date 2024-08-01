# 1022: NIOS Advanced DNS Protection (ADP) Monitoring and Reporting

## 1022-01: Accessing ADP Logs

### Controlling ADP Logging
- ADP continuously tracking when rules are triggered
- When a rule is triggered, ADP can be configured to log  this event
- ADP supports logging via: syslog, SNMP traps, Infoblox Reporting Server

### Events Per Second
- ADP does not write to syslog every time a rule is hit/events is triggered
- Events per second value controls logging of events to syslog and SNMP
    * 0 = no logging
    * \>0 = log up to n per second
    * 1 is default
    * Setting events/sec to high value can overload syslog
    * IB recommends increasing only for rules that require investigation

### ADP Syslog Messages
- Administration > Logs > Syslog
- Select Grid Member, Quick Filter: \[S\] - Threat Detection Events
- CEF format, same format as DNS Firewall
- Timestamp, severity, facility, process
- CEF Severities:
    * 8 - Critical
    * 7 - Major
    * 6 - Warning
    * 4 - Informational

### Configuring SNMP Traps
- Grid Properties (Basic) > SNMP
    * Enable SNMPv1/v2 Traps
        * Check + community string
    * Enable SNMPv3 Traps
    * Trap Receivers
        * Address, SNMPv3 User, Comment
- Grid Properties (Adv) > SNMP Threshold

|               | Trigger (evts/sec) | Reset (evts/sec) |
| ------------- | ------------------ | ---------------- |
| Flood Threats |                    |                  |
| Alert Rate    | 1 (default?)       | 0 (default?)     |
| Drop Rate     | 1 (default?)       | 0 (default?)     |


## 1022-02: NIOS ADP Dashboards

Dashboards > Status > Security
- Security will be an option if any security products are licensed
- Does not require Reporting Server
- Security Status for Grid
- Security Status for All Members

Threat Protection Status for the Grid widget
- Total events  by severity
- Top 10 grid members
- Events over time
- Top 10 Rules
- Top 10 Clients

Threat Protection Status for Members widget
- Monitor a single ADP appliance
- Same graphs as Threat Protection Status for the Grid, plus:
    * Traffic Usage Summary
    * Interface Usage Chart


## 1022-03: Reporting Server ADP Dashboards and Reports

### Reporting Server Indexes
- Confirm index categories:
    * DNS Query
    * DNS Performance
    * Security
    * System Capacity
    * System Utilization
- Confirm Reporting Service status
    * On Reporting Server member and ADP members

Reporting Server does NOT provide real-time view
- ADP appliances gather stats & logs, send to Reporting Server periodically
- Reporting Server indexes
- Data could be up to 15 minutes old

### Preloaded Dashboards (2)
- Threat Protection Event Count Dashboard
- Threat Protection Event Top Rules Logged Dashboard

### Preloaded Reports (8)
- Threat Protection Event Count by Category
- Threat Protection Event Count by Member
- Threat Protection Event Count by Member Trend
- Threat Protection Event Count by Rule
- Threat Protection Event Count by Severity Trend
- Threat Protection Event Count by Time
- Threat Protection Top Rules Logged
- Threat Protection Top Rules Logged by Source

### Dashboards
- Threat Protection Event Count
    * Separate data sets under one central dashboard
    * Threat Protection Event Count by Category - table
        * Attacks by threat category
        * Category
        * Critical Event Count
        * Major Event Count
        * Warning Event Count
        * Informational Event Count
        * Total Event Count
    * Threat Protection Event Count by Member - table
        * Attacks happening on each member in the network
        * Member
        * Critical Event Count
        * Major Event Count
        * Warning Event Count
        * Informational Event Count
        * Total Event Count
    * Threat Protection Event Count by Member Trend - line chart
        * Members targeted the most with DNS attacks
        * Event Count (Y), Time (X), Member (line)
    * Threat Protection Event Count by Rule - table
        * Attacks by predefined threat rules
        * SID
        * Category
        * Log Severity
        * Event Name
        * Alert Count
        * Drop Count
        * Total Event Count
    * Threat Protection Event Count by Severity Trend - line/area chart
        * Attacks categorized by severity
        * Event Count (Y), Time (X), Severity (area)
- Threat Protection Top Rules Logged
    * Threat Protection Top Rules Logged
        * Top rules hit by attacks
        * Rule (Y), Logged Event Count (X)
    * Threat Protection Top Rules Logged by Source
        * Lists top source IPs hitting threat rules
        * Source (Y), Logged Event Count (X)

### Reports
- Threat Protection Event Count by Category
    * Details of categories of attacks that have been logged
    * Bar Chart
        * Category (X / bars), Event Count(Y),
    * Table
        * Category
            * DNS Protocol Anomalies
            * ICMP
            * DHCP
            * Default Pass/Drop
            * NTP
            * General DDoS
            * etc
        * Critical Event Count
        * Major Event Count
        * Warning Event Count
        * Informational Event Count
        * Total Event Count
- Threat Protection Event Count by Rule
    * Details of the rules that have been trigged
    * And the number of times the rules were trigged
    * Each rule identified by a unique number (SID)
    * Bar Chart
        * Event Counts (Y), SID (X)
        * Bars:
            * Category
            * Log Severity
            * Event Name
            * Alert Count
            * Drop Count
            * Total Event Count
    * Table
        * Attacks by predefined threat rules
        * SID
        * Category
        * Log Severity
        * Event Name
        * Alert Count
        * Drop Count
        * Total Event Count
- Threat Protection Top Rules Logged by Source
    * Details of the top clients that have been triggering rules
    * Bar chart
        * Count (Y)
        * Source IP (X)
        * Logged Event Count, Top Rules, Last Active bars (… what?)
    * Table
        * Source IP
        * Event Count
        * Top Rules
        * Last Active

### Extending Reporting
https://community.infoblox.com

Discussing on reporting & ADP, reports created by other users
    

