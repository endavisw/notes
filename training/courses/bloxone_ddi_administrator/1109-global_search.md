# BloxOne Global Search

Global Search to look up specific resources quickly

Global Search icon is in the upper-right corner of the UI

Search by entering full or partial text of the desired resource

Returns most relevant results matching keywords
* Includes search term highlighted in yellow

Can apply filters in results. Can jump to editing resource from results

Displays up to 25 results/page by default
* Can change to 50 or 100

### Global Search Query Syntax

| Syntax                        | Query                                                        |
| ----------------------------- | ------------------------------------------------------------ |
| Wildcard Search (*)           | Wildcard implied                                             |
| IPv4 address                  | Full or partial string                                       |
| Date Format                   | YYYY-MM-DD                                                   |
| Regex and reserved characters | \ + - = && \|\| > < ! ( ) { } [ ] ^ ~ * ? : / and whitespace |
| Double quite characters ("")  | Treated as one single string                                 |
| Space or whitespace character | All entries between spaces are separate strings              |
| Logical operations            | OR implied with spaces, AND and OR can be used explicitly    |

### Supported Search Objects
* On-Prem Hosts
* IPAM/DHCP
    * IP Space
    * Address Block
    * Range
    * Subnet
    * Address
    * IPAM Host
    * Server
    * HA Group
    * Hardware Filter
    * Fixed Address
    * Option Code
    * Option Filter
    * Option Group
    * Option Space
    * Fingerprint
    * TSIG Keys
    * Leases
    * DDI Keys
* DNS Objects
    * DNS View
    * DNS Config Profile
    * Authoritative Zone
    * Forward Zone
    * Resource Records
    * Access Control List
    * Authoritative DNS Server Group
    * Forward DNS Server Group
    * Zone Delegation
* User
    * User Groups
    * User Role
    * Access Policy
* All Tags
