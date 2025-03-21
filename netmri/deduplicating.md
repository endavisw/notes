# Deduplication Across Multiple NetMRI Systems

This document shares thoughts on deduplicating data across multiple NetMRI
Operations Centers (OCs) or stand-alone NetMRI instance. This is *not* about
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

To date, two major deduplication approaches have been developed, which this
document calls "Device Level" and "Component Level".


# Device Level
## Data Collection
This approach correlates Device & related info (interfaces, if_addrs, etc) in a
dictionary/hashtable keyed by something identifying the system (hostname, IP,
informal identifier, whatever), then iterates over those trying to find the
"best" version of each Device. The underlying idea is that the "best" Device
must have the best component-level data as well.

Examples for this will be presented in Python, such as a starting per-system
dict:
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

In the per-system dict, these should be keyed by DeviceID. Example:
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
    - Only relates how sure NetMRI is of the DeviceType for the
    Device. Multiple NetMRI systems of the same version, equipped with the same
    DSBs (where applicable), should come up with the same Type & Assurance values
    for non-Unmanaged devices that both successfully query with SNMP.
    - Previous discussion on this topic attributed some importance to
    DeviceAssurance, but its usefulness as a tie-breaker may only show when one
    system is in the middle initially discovering the Device in question.

This should then be augmented with data from Discovery Statuses.

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
* StatusDetail - License status
* LastSeen
* ReachableStatus
* SNMPCollectionEnabled, SNMPCredentialStatus, SNMPCredentialTimestamp
* SDNCollectionEnabled, SDNCollectionStatus, SDNCollectionTimestamp

Use of attributes in tie-breaking for deduplication will be discussed futher
down.

### Components

If gathering other data like Interfaces, Interface Addresses, Routes, VRFs, etc,
this point would be good to collect them from the current NetMRI system and
place that data under the DeviceID in the per-system dict. Most of these can
be dumped via index.csv.

## Deduplication

After collecting from all NetMRI systems, it's now time to iterate over the
Devices from each one, so generating an identifier from available Device
attributes is necessary to determine if two Devices being compared are
(probably) the same real-life device or not, and thus if figuring out a 
"winner" is required.

But shouldn't each system have different Discovery Ranges and thus not have
duplicate Devices? Ideally, yes, but there might be technical reasons why there
could be overlap, or seed routers used by multiple systems, or situations like
lo0 falling into one system's ranges and mgmt0 in another's.

### What's in a Name? Generating Identifiers

First, what's _not_ good?

DeviceID is just an incrementing integer that's only significant to an
individual system, so it's meaingless when comparing Devices from multiple
systems.

DeviceUniqueKey likely isn't reliable for much in the situation that two
systems (or even the same system) have different ideas about the same Device.

Something can be formed from DeviceDNSName, DeviceSysName, and DeviceIPDotted to
uniquely identify a Device across multiple NetMRI systems, but they're all,
well, kinda bad.

* DeviceDNSName
    - Could be out of date, if it even exists
* DeviceSysName
    - Self-reported by the Device via SNMP. Could be vendor-default,
    unconfigurable, or not necessarily desirable for other reasons.
* DeviceIPDotted
    - NetMRI's best guess at what the management IP address might be. Depends
    on Discovery config, the IP that NetMRI determined to exist, interfaces are
    on the Device and how they're configured (if it's a DeviceType for which
    NetMRI will collect Interfaces). Not guaranteed to be unique in an
    environment with VRFs and potentially overlapping IP space. Hopefully it
    won't stumble upon VIPs this way.
* DeviceSysDescr
    - Because the Mavenir mNode180 is... different.

There's likely no universally _right_ answer for if DeviceDNSName or
DeviceSysName is "better" and will depend highly on the environment, quality/
veracity of DDI data, and configuration management.

But to compare these there needs to be some kind of baseline. Upper-casing and
taking the first token split on dot ('.') is a good place to start. "UNKNOWN"
might as well be empty-string since it's just a placeholder (okay, a customer
_could have_ called a Device this... but why?); both should "lose" against
another name for the potential identifier.

The Mavenir mNode180 just outputs "mNode180" for `SNMPv2-MIB::sysName.0`, but
this isn't a normal vendor-default-name situation: the real hostname is the
first space-delimited token in `SNMPv2-MIB::sysDescr.0`, so it can be found in
DeviceSysDescr.

```
sysDescr.0 = DisplayString : [hostname_here] aTCA ISBC 
sysObjectID.0 = ObjectIdentifier : 0.0 () 
sysName.0 = DisplayString : mNode180 
```

