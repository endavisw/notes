# Using SetDiscoveryCSV FileOp in with NIOS WAPI

The mechanism that NetMRI and Network Insight use to populate the "discovery 
data" attributes of an `ipv4address` or `ipv6address` (without a Record object)
is just a particular `fileop` WAPI call with a specially-formatted CSV.

Sounds easy on the surface, but diffcult to build a real workflow around.

Highly suggested to read [Hacking Infoblox Discovery for Fun and Profit](https://blogs.infoblox.com/community/hacking-infoblox-discovery-for-fun-and-profit-its-just-another-csv/) ([or here](https://www.dvolve.net/blog/2020/09/hacking-infoblox-discovery-for-fun-and-profit/)) before
continuing.

## NIOS Requirements

The `fileop?_function=setdiscoverycsv` function should be present in WAPI v2.9
(NIOS 8.3.0) or later. Earliest tested has been v2.11, corresponding to NIOS
8.5.0.

WAPI v2.11.1 (NIOS 8.5.1) is required for `fileop?_function=get_log_files` to
support `DISCOVERY_CSV_ERRLOG` as a valid value for `log_type`.

NIOS 9.0.6 is current at time of writing and 8.6.x has eached EoL.

## CSV Format

Various Infoblox documents make mention of Discovery Data fields:
* [Variables for Object Change Discovery Data](https://docs.infoblox.com/space/nios90/1375045306/Variables+for+Object+Change+Discovery+Data)
* [discovery_data struct](https://ipam.illinois.edu/wapidoc/additional/structs.html#struct-discoverydata)
* [ipv4dress search-only fields](https://ipam.illinois.edu/wapidoc/objects/ipv4address.html#search-only-fields-list)
* [ipv6address search-only fields](https://ipam.illinois.edu/wapidoc/objects/ipv6address.html#search-only-fields-list)

### Columns

NetMRI's IPAM Sync uses at least these columns:
- ip_address
- ap_ip_address
- ap_name
- ap_ssid
- bgp_as
- bridge_domain
- device_contact
- device_location
- device_model
- device_vendor
- discovered_name
- discoverer
- endpoint_groups
- first_discovered_timestamp
- last_discovered_timestamp
- mac_address
- netbios_name
- network_component_contact
- network_component_description
- network_component_ip
- network_component_location
- network_component_model
- network_component_name
- network_component_port_description
- network_component_port_name
- network_component_port_number
- network_component_type
- network_component_vendor
- os
- port_duplex
- port_link_status
- port_speed
- port_status
- port_vlan_name
- port_vlan_number
- tenant
- vrf_description
- vrf_name
- vrf_rd
- task_name

Other verified column names:
- device_port_name
- device_port_type
- device_type

Known non-working column names:
- mgmt_ip_address
  - discovery_gm_sorter will log this as an unknown field name in the header
    and ignore the column, at least in NIOS 9.0.6. Older versions not tried.
- last_updated_timestamp
  - discovery_gm_sorter will log this as an unknown field name in the header
    and ignore the column, at least in NIOS 9.0.6. Older versions not tried.

### Date Format
`first_discovered_timestamp` & `last_discovered_timestamp` are formatted
YYYY-MM-DD HH:MM:SS without timezone (`%Y-%m-%d %H:%M:%S` for strftime/strftime)
and assumed to be UTC/GMT/Zulu (based on web UI representation). Including a
UTC offset (ie, "-04:00") will cause discovery_gm_sorter to skip the row.

It is unknown if milliseconds are supported.

Example: "2025-05-14 12:12:05"

`first_discovered` & `last_discovered` are seconds after the epoch. Import with
these fields has not been attempted as the "_timestamp" versions are easier to
manually investigate/verify in the CSV.

### MAC Address Format

Colon-delimited, **all lower-case**. Any uppercase characters will cause the row
to be skipped with discovery_gm_sorter logging "Invalid data format for column
'mac_address'"

- Valid: `00:1f:12:57:9c:c0`
- Invalid: `00:1F:12:57:9C:C0`

### IPv6 Address Format

Seems to be case-insensitive!

