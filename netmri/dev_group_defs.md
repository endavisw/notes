# NetMRI Device Group definition changes

## IT Services w/o SNMP

The default "IT Services" Device Group membership criteria is
```perl
$Assurance >= 20 and $Community ne "" and $Type in ["Netware","Time Server","Printer","Copier","X-Terminal","NTO","PDU","Server","Power Controller","UPS","Optical Networking Platform"]
```

And the default "IT Services w/o SNMP" criteria is
```perl
$Assurance >= 20
		and $Community eq ""
		and $Type in [
	    	"Netware", "Time Server",
	    	"Printer", "Copier", "X-Terminal"
		]
```

The "App Servers" and "Workstations" w/o SNMP groups do not have such a discrepancy.

For Device Types "NTO", "PDU", "Server", "Power Controller", "UPS", "Optical 
Networking Platform" without SNMP to _correctly_ be members of the "IT Services 
w/o SNMP" group and **not** "UNKNOWN", change "IT Services w/o SNMP" criteria to:

```perl
$Assurance >= 20 and $Community eq "" and $Type in ["Netware","Time Server","Printer","Copier","X-Terminal","NTO","PDU","Server","Power Controller","UPS","Optical Networking Platform","Lights Out Management","Hypervisor"]
```

## Routers (basic group)

A layer3-switch, "Switch-Router" in NetMRI parlance, will be a member of both
"Routing" and "Switching" Device Groups, and while correct in _some_ measure,
this messes up the ability to accurately count the number of devices broken
into groups.

One possible solution is to create a _basic_ Device Group definition, called
something like "Routers", with the definition:

```perl
$Assurance > 75 and $Type in ["Router","SD-WAN"]
```

Since Device Type "SDN Element" is included in Routing, Switching, and SDN
groups by default, a similar treatment for "Switches" might be forseeable.