Unless it's one that just outputs Linux platform information for sysDescr.

```
sysDescr.0 = DisplayString : Linux release:2.4.20-8 machine:i686 
sysObjectID.0 = ObjectIdentifier : 0.0 () 
sysName.0 = DisplayString : mNode180 
```

That would yield "LINUX" for name, so add that to the list of vendor-default
(known duplicate) names that are unusable as keys:

| Known Duplicate Names               |
| ----------------------------------- |
| CMM                                 |
| DOCKER-TEST                         |
| IBM REMOTE SUPERVISOR ADAPTER II--1 |
| IDRAC                               |
| IDRAC8                              |
| IPAD                                |
| LINUX                               |
| LOCALHOST                           |
| MALBAN7                             |
| MALBAN10                            |
| MNODE180                            |
| NETSCALER                           |
| NOS                                 |
| OA1                                 |
| PIXEL-6                             |
| PIXEL-7-PRO                         |
| SAMSUNG                             |
| SC-1                                |
| SC-2                                |
| SWD77                               |
| SWITCH1                             |
| SWITCH2                             |
| TP5000                              |
| TX-3000                             |
| UNINITIALIZED NAME                  |
| YOUR SYSTEM NAME HERE               |

_("Your System Name Here"? Those are Netscout Infinistreams)_

Many other smartphone models are likely candidates for this list and "IDRAC9"
is probably out in the wild somewhere. The above has been observed in the
aforementioned large customer's environment. If "UNKNOWN" and empty-string
should be in this list or a separate disqualifier remains up to the implementer.
It's likely that any name containing a space or is otherwise non-DNS-compliant
should be disqualified.

What if there's no DNS entry and the SNMP hostname is unusable? Last thing left
that could possibly be used as a unique identifier is the IP address. There's
probably a decent chance of uniqueness on its own but VirtualNetworkName (the
NetMRI View name) could be appended. That would presume all systems have
matching naming for Views, but if there's no usable DNS or SNMP hostname, it's
almost guaranteed that interfaces, VRFs, and physical components aren't
available, either. Whether multiple systems would agree on the "management" IP
for a seemingly otherwise unmanagable Device is up to chance, but this scraping
the bottom of the barrel territory.

Example - prefers DeviceSysName to DeviceDNSName, does not use VirtualNetworkName
```python
from typing import Dict, Union

from infoblox_netmri.api.remote.models.device_remote import DeviceRemote
from infoblox_netmri.api.remote.models.infra_device_remote import InfraDeviceRemote

def get_device_name(
        device: Union[Dict[str, str], DeviceRemote, InfraDeviceRemote]
        ) -> str:
    """ Returns Device mgmt IP if name unknown, else Device Name """
    if isinstance(device, dict):
        d_name = device.get('DeviceDNSName', 'UNKNOWN').upper().split('.')[0]
        s_name = device.get('DeviceSysName', 'UNKNOWN').upper().split('.')[0]
        ip_addr = device.get('DeviceIPDotted', 'UNKNOWN')
        descr = device.get('DeviceSysDescr', '')
    elif isinstance(device, (DeviceRemote, InfraDeviceRemote)):
        d_name = device.DeviceDNSName
        d_name = d_name.upper().split('.')[0] if d_name else 'UNKNOWN'
        s_name = device.DeviceSysName
        s_name = s_name.upper().split('.')[0] if s_name else 'UNKNOWN'
        ip_addr = device.DeviceIPDotted or 'UNKNOWN'
        descr = device.DeviceSysDescr or ''
    else:
        d_name = 'UNKNOWN'
        s_name = 'UNKNOWN'
        ip_addr = 'UNKNOWN'
        descr = ''

    if s_name == 'MNODE180' and descr and not descr.startswith('Linux'):
        s_name = descr.upper().split(' ')[0]
    name = (s_name if s_name not in ['UNKNOWN', '']
            and s_name not in DUPLICATE_NAMES
            and ' ' not in s_name
            else d_name)
    return ip_addr if name == 'UNKNOWN' or name in DUPLICATE_NAMES else name
```


### Tie-Breaking, Stage 1 - Filtering

Potentially every Device from the first NetMRI system in the per-system dict
will make it into the all-devices dict, as there's seemingly nothing else to
conflict yet, but it's possible for conflicts within Devices from the same
system due to things like outdated DNS, incorrectly redeployed hardware,
incomplete discovery, pending internal deduplication, etc.

