# Configuring Threat Defense

## Admin course
Learned to:
* Configure B1TD Cloud
* Create security policies & custom lists
  - Apply them to On-Prem Hosts

## Configure Policies
Infoblox recommendation is to clone the default policy

Policies > Security Policies > Security Policies > Select Default Global Policy > Clone
* General
  - Name
  - Description
  - Precedence
  - Geolocation
  - Safe Search
  - Local On-Prem Resolution
* Network Scope
  - External Networks
  - DNS Forwarding Policy
  - Endpoint Groups
  - User Groups
  - IPAM
* Policy Rules
  - Default Action
* Bypass Codes
* Summary
  - Save & Close

Clone just needs a Name, then "Finish" on General tab goes to Summary, then Save & Close

## Custom Allow & Block Lists
Policies > Security Policies > Custom Lists > Create Custom List

After creating custom allow & deny lists, they can be attached to a Policy & moved to top of list

Allow lists are typically before Deny lists.

## Policy Order
Ordering of lists is possible within a Policy, the Production policy should be first the list

Policies > Security Policies > Security Policies > Select Policy > Edit Precedence
* 1 = top of list

Available on menu:
Policies > Security Policies > Security Policies > Select Policy
- Edit
- Edit Precedence
- Clone
- Remove

Organizations typically only have a few policies for a quickstart.
* Production
* Lab/Test

## Access
Bypass codes available, typically used by marketing depts
* Example: Facebook

## Category Filters
In use where policies need to be a little more restrictive, like guest wifi
