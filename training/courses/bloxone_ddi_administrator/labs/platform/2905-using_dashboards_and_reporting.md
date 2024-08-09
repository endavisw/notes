# 2905 - Using Dashboards and Reporting in BloxOne
## Scenario

While you are planning out your new network, you want to make sure you can track and present data to senior management and your team. You want to customize your available Dashboards for your own needs, adding a few widgets and removing others. You also want to schedule a daily Executive Summary Report for the last 24 hours of data to be emailed to your CSP email address, but have it stop after 5 days.

Estimate Completion Time
* 10 to 15 minutes

Prerequisites
* Administrative access to the CSP

Course References
* 1115 BloxOne Dashboards and Reporting

## Tasks

To complete this lab, you need to perform the following tasks.

1. Remove unneeded widgets from the DDI Dashboard 

2. Rearrange DDI Dashboard

3. Rearrange Security Dashboard

4. Schedule Executive Summary Report

### Task 1: Remove unneeded widgets from the DDI Dashboard 

For your initial purposes, you only need to see _DNS Responses_, _DNS_ and _DHCP Status_, and _DHCP Leases Per Second_. Remove the rest of the widgets in the DDI Dashboard.

### Task 2: Rearrange DDI Dashboard

Rearrange the widgets in the DDI Dashboard so that _DNS Responses_ is next to _DNS Status_ and _DHCP Leases Per Second_ is next to _DHCP Status_. Also, set the refresh period to every 30 minutes.

### Task 3: Rearrange Security Dashboard

We like what we see in the Security dashboard, but we want to move the _Top Attackers_ information to the top.

### Task 4: Schedule Executive Summary Report

Schedule emailing of Executive Summary Reports for the last 24 hours of data for 5 days.

### Bonus Task: Delete or edit the scheduled Executive Summary Report

In the previous task, we scheduled the emailing of Executive Summary Reports for 5 days. Use the Scheduled Events manager to delete or edit this scheduled report.


## Solutions
### Task 1 Solution: Remove unneeded widgets from the DDI Dashboard 
1. Click on the main menu panel item **Dashboard**

2. Find the Dashboards dropdown item named _DDI_

3. Scroll down and click on the X for the widgets we are removing. Leave only the widgets _DNS Responses_, _DNS Status_, _DHCP Status_, and _DHCP Leases Per Second_.

### Task 2 Solution: Rearrange DDI Dashboard

1. While you are still at the _DDI_ Dashboard, scroll down and drag each widget to re-arrange them in the Dashboard. As you drag a widget, the other widgets will adjust on the screen. A dotted line will show up in the area where the widget will be dropped in when you let go of the mouse button.

2. Your end result may look something like this:

### Task 3 Solution: Rearrange Security Dashboard

1. Click on the main menu panel item **Dashboard**

2. Find the Dashboards dropdown item named _Security_

3. Like the previous Task, we want to move a widget to another location on the Dashboard. Scroll down to find _Top Attackers_ and drag it to the top of the dashboard

### Task 4 Solution: Schedule Executive Summary Report

Finally, we want to schedule a report to be sent to an email address on a regular basis, for a limited number of days. We want the last 24 hours of data in the report, and we want the report to be sent for only the next 5 days.

1. Navigate to **Reports → Summary Reports**

2. Since we are not exporting, we do not need to fill in the steps on this screen but we will click **Schedule**

3. Give the Scheduled Report a name: _Daily Executive Summary Report_

4. For _Email_, enter the email address you have been using to log in to the CSP. This will be forwarded to the email address you use for the lab.

5. Make sure _Executive Summary Report_ is selected

6. Use the dropdown to choose a time period for the report data. Select _24 hours_.

7. For _Occurs_, select **Daily**

8. **For Ends**, select the After radio checkbox and type 5 or use the arrows to set the number

9. Your scheduled report configuration should look similar to this:

10. Click **Save & Close**. Now, every day for 5 days you will get an email with a link to an Executive Summary Report with the last 24 hours of data. 

### Bonus Task Solution: Delete or edit the scheduled Executive Summary Report

We may not want to see this emailed report for the next 5 days as we work with our Infoblox Education labs, so let's delete this report.

1. Navigate to **Reports → Scheduled Events**

2. Find your scheduled report on in the report manager and check the checkbox for it. For details on the report, click the expansion arrow on the report item.

3. You can edit or delete this report from here by clicking **Edit** or **Delete**


4. If you are deleting the item, you will need to confirm the delete operation. Note the notification for successful deletion.
