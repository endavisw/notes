# Exporting and Importing with BloxOne DDI

## Overview

### What can we do with Imports and Exports?
* Systems planning
    * Download exported objects in a JSON or CSV file for coordination and verification with other administrators and teams
* Bulk changes
    * Modify an exported file to add, delete, or modify the objects for re-import
* Migration and duplication
    * Migrate and copy data to a different user account in the Cloud Services Portal
* Automation
    * Use an exported objects file as a basis for REST WAPI and API calls for UI-free changes

### Supported File Formats
* JSON
    * Simple, compact, easy to read
    * Object-oriented
    * Hierarchical structure
* CSV
    * Infoblox specific format
    * Formatted different from standard CSV
    * Includes Header row to define object variables

### Supported Objects
* On-Prem objects
    * Hosts
    * Host Configs
* IPAM/DHCP objects
    * IP Spaces
    * Subnets
    * Ranges
    * Fixed Addresses
    * DHCP Options
    * More...
* DNS Data objects
    * Authoritative Zones
    * Forward Zones
    * Records
    * DNS Server configuration
    * DNS Views
    * TSIG Keys
* Threat Defense objects
    * Application and Category Filters
    * Endpoints and Endpoint Groups
    * External Networks
    * Internal Domains
* NTP Service Configuration
* Tags

## Import/Export Job Manager

Administration -> Data Import/Export
* Switch between import and export history views
* Launch import and export wizards

Each view will show history of events, who ran them, and when, and status if job failed or completed. Can also use search fitler.

### File Selection
Select file type for export
* JSON
* CSV

Select file for import
* Supported file types: JSON, CSV (750MB or less)

### Object Selection
Can select object types for export

When importing, can select which object types to import, if file has more types than currently desired for importing

### Import Options
Specify the type of import
* Add new records from the imported file
* Add new records and update existing records
* Add new records, update existing records, and delete all records not in the imported file
* Add new records and delete all records not in the imported file
* Update existing records and delete all records not in the imported file
* Delete all records not in the imported file
* Delete all records in the imported file

### Error Handling
(Import) Specify error handling
* Skip failed records and continue import
* Stop import if any record fails

(Export) Specify error handling
* Skip failed records (continue export)
* Stop export if any record fails

### Import/Export Job Manager
Will see an alert that the process started and status will be displayed in the Job Manager

Once job has finished, details can be examined to determine why it may have failed. Click arrow to expand

Verify export completed and download exported files
