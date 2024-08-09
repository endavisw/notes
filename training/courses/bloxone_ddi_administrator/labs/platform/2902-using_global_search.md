# 2902 - Using Global Search in BloxOne

## Scenario

You now have some network objects in your deployment, but you need to make a few changes to them. For instance, there is an APJ address block to name, you are moving the LATAM Branch network over to a new US-West Branch network, and the EMEA team moved a printer so that the Fixed Address MAC address you used has changed. Rather than drilling into sub-menus, you decide to use the Global Search feature as you want to find these objects quickly to make changes.

Estimate Completion Time
* 10 to 15 minutes

Prerequisites
* Administrative access to the CSP
* Lab 2901 BloxOne Exporting and Importing

Course References
* 1109 BloxOne Global Search

## Tasks

To complete this lab, you need to perform the following tasks.
* Find and update the objects listed in Table 2902-1

### Task 1: Find and update the objects

Using Global Search, find the objects in  Table 2902-1 and make the required change action. Global Search query syntax rules are at https://docs.infoblox.com/space/BloxOneDDI/186400787


Table 2902-1
| Object Name                   | Object Type               | Action                                  |
| ----------------------------- | ------------------------- | --------------------------------------- |
| 172.31.134.0/23 Address Block | IPAM/DHCP: Address Block  | Set the name to East Networks           |
| LATAM Branches                | IPAM/DHCP: Address Block  | Rename to US-West Branches              |
| EMEA Branch Printer 1         | IPAM/ DHCP: Fixed Address | Change MAC address to 89:35:B2:24:CB:92 |

You can edit the found objects from the search results list


## Solutions

### Task 1 Solution: Find and update the objects

There are several ways to go about searching for objects. We can use whatever information we have available and narrow it down to our selections. We can filter by object type if our search terms are too general. We can use wildcards, dates and regular expressions too! Here are some solutions to use as ideas but try to search in your own creative way.

1. Click on the Global Search button on the top right of any screen. You will get a search entry field at the top of the screen. Your most recent searches will be listed under it.

2. First on our list is to set a new name for the 172.31.134.0/23 address block. There are a few ways to accomplish this, but one way is to just start typing what you're looking for. Use _172.31.134_ as the search text. The search will start automatically as you type.
     
3. We can see there are two results. We can see an Address Block and a Subnet. We don't need to use View All Results yet, because these are the only two results and filtering may not be necessary. Since it's the full /23 address block we want to name (which includes 172.31.134.0/24 and 172.31.135.0/24), we can hover over our cursor the result and click **Edit**.

4. This brings us to the configuration for this Address Block. We can set the empty name as East Networks and click **Save & Close**.

5. Next, we need to move the LATAM Branch networks over to the non-existent US-West Branch. For our purposes, we can just rename the Address Block. We can start by searching for _LATAM Branch_.

6. We see a few results. The search matches on _LATAM_ and _Branches_ so we see the other Branches as well. Click on VIEW ALL RESULTS to give us some more results and options.

7. We see a couple of object types, but we are only concerned with _Address Blocks_. We can filter out the other objects by checking the objects we want and clicking Apply Filter.

8. Now, we only have _Address Block_ results. Hover over the _LATAM Branches_ entry and click **Edit**.

9. Change the name from _LATAM Branches_ to _US-West Branches_ and click **Save & Close**

10. One last change on our list to make: Changing the fixed-address MAC for Printer 1 in the EMEA Branch. We have several bits of information, so let us search for _EMEA "Printer 1"_

11. Hover the mouse over the Fixed Address entry and click **Edit**

12. Change the _MAC Address_ field from _41:69:69:80:a3:d2_ to _89:35:B2:24:CB:92_ and click **Save & Close**