Every Device iterated over should have an identifier generated and used as its
key in the all-devices dict. Key doesn't exist in the dict? Great! Add it and
its Device to the dict. Key exists? Need to compare both "versions" of the
Device to determine which one stays in the all-devices dict.

Before getting into this situation, it might be worth filtering Devices from
consideration at all, either disqualification in a conflict or from being in
the all-devices dict to begin with.

For example, under normal circumstances, a Device is removed from a system's
database if it's been unreachable for a configurable amount of time. Except if
said Device is a seed router. Being configured as a seed router satisfies
NetMRI's criteria for _existing_ and thus will not be auto-removed in any amount
of time

This is where ReachableTimestamp **is not useful in any way**. ReachableTimestamp
is when NetMRI last determined if the Device was reachable _or not_ and updated
ReachableStatus/ReachableMessage. Just ignore ReachableTimestamp.

DeviceTimestamp and LastSeen appear to be when NetMRI last communicated with the
Device. These have been observed, with rare exception, to be identical. In the
instances where they were different, they could be an hour apart.

ReachableStatus of "Error" and DeviceTimestamp/LastSeen that's past a certain
age could be criteria for disqualifing a Device.

A recent DeviceTimestamp/LastSeen doesn't mean NetMRI has had _useful_
communication with the Device, could be that it's merely responded to a ping.

Example Timestamp Comparisons
| Reachable, SNMP OK      |                           |
| ----------------------- | ------------------------- |
| DeviceTimestamp         | 2024-06-13 16:22:15 +0000 |
| LastSeen                | 2024-06-13 16:22:15 +0000 |
| ReachableTimestamp      | 2024-06-13 16:28:19 +0000 |
| SNMPCredentialTimestamp | 2024-06-13 16:20:26 +0000 |

| Reachable, SNMP Failure |                           |
| ----------------------- | ------------------------- |
| DeviceTimestamp         | 2024-06-13 07:06:10 +0000 |
| LastSeen                | 2024-06-13 07:06:10 +0000 |
| ReachableTimestamp      | 2024-06-13 17:06:45 +0000 |
| SNMPCredentialTimestamp | 2024-06-12 22:54:14 +0000 |

| Reachable, Unlicensed   |                           |
| ----------------------- | ------------------------- |
| DeviceTimestamp         | 2024-06-13 15:40:22 +0000 |
| LastSeen                | 2024-06-13 16:35:03 +0000 |
| ReachableTimestamp      | 2024-06-13 16:35:03 +0000 |
| SNMPCredentialTimestamp | 2024-06-13 16:35:03 +0000 |

| Unreachable             |                           |
| ----------------------- | ------------------------- |
| DeviceTimestamp         | 2024-06-06 18:52:21 +0000 |
| LastSeen                | 2024-06-06 18:52:21 +0000 |
| ReachableTimestamp      | 2024-06-13 16:34:36 +0000 |
| SNMPCredentialTimestamp | 2024-06-06 09:09:51 +0000 |

NetMRI doesn't necessarily use consistent timestamp formats, could be any of
| Example Timestamp         | strptime Format        |
| ------------------------- | ---------------------- |
| 2024-08-02 14:12:15 -0400 | `%Y-%m-%d %H:%M:%S %z` |
| 2024-08-02T14:12:15-0400  | `%Y-%m-%dT%H:%M:%S%z`  |
| 2024-08-02 14:12:15       | `%Y-%m-%d %H:%M:%S`    |
| 2024-08-02T14:12:15       | `%Y-%m-%dT%H:%M:%S`    |

Good luck.

```python
dt_fmt_tz = (r'%Y-%m-%dT%H:%M:%S%z'
             if 'T' in time_str else r'%Y-%m-%d %H:%M:%S %z')
dt_fmt_no_tz = (r'%Y-%m-%dT%H:%M:%S'
                if 'T' in time_str else r'%Y-%m-%d %H:%M:%S')
try:
    time_dt = datetime.strptime(time_str, dt_fmt_tz)
except ValueError:
    time_dt = datetime.strptime(time_str, dt_fmt_no_tz)
```

### Tie-Breaking, Stage 2 - Comparing

There's a Device in the all-devices dict with a key that matches a new Device
that's currently being processed. If there was any staleness filtering, it
didn't apply. Now what?

There are a number of attributes to compare between the two to determine the
"best" version of the Device. This might not mean just "most-recent", however.

* StatusDetail
  * DeviceAssurance
    * SNMPCredential or SDNCollection
      * DeviceTimestamp/LastSeen

