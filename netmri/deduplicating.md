# Deduplication Across Multiple NetMRI Systems

This document shares thoughts on deduplicating data across multiple NetMRI
Operations Centers (OCs) or stand-alone NetMRI instance. This is *not** about
how an OC dedupes between Collectors.

The term NetMRI "system" will be used to refer to an invidiual OC or stand-alone
instance interchangably, as the concepts are the same. A stand-alone NetMRI
instance is basically an OC limited to a built-in collector.

The term "component" will be used to refer to any data indexed to a DeviceID,
not limited to physical component inventory. So Interfaces, Interface Addresses,
Routes, VRFs, etc.

These method were developed during an engagement with a large customer with
8 stand-alone instances for scaling.

Multiple systems may have "blindspots" in the network where a single OC or a
solution like Forward Networks would be able to put the pieces together.

Direct HTTPS calls using something like Requests in Python is assumed; the
`infoblox_netmri` module is incapable of accessing certain URLs or performing
CSV dumps.

## Device Level

This approach correlates Device & related info (interfaces, if_addrs, etc) in a
dictionary/hashtable keyed by something identifying the system (hostname, IP,
informal identifier, whatever), then iterates over those trying to find the
"best" version of each Device. The underlying idea is that the "best" Device
must have the best component-level data as well.

```python
{
    'netmri1': {},
    'netmri2': {},
    # etc
}
```

### Devices

Ideally, this process would start with a full Devices or Infra Devices dump
via `/api/x.y/devices/index.csv` or `/api/x.y/infra_devices/index.csv`. Getting
index.csv will return everything from a particular API broker at once, without
pagination, unlike JSON. This is faster and less error-prone than iterating 
over multiple index.json calls, but does assume client-side filtering.

In the per-system dict, these should be keyed by DeviceID

```python
{
    'netmri1': {
        '5':  {
            'DeviceID':   '5',
            'DeviceName': 'Device1',
            # etc...
        },
        '17': {
            'DeviceID':   '17',
            'DeviceName': 'Device2',
            # etc...
        },
    },
}
```

Useful deduplication attributes:
* DeviceDNSName
* DeviceIPDotted
* DeviceSysName
* DeviceTimestamp

But wait, what about... ?
* DeviceID
    - Only significant to an individual NetMRI system. This should be
    used to correlate related info _on the same system but isn't useful at all
    for deduplication between systems
* DeviceName
    - The "Device Name Priority" setting may be inconsistent between
    NetMRI systems and it may be better to generate an identifier on one's own
    using DeviceDNSName & DeviceSysName
* DeviceAssurance
    - Only relates how sure NetMRI is of the Device Type for the
    Device. Multiple NetMRI systems of the same version, equipped with the same
    DSBs (where applicable), should come up with the same Type & Assurance values
    for non-Unmanaged devices that both successfully query with SNMP.
    - Previous discussion on this topic attributed some importance to
    DeviceAssurance, but its usefulness as a tie-breaker may only show when one
    system is in the middle initially discovering the Device in question.

This should be augmented with data from Discovery Statuses.

### Discovery Status

Important Device attributes like licensing and reachability statuses are not
provided by the Devices API broker. This information's available via Discovery
Statuses at `/api/x.y/discovery_statuses/static/licensed.csv?filename=license_management.csv`

The `filename=` parameter _is required_ however it doesn't need to have a value.
"license_management.csv" is just the default filename from the web UI.

DeviceID in Discovery Statuses can be used to locate the Device's dict in the
per-system dict and update() it.

Useful deduplication attributes:
* VirtualNetworkName (don't need to get this separately)
* StatusDetail - Licensed, Unlicensed, Unmanaged
* LastSeen
* ReachableStatus, ReachableTimestamp
* SNMPCollectionEnabled, SNMPCredentialStatus, SNMPCredentialTimestamp
* SDNCollectionEnabled, SDNCollectionStatus, SDNCollectionTimestamp



