# Managing Role-Based Access Control

## RBAC Overview

### RBAC
* Define access policies
* Restrict service-related responsibilities to user roles and user groups
* The Cloud Service Portal provides quick-start defaults
    * User roles
    * User groups
    * Access policies
* Define service account users
* Assign service API keys

User access is based on roles and permissions, with the ability to define access polices.

### Configuration Workflow
To set up role-based access control
1. Create new users and assign them to respective user group(s)
2. Review the default user groups and create additional groups (if needed)
3. Review the default access policies and create additional access policies
4. Create new user roles if the predefined ones do not fit your organization needs


## Managing Users

**Administration -> User Access -> Users**

Information about portal users. Selecting a user will show details in the right pane.

Create, edit, and remove users

Export user list & reset passwords

### Creating and Editing Users
Click **Create User** or select the user and click **Edit**
* Name
* Type (New user only)
    * Interactive
    * Service
* Email (New user only)
* Group membership


## Managing User Roles
### User Roles
Defines the set of permissions or responsibilities of user

**Default User Roles** - depends on subscription and license entitlements

**Custom Roles** - Specific permissions

### Managine User Roles
**Administraiton -> User Access -> Roles**

* List of predefined roles
* Each of the user roles supports various permissions

### Creating and Editing Custom User Roles
Click **Create Role** or select a role and click **Edit**
* Name
* Description
* Features
    * Account
    * BloxOne TD
    * BloxOne Cloud
    * BloxOne DDI
    * BloxOne Service Edge

Features have permissions that can be granted/denied


## Managing User Groups
**Administration -> User Access -> User Groups**

A user group contains a list of users with identical access profiles

Create, edit, and remove groups. Choose group to be the default if new user's group is not specified.

Group details are showen in the right-side pane

### Creating and Editing User Groups
Click **Create User Group** or select group and click **Edit**
* Name
* Description
* Select users


## Managing Access Policies
### Access Policies
* Applies a specific role to a user group
* Role permissions applied to all users in a group
* Default access policies available for quick-start configuration

### Creating and Editing Access Policies
**Administration -> User Access -> Access Policies**

Access Policies list and information

Create, edit, remove policies. Policy details shown in right-side pane.

Click **Create Access Policy** or select a policy then click **Edit**
* Name
* Description
* Role
* User Group

### Authentication Profiles

Provides captive portal using third-party IdP (Identity Provider) federation to create security polices based on user groups
* LDAP
* SAML
* OpenID Connect

Create authentication profiles associated with on-prem hosts

Synchronize user groups from your IdPs

Build security policies based on user groups

### Create Authentication Profile
* Name
* Description
* State
    * Enabled / Disabled
* 3rd part IDP support
    * Microsoft Azure AD (now Entra ID)
    * Okta
    * OpenAM
