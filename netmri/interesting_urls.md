# NetMRI Interesting URLs

# WebUI

### Database Stats
`https://{{server}}/webui/settings/database_statistics.json`

Example
```json
{
    "stats": [
        {
            "data_sum": 351825663,
            "index_sum": 275817839,
            "total_sum": 627643502,
            "server_time": "2024-07-19 16:15:52"
        }
    ]
}
```

# WebUI Grids

"Network" is a common attribute to select and reduce payload size.

The JSON "total" key is often the most useful for these.

### Endpoint Count
`https://{{server}}/webui/grid_data/end_host_network_explorer_inventory_summary_grid.json?limit=1&select=Network`

Example
```json
{
    "rows": [
        {
            "Network": "Default"
        }
    ],
    "total": 19,
    "updated_at": "2024-07-19 16:24:01",
    "status": 200,
    "columnstotal": {},
    "message": "Success"
}
```

### Unique Route Destination Count
`https://{{server}}/webui/grid_data/device_route_network_explorer_summaries_section_grid.json?limit=1&select=Network`

Not the same as the total from `/api/{{ver}}/device_routes/index.json`, as those
are individual instances of routes on devices.

Example
```json
{
    "rows": [
        {
            "Network": "Default"
        }
    ],
    "total": 64,
    "updated_at": "2024-07-19 16:12:01",
    "columnstotal": {}
}
```

### Unique Routes
`https://{{server}}/webui/grid_data/device_route_network_explorer_summaries_section_grid.csv`

This appears to be generated at intervals and may not be as complete as `/api/{{ver}}/device_routes/index.csv`

Example
```csv
Proto,Route,Network View,Count
local,ff00::/8,Default,4
local,192.168.10.0/24,Default,3
local,::/0,Default,3
local,169.254.1.0/24,Default,1
local,0.0.0.0/0,Default,3
local,fe80::/128,Default,1
local,fe80::/64,Default,1
netmgmt,fe80::1/128,Default,1
netmgmt,fc00::/7,Default,1
netmgmt,0.0.0.0/8,Default,1
```

### Unique Subnet Count
`https://{{server}}/webui/grid_data/subnet_network_explorer_summaries_section_grid.json?limit=1&select=Network`

Not individual instances of subnets (locally-connected routes).

Example
```json
{
    "rows": [
        {
            "Network": "Default"
        }
    ],
    "total": 4,
    "updated_at": "2024-07-19 16:22:03",
    "columnstotal": {}
}
```

### Unique Subnets
`https://{{server}}/webui/grid_data/subnet_network_explorer_summaries_section_grid.csv`

This appears to be generated at intervals and may not be complete.

Example
```csv
Subnet,Network View,Vlan,VLAN ID,Root Bridge Address,Count
169.254.1.0/24,Default,,,,1
192.168.10.0/24,Default,default,1,50:61:BF:7F:9D:80,19
fe80::/64,Default,,,,4
```

### Unique VRF Names Count

Does not have "Network" attribute.

`https://{{server}}/webui/grid_data/vrf_network_explorer_summaries_section_grid.json?limit=1&select=Count`

Example
```json
{
    "rows": [],
    "total": 0,
    "updated_at": "2024-07-19 16:36:41",
    "columnstotal": {}
}
```

### "Network Pending" Device Group Members
`https://{{server}}/webui/grid_data/device_network_explorer_inventory_summary_grid.json?GroupID=21`

The `GroupID` for Network Pending _should_ always be 21 since it's a stock group.

Example from a different group to show available fields
```json
{
    "rows": [
        {
            "DeviceID": "1",
            "Collector": "netmri",
            "DeviceIPDotted": "192.168.10.132",
            "DeviceIPNumeric": "3232238212",
            "VirtualNetworkID": "1",
            "Network": "Default",
            "DeviceName": "netmri",
            "DeviceType": "NetMRI",
            "DeviceVendor": "Infoblox",
            "DeviceModel": "Campus",
            "DeviceAssurance": "99"
        }
    ],
    "total": 20,
    "updated_at": "2024-07-19 16:48:17",
    "status": 200,
    "columnstotal": {},
    "message": "Success"
}
```

# API

### Device Group Membership Counts

"select" parameter does nothing

`https://{{server}}/api/{{ver}}/device_groups/get_group_tree.json?use_hide_empty_groups_option=false&count_ungrouped_devices=true&advanced_groups_only=false`

Example
```json
{
    "device_group_tree": [
        {
            "AdvancedGroupInd": true,
            "Criteria": "$Assurance >= 20\n\t\tand $Community ne \"\"\n\t\tand $Type in [\n\t    \t\"Application Switch\", \"Solaris\", \"IBM AS/400\",\n\t    \t\"IBM OS/390\", \"Linux\", \"VAX\", \"IBM AIX\",\n\t    \t\"Digital Unix\", \"HP-UX\", \"IRIX\", \"FreeBSD\",\n\t    \t\"NetBSD\", \"NextStep/OpenStep\", \"OpenBSD\", \"UnixWare\"\n\t\t]",
            "DeviceGroupDefnID": 16,
            "GroupID": 16,
            "GroupName": "App Servers",
            "MemberCount": 8,
            "Network": "Lab",
            "ParentDeviceGroupID": 0,
            "Rank": "1131529406376837120",
            "HasPermission": true,
            "leaf": "true",
            "HasIssuesSuppressPrivilege": false
        },
        {
            "AdvancedGroupInd": true,
            "Criteria": "$Assurance >= 20 and $Community eq \"\" and $Type in [\"MacOS\",\"Windows\"]",
            "DeviceGroupDefnID": 19,
            "GroupID": 19,
            "GroupName": "Workstations w/o SNMP",
            "MemberCount": 0,
            "Network": "Lab",
            "ParentDeviceGroupID": 0,
            "Rank": "1128151706656309248",
            "HasPermission": true,
            "leaf": "true",
            "HasIssuesSuppressPrivilege": false
        }
    ],
    "device_total": 20
}
```