**StatusDetail** has 4 (really 3) possible values and is the easiest comparison.

* Licensed
    - Device has been determined to be of a DeviceType that consumes a license.
    It's likely infrastructure and NetMRI will attempt to collect _lots_ of
    information from it. Is being collected by SNMP or SDN, and may have CLI
    collection as well.
* Licensed, Switch Port Manager
    - Same as Licensed, but DeviceType is Switch, Switch-Router, or Wireless
    Controller.
* Unlicensed
    - Could mean many things: Device could be of a DeviceType that does not
    consume a license and only basic information is collected; could potentially
    be infrastructure but NetMRI hasn't finished discovering the Device yet;
    could be any Device that does not respond to SNMP but is known to exist and
    might respond to ping.
* Unmanaged
    - Device IP is in a discovery range configured "Exclude from Management"
    (aka, IGNORE). This is similar to "Exclude from Discovery" (EXCLUDE), in
    that NetMRI doesn't make any attempt to poll the IP, but its existence is
    noted, whereas Discovery-Excluded IPs never make it into the database.

StatusDetail starting with "Licensed" should win against "Unlicensed" or
"Unmanaged".

StatusDetail "Unlicensed" should win against "Unmanaged".

A conflict here could arise, for example, if a Discovery Range has been moved
from one system to another, and the range has been set to Unmanaged on the older
system.

**DeviceAssurance** could be useful as a tie-breaker if StatusDetail is the same.

This is _only_ how confident NetMRI in its determination of DeviceType. Being
found by CDP/LLDP, a hop in a traceroute, hop in a route table, or configured
Device Hint could influence this score, but without SNMP will only go so high.
A sysObjectID or sysDescr match (\<devicetypemodels> and  \<devicemap> DSB tags)
will result in a score 99, the highest possible.

Most fully-discovered Licensed Devices should have DeviceAssurance of 99, but
it's possible for one system to have a Device with 99 Assurance score, but
another system is still discovering the same Device and at time of comparison is
at 60 or 72.

**Collection Status**

SNMPCollectionEnabled and SDNCollectionEnabled should be checked for a non-empty
value to see which mechanism is being used to poll the Device.

Then, SNMPCredentialStatus/SDNCollectionStatus can be checked to see if the
last collection was successful, with a value of "OK". If so, compare
SNMPCredentialTimestamp/SDNCollectionTimestamp; most recent wins.

Wait, why not SNMPCollectionStatus? In a case where, for example, Inventory
collection succeeds but Environmental collection fails, SNMPCollectionStatus
will be Error. SNMPCredentialStatus is a better indicator of any working SNMP
communication.

If two systems are using a _different_ mechanism for seemingly the same Device,
that could be the result of misconfiguration but not necessarily the end of the
world. A hypothetical example could be that one system is correctly configured
to discover a Cisco ACI Fabric as SDN, but another system has found the switches
by a means like CDP and is querying them with SNMP.

If these timestamps are somehow the same, or more likely, the means aren't
compatible and shouldn't be used, the last time NetMRI got any sort of response
from the Device can break the tie.

**DeviceTimestamp / Last Seen** can basically be used interchangably, just chose
one. This is not an indicator that NetMRI has successfully collected from a
Device, just that there's been some proof of life like a ping response.

Most recent wins.

**Tie not broken?** Then these systems have _identical_ ideas about this Device
and have done so at _identical_ times, so it doesn't matter. Keep the one already
in the all-devices dict and move on.


## End State

The all-devices dict can be considered to have _individual_ Devices, keyed by
their generated unique identifiers.

```python
{
    'DEVICE1': {
        'DeviceSysName':   'Device1',
        # etc...
    },
    'DEVICE2': {
        'DeviceDNSName':   'Device2.example.com',
        # etc...
    },
    '10.255.255.19': {
        'DeviceDNSName':   '',
        # etc...
    },
}
```

Example program TDB.


# Component Level

This approach is more record-based and uses multiple fields in a record
assembled from various data in a system to determine if deduplication is
required, and if so, uses a component-specific timestamp for comparison.

While physical component inventory may be present on Unlicensed Devices, other
things such as Interfaces will only be collected from Licensed Devices, making
StatusDetail and DeviceAssurance comparisons largely unnecessary.

It's possible that a Device was formerly Licensed, had Interfaces collected,
and later the license state changed. In such an event, NetMRI would stop
collecting Interfaces and thus have an older Interface Timestamp than a system
that was currently collecting the Device in a Licensed state.

More detail to come.