# 1021: NIOS Threat Insight Monitoring and Reporting

### 1021-01: Threat Insight System and Audit Logs

Administration > Logs > Syslog
- Select Member
- Quick Filter: RPZ Incident
Threat Insight uses an RPZ to block Data Exfiltration & DNS tunneling

Logs are RPZ Incident logs, CEF format

mitigation.rpz

Audit logs can be used to view changes to Threat Analytics inside the Grid
- Changes to Threat Inside Allowlist, etc
- Administration > Logs > Audit Log


## 1021-02: NIOS Threat Analytics Dashboards

Dashboards > Status > Security
Status for security products licensed on the Grid
Security Status for Grid widget
- RPZ, Threat Analytics
- Status, Events, Definitions/Rules, Configuration Status
    * Events exceeding configured thresholds
    * Gear on top right-hand side
    * RPZ Thresholds:
        * Yellow >= 10 Blocked, 1 Substitute, 100 Passthru
        * Red >= 100 Blocked, 10 Substitute, 1000 Passthru
    * Threat Analytics Thresholds
        * Yellow >= 1 DNS Tunneling
        * Red >= 5 DNS Tunneling
Security Status for All Members widget
- All members supporting DNS security products
Threat Analytics Status for Grid
- Displays statistical information about DNS exfiltration events
- Detections Over Time tab:
    * Number of Detections (Y), Time (X) - line chart
- Top 10 Grid Members tab:
    * highest number of detections - bar chart
    * Top 10 grid members in descending order
- Detections tab: 
    * Table
    * More detailed information on tunneling events
    * Client IP Address, Domain, Timestamp, Module
    * Export table as CSV, icon on top right-hand corner blue arrow

"DNS Tunneling" is used to describe **all** exfiltration events


## 1021-03: Reporting Server Dashboards and Reports

Access to additional reports beyond standard GM dashboard

Longer than last 30 minutes

Historic data & real-time

### DNS Top Tunneling Activity
- List the clients that have the largest number of DNS tunneling activities over a given time
- Identify clients most often performing DNS tunneling activities
- Percentages relate only to the number of clients being displayed
- Filter on time, top N, members, source IP address, source port, NAT status, View
- Bar chart: Client IP, Event Count
- Table: Rule hits for Client IP = Category, Event Count, Last Seen
- Bar chart, table, or both
- Top N: 5 - 500
- Default: Top 10 clients over last week in  bar chart form
- Category: can include the type of DNS tunneling activity, as well as tunneling tools used to generate the activity
    * Short TTL
    * NXDOMAIN
    * High entropy domains
    * Iodine tool
    * Others
- Export dashboard as PDF or print
- Report: top 10, 25, 50; not as customizable as dashboard
    * Bar chart & table config only
    * Export to PDF or print
    * Custom time button

### DNS Tunneling Traffic by Category
- Provides information about DNS tunneling activities  by specific categories
- And the percentage of events by the category of DNS tunneling events in a given time frame
- Helpful in tracking abnormal DNS traffic
    * Visibility in the top categories of DNS tunneling activities
    * DNS tunneling, malware insertion, data exfiltration, anonymous IP traffic tunneling attempts
- Pie chart of DNS tunneling events
- Table Format
- Time, Top N, Members, View
- More data is visible with ADP / Threat Insight features
- Columns: Category, Events
- DNS Top Tunneling Activity Report = Detected by Analytics Engine
    * Columns: Client IP, Rule SID, Event Count, Rule Description, Last Seen
- Export to PDF or print
    
### Top Malware and DNS Tunneling Events by Client
- Lists clients that have the most number of outbound malicious queries, or RPZ hits, and DNS tunneling events in a given time frame
- Top infected clients making outbound malicious queries & associated with DNS tunneling activities
- Filter by: Time, Members, Source IP address, NAT Status, Source Port
- Table columns: Client IP, Total DNS Tunneling Events, Total Outbound Malicious Queries, Last Seen
Bar Chart also available