## Discovery Statuses

Contents from the Network Explorer -> Discovery tab; these aren't possible with
the InfobloxNetMRI module due to limitations in the Broker model.

A value for the "filename=" parameter doesn't need be included, but the parameter
itself (_and_ "=") need to be present. Filenames are default ones given by the
NetMRI UI.

"problems", "non_reached", and "current appear" to be subsets of "licensed"

### License Management

`https://{{server}}/api/{{ver}}/discovery_statuses/static/licensed.csv?filename=license_management.csv`

Example
```csv
DeviceID,DeviceIPDotted,VirtualNetworkID,VirtualNetworkName,VirtualNetworkMemberArtificialInd,DeviceIPNumeric,DeviceName,DeviceType,StatusDetail,LicenseOverride,FirstSeen,DeviceFirstOccurrenceTime,LastSeen,FingerPrintEnabled,SDNCollectionEnabled,SNMPCollectionEnabled,ConfigCollectionEnabled,CLICredentialMessage,CLICredentialStatus,CLICredentialTimestamp,ConfigCollectionMessage,ConfigCollectionStatus,ConfigCollectionTimestamp,DeviceGroupMessage,DeviceGroupStatus,DeviceGroupTimestamp,ExistsMessage,ExistsTimestamp,FingerPrintMessage,FingerPrintStatus,FingerPrintTimestamp,SDNCollectionMessage,SDNCollectionStatus,SDNCollectionTimestamp,LastAction,LastTimestamp,ReachableMessage,ReachableStatus,ReachableTimestamp,SNMPCollectionMessage,SNMPCollectionStatus,SNMPCollectionTimestamp,SNMPCredentialMessage,SNMPCredentialStatus,SNMPCredentialTimestamp,RuleListAnalysisMessage,RuleListAnalysisStatus,RuleListAnalysisTimestamp,UnitID
1,192.168.10.132,1,Default,1,3232238212,netmri,NetMRI,Unlicensed,2,2021-12-08 17:45:07 -0500,2021-12-08 17:45:07 -0500,2024-07-19 17:23:49 -0400,0,"",1,0,"","","",Config Collection: Not licensed,Skipped,2024-07-15 03:00:00 -0400,Device Groups: Successfully assigned to device groups,OK,2024-07-19 17:24:11 -0400,Exists: Device exists / Source: NetMRI,2021-12-08 17:45:07 -0500,"","","","","","",Device Groups: Successfully assigned to device groups,2024-07-19 17:24:11 -0400,Reachable: Successfully reached / Source: SNMP,OK,2024-07-19 03:26:49 -0400,SNMP Collection: Failed to collect data / Table: Inventory,OK,2024-07-19 03:26:49 -0400,SNMP Credentials: Successfully authenticated / Version: SNMPv3,OK,2024-07-19 03:26:49 -0400,"","","",0
```

### Recent Activity

`https://{{server}}/api/{{ver}}/discovery_statuses/static/current.csv?filename=recent_activity.csv`

### Recently Unreachable

`https://{{server}}/api/{{ver}}/discovery_statuses/static/non_reached.csv?filename=non_detected_ips.csv`

### Problems

`https://{{server}}/api/{{ver}}/discovery_statuses/static/problems.csv?filename=?filename=problems.csv`


## Subnets from Routes

"Subnets" are just a subset of "Routes". Per Rajiv Nair of Infoblox:

```
Admin/Oper state is considered when a learned route is converted as a discovered Subnet in NetMRI. The following is a copy/paste from a knowledge base article that lists some criteria for a route to be considered as subnet for IPAM Sync push

1. From the Network Explorer > Summaries > Routes table: A managed device must have a route for the subnet in question, for the subnet to be created.
2. The Route must be protocol "local" and type "direct."
3. From the Network Explorer > Inventory > Interface > Interface Config table:
   A managed device must have AT LEAST one interface in this subnet.
4. The interface must be "admin up" AND "oper up"

In a few corner cases, A network device may have a local but indirect route. For example, the route was previously routed on an interface which is now reconfigured as a switchport, such routes are also not qualified as Subnets. Static routes are also excluded from this consideration."
```

A quick & dirty way would be to look for routes with `RouteProto` value of 
"local" and `RouteType` value of "direct":
```python
route['RouteProto'] == 'local' and route['RouteType'] == 'direct'
```

May be beneficial to bolster that data by generating networks using `ifIPDotted`
and `ifNetMaskDotted` from `if_addrs` for a fuller list of locally-connected
networks.