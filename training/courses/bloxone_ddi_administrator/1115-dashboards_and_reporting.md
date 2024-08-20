# Dashboards and Reporting

## Dashboards and Widgets
### What is the Dashboard?
Overview of the overall health of services

Widgets
* Snapshots of the high-level traffic
* RPZ hits per policy
* DNS and DHCP status
* License Utilization

Widgets provide customizable views of data

### Widgets
Widgets are the **building blocks** of your Dashboard

* Add and Remove to customize a Dashboard
* A Dashboard is empty by default. You must add the Widgets you want
* Later, the dashboard will be populated with the widgets you last had open

### Dashboards
* Security
* DDI
* License Utilization

Widgets are relevant to Dashboard context

* Contextual Widgets
* Download as PDF

### Managing Widgets
To add a new Widget to the Dashboard, click the + (plus) symbol next to the icon for the widget that you want.

To close a Widget and remove it from the Dashboard click the **X** on the top right-hand corner of the widget you want to close.

* **Refresh every** - Set refresh frequency
* **View Last** - Set time range
* **Download as PDF** - Dashboard snapshot

### Dynamic Chart Updating
* X and Y Axis tooltip with data summarization
* Dataset toggling

Chart types:
* Vertical bar
* Line
* Donut
* more...


## Configuring and Using Notifications
### Notifications

CSP displays notifications for specific events, such as:
* License expiration
* CPU usage exceeding thresholds
* etc

**Bell** icon indicator; **Manage -> Notifications**

Account Notifications, Service Notifications

* Filter by severity
* Delete All
* Search
* View Integrations

### Notification Settings - Delivery
Controls delivery of Notifications to Groups and Service Integrations

Email and In-App (banner) notifications

Webhook Notifications for Service Integrations

Global administrators (email & in-app)
* Account Notifications
* DDI Metrics
* Host Notifications
* Service Notifications
* New Release Notifications

Service Integrations (webhook)
* Account Notifications
* DDI Metrics
* Host Notifications
* Service Notifications
* Upcoming Release Notifications
* New Release Notifications
* Dossier, if Dossier is being used

### Notification Settings - Thresholds
Set Thresholds for alerts and notifications
* On-Prem Host CPU, Disk, and Memory usage
* Inactive On-Prem Hosts
* Services Down

Host Notifications
* Host CPU Usage
    * CPU usage is over _x_ %
* Host Memory Usage
    * Memory is more than _x_ % used
* Host Disk Usage
    * Root file system is more than _x_ % used
* Host Inode Usage
    * Inode isage is over _x_ %

Service Notifications
* Host State
    * In 'Review Details' for more than _x_ seconds
* Last Host Activity
    * No activity detected for more than _x_ seconds
* DNS Service Status
* NTP Service Status
* DHCP Service Status
* CDC Service Status
* AuthN Service Status
* Anycast Service Status


## Working with the Reporting System
### Reports
BloxOne DDI uses Infoblox Reporting & Analytics to automate the collection, analysis, and presentation of data.

**Reports** on menu panel. Available reports depend on license.

**BloxOne**
* DHCP Reports
    * Total DHCP Leases Report
    * Top DHCP Clients Requests Report
* DNS Reports
    * Total DNS Responses
    * Total DNS QPS
    * Total DNS Queries Report

**B1TDC**
* DNS Activity
* Security Activity
* Summary Reports
* Lookalike Domains

### Report Configuration
* **Show All** servers
* **Filter** specific servers
* Duration
* Export to CSV
