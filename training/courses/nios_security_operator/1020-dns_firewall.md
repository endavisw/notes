# 1020: NIOS DNS Firewall Monitoring and Reporting

## 1020-01: NIOS DNS Firewall Overview

DNS Firewall is Infoblox's implementation of Response Policy Zones (RPZ)

RPZs apply policies

Curated list of known-malicious domain names
* Apply policy actions
* Block, redirect

All blocked communications are sent to Infoblox reporting
* Infoblox reporting & analytics
Client's IP, MAC, device type

## 1020-02: NIOS DNS Firewall Security Dashboards
Dashboards > Status > Security

"Security Status" dashboard
At least one member needs RPZ Threat Protection or Threat Analytics

Status for grid as a whole, and per member
Green - OK
Yellow - Warning
Red - Critical problems

Thresholds in widget - gear in right-hand corner
* Blocked, substitute, passthru counts for Warning & Critical

### Default Thresholds
| Status   | Threshold | Grid Status | RPZ Status    |
| -------- | --------- | ----------- | ------------- | 
| OK       |           | < 10 events | < 10 RPZ hits |
| Warning  | 10	       | >= 10 < 100 | >= 10 < 100   |
| Critical | 100       | >= 100      | >= 100        |

Links to RPZ & Members config pages. Enable/disable auto-refresh period

### RPZ Status Widget
* Top 10 Grid Members
* RPZ Recent Hits
    * Up to 10 unique clients
    * Requires RPZ logging enabled
* Trend tab, RPZ statistics
* Health tab, latest feed updates
* Config gear top right corner
    * Graph Config:
        * Client hits
        * Passthrough hits
        * Blocked hits
        * Substituted hits
    * Graph Type
        * Stacked Diagram
        * Line Diagram
    * Auto Refresh Period (10 default?) seconds

### RPZ Status for Members widget
* Similar to grid-wide multi-member widget
* Limited to one member
* RPZ Recent Hits tab (rpz logging required), Trend tab, Health tab
* Link to syslog


## 1020-03: Reporting Server DNS Firewall Dashboards and Reports

Reporting Server gives access to additional reports than GM dashboard
* Real time & historic data

### Enabling Reporting Server
* Administration > Reporting > Grid Reporting Properties > Basic > Security (ib_security index)
* ~ 10 minutes for DNS RPZ events to appear in Reporting Server
* Required to enable RPZ syslog to Reporting Server

### Reporting Alerts
* Email, SNMP trap, log message
* Pre-defined alert:
    * si-search-dns-rpz-hits
    * Triggers as soon as RPZ events occur

### Dashboard and Reports
* DNS Firewall Executive Threat
    * Pre-defined custom report which consists of multiple sub-reports
    * High-level overview of client activity
    * Malicious Activity by Client (bar chart)
        * Number of RPZ hits for top clients
    * Malicious Activity by Client (table)
        * Details of hits, default up to 10 domains/client
    * Top DNS Firewall Hits (table)
        * Rule, percentage, number of hits, description
    * Time, Top N clients/rules, Top N domains, Hit count
    * Bottom left: search, create new reports, export (csv, xml, json), inspect/refresh
* DNS RPZ Hits Trend by Mitigated Action
    * "Provides trends for the total number of RPZ hits for each mitigation action together with the total client hits for a given time frame"
    * Time, Block, Passthru, Substitute, Client Hits
* DNS Top RPZ Hits
    * Time, top N, client, domain name, dns view
    * Members, mitigation action, rpz zone, rpz entry, severity
    * Filter on mitigation action
    * Show aggregated hits count or details
    * Click on client for IP/DHCP/user info
    * Chart View * report executed every 10 minutes
        * "lists top clients who received ??? responses through RPZ"
        * "displays the total client hits & total rule hits over a given time frame"
        * Total Client Hits
        * Domain Name
        * RPZ Entry
        * RPZ Severity
        * Total Rule Hits
        * Mitigation Action
        * Substitute Addresses
        * Time
    * Detail View
        * Client ID (IP address)
        * Total Client Hits
        * RPZ Entry
        * RPZ Severity
        * Total Rule Hits
        * Mitigation Action
        * Substitute Address
        * Time
* DNS Top RPZ Hits by Clients
    * "list the total number of RPZ hits from a client during an interval"
    * Chart: Client ID (X axis), Total Client Hits (Y axis), Time
    * Table: Client ID (IP address), Total Client Hits, Time
    * Clients that have the most total counts of malicious activities that triggered the RPZ
* Malicious Activity by Client
    * "lists the clients that have the most malicious activities"
    * Client ID (X), number of hits (Y), Domains, last active time
* Top DNS Firewall Hits
    * "lists the top RPZ rules triggered over a given time frame"
    * RPZ Rules, Percentage (of hits), Number of Hits, Description
* DNS Top NXDOMAIN / NOERROR (no data)
    * Filter on time, Top N, Members, DNS View, Reply Type
    * Bar Chart, Table, Both

Administration > Reporting > Grid Reporting Properties > DNS> DNS RPZ Rule Hit Configuration
* Top N Limit (default 100?)
* Top RPZ Entries per Client (default 10?)
