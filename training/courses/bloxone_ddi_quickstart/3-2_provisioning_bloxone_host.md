# Provisioning the BloxOne Host

The on-prem host is a collection of containers that provide service for BloxOne

Can be deployed on a physical machine, VM, or Docker host
* Most deployments will use VM image downloaded from CSP

Download location on CSP
* Administration > Downloads > On-Prem Hosts
* Azure - 60 GB
* Azure - 750 GB
* Dell VEP 1425/2485 ISO
* Docker
* KVM/qcow2 - 60 GB
* KVM/qcow2 - 700 GB
* OVA - 60 GB
* OVA - 750 GB

Image size
* 60 GB is for DNS, DHCP, other services
* 750 GB is pre-allocated disk size for using Cloud Data Connector
  * needs more space for log transfer
* Otherwise, base payload is the same

Firewall Requirements
* Outbound DNS & HTTPS

Manage > On-Prem Hosts > Join Tokens
* Create Token
* Token is displayed ONE TIME, copy & save it
* Multiple deployments can use the same token

Depending on Hypervisor, deploying may ask for
* Join token
* IP address for on-prem host
* Local DNS & NTP servers

4 cores, 16 GB RAM default

Manage > On-Prem Hosts
* Will take a few minutes to show up in CSP
* Name will start with ZTP
* Rename to match production name on the host
* May take up to 20 minutes to finish prep

Hardware options include
* Infoblox B1-105
* Dell VEP (Virtual Edge Platform)

Ready to configure DNS, DHCP, security, and NTP services
