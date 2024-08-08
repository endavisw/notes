# 2900 - CSP Overview for BloxOne
## Scenario

You have been given administrative access to the Infoblox CSP and you will be expected to start deploying BloxOne hosts to service your DDI and Threat Defense needs. You have some time to get familiar with the interface so that you can plan your work. This process should help you become more familiar with the CSP interface and the location of commonly used elements for configuring your network services.

Estimate Completion Time
* 10 to 15 minutes

Prerequisites
* Administrative access to the BloxOne CSP

Course References
* 1110 BloxOne CSP Overview

## Tasks

To complete this lab, you need to perform the following tasks.

1. Log onto the BloxOne CSP
2. Locate and configure your own user profile
3. Locate the Dashboards and Help panel
4. Locate the Infrastructure configuration section
5. Locate more configuration sections

### Task 1: Log onto the BloxOne CSP

First, check the current status of the Cloud Service Portal (CSP) and scheduled maintenance of the BloxOne platform at status.infoblox.com.Then, log in to the BloxOne CSP web interface using the Infoblox lab-provided tenant information.

### Task 2: Locate and configure your own user profile

Locate your user configuration section. Change the Time Zone to something appropriate. Change the Dark/Light mode to your preference.


### Task 3: Locate the Dashboards and Help panel

Locate the Dashboards to confirm the initial statistics. Open the context-sensitive Help panel while you're here to see helpful information about the Dashboards panel.

You can pin the Help panel to your workspace, and it will update its content based on which menu and sub-menu you are working with. Look for the 
icon.

### Task 4: Locate the Infrastructure configuration section

You'll soon be adding BloxOne Hosts and Services to the network. Locate the Infrastructure section where we will be configuring these. Find the Hosts, Join Tokens, and Services sub-menus.

### Task 5: Locate more Configuration Sections

There are a few other sections in the CSP we need to be familiar with. Locate the configuration sections listed in Table 2900-1 below.

Table 2900-1

| UI Section           | Purpose                                                                                            |
| -------------------- | -------------------------------------------------------------------------------------------------- |
| Downloads            | Download software for Endpoints and Hosts                                                          |
| Data Import / Export | Import and Export network objects for migration, maintenance, or bulk changes                      |
| Recycle Bin          | Manage deleted objects                                                                             |
| User Access          | Configure user accounts and roles for network administrators and operators                         |
| Security Policies    | Define sets of rules and actions for access and network security                                   |
| Address Spaces       | Create and navigate the network definitions used by DHCP services and IP Address Management (IPAM) |


## Solutions

### Task 1 Solution: Log onto the BloxOne CSP

1. It's good practice to check in on the latest maintenance updates and status of the BloxOne Cloud platform. Open a web browser in your lab or any internet-connected web browser and go to status.infoblox.com. Expand the BloxOne DDI tab to see if CSP is operational

2. Log into the BloxOne CSP web interface using the Infoblox lab-provided tenant information. Go to csp.infoblox.com - This may forward automatically to auth.infoblox.com for the login portion.



### Task 2 Solution: Locate and configure your own user profile

1. Navigate to your username, on the bottom-left above the User Agreement. In this example, the username is BloxOne User, so we'll go to BloxOne User → User Profile

2. In the User Preferences sub-menu is Account Preferences. Adjust the Time Zone if you like, and use the Dark/Light mode toggle to see what you prefer. Click Save when you are done.


### Task 3 Solution: Locate the Dashboards and Help panel

1. On the main menu, select Dashboard. Since we have no services configured yet, there will likely be No Data.
2. On the main menu panel, select Help
3. We can lock this Help panel in place by selecting the icon.
4. Notice how the content of this panel will change as we move between main menus and sub-menus.
5. Un-Pin the panel with the icon if you want more space on the screen for the other panels.

### Task 4 Solution: Locate the Infrastructure configuration section

1. On the main menu panel select Manage → Infrastructure
2. Click each submenu to see where we will need to configure our Hosts, Join Tokens, and Services. If you have not deployed any Hosts or Services yet, there will be no results listed.

### Task 5 Solution: Locate more Configuration Sections

1. Downloads is where we will go to get software for Endpoints and Hosts. In a future lab, this is where we will get our BloxOne Host container package. Go to Administration → Downloads.

2. You may need to Import data for migration, maintenance, or bulk changes. Go to Administration → Data Import / Export.

3. Recycle Bin is where objects go when we delete them. Go to Administration → Recycle Bin or click on Recycle Bin at the bottom left of the main menu panel.

4. User Access is where we will go to configure other user accounts and roles for our network administrators and operators. Go to Administration → User Access.

5. Security Policies is where we will go to define sets of network security rules and actions for access and constraints. Go to Policies → Security Policies.

6. Address Spaces is where we create and navigate the network definitions used by our DHCP services and IP Address Management (IPAM). Go to Manage → IPAM/DHCP and click on the Address Spaces sub-menu.
