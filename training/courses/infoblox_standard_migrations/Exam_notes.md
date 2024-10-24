# DNS
## Zone Count
```
$ ls -1 dc1*/*db | wc -l
55
$ ls -1 dc2*/*db | wc -l
55
```

There is an unused set of DB files
```
$ grep -c -e '^zone "' dc1*/dc1.blox-1.corp-named.conf
54
$ grep -c -e '^zone "' dc2*/dc2.blox-1.corp-named.conf
55
```

112.171.216.in-addr.arpa zone on both DCs
```
$ for dbfile in `ls -1 *db`; do echo ${dbfile}; fgrep -c ${dbfile} dc1.blox-1.corp-named.conf; done
...
112.171.216.in-addr.arpa.db
0
...

$ for dbfile in `ls -1 *db`; do echo ${dbfile}; fgrep -c ${dbfile} dc2.blox-1.corp-named.conf; done
...
112.171.216.in-addr.arpa.db
0
...

$ fgrep '.171.216.in-addr.arpa' dc*/*corp-named.conf
dc1.blox-1.corp_ib_export/dc1.blox-1.corp-named.conf:zone "111.171.216.in-addr.arpa" in {
dc1.blox-1.corp_ib_export/dc1.blox-1.corp-named.conf:    file "111.171.216.in-addr.arpa.db";
dc2.blox-1.corp_ib_export/dc2.blox-1.corp-named.conf:zone "111.171.216.in-addr.arpa" in {
dc2.blox-1.corp_ib_export/dc2.blox-1.corp-named.conf:    file "111.171.216.in-addr.arpa.db";
```

No indication why this zone is not in config
```
$ diff dc1*/112.171.216.in-addr.arpa-info.txt dc1*/111.171.216.in-addr.arpa-info.txt 
6c6
< 	zone name             = 112.171.216.in-addr.arpa
---
> 	zone name             = 111.171.216.in-addr.arpa
26c26
< 	zone DN               = DC=112.171.216.in-addr.arpa,cn=MicrosoftDNS,DC=DomainDnsZones,DC=blox-1,DC=corp
---
> 	zone DN               = DC=111.171.216.in-addr.arpa,cn=MicrosoftDNS,DC=DomainDnsZones,DC=blox-1,DC=corp
```

PTRs in 112.171.216.in-addr.arpa seem stale
```
$ fgrep 216.171.112. dc1*/*db | wc -l
0
$ fgrep 216.171.112. dc2*/*db | wc -l
0
```

Names in 112.171.216.in-addr.arpa have 216.171.111.x addressesin blox-1.corp.db

DC2 has a non-authoritative zone configured
```
$ fgrep -e type dc1*/dc1.blox-1.corp-named.conf | fgrep -c -v master
0

$ fgrep -e type dc2*/dc2.blox-1.corp-named.conf | fgrep -c -v master
1
$ fgrep -e type dc2*/dc2.blox-1.corp-named.conf | fgrep -v master
    type forward;
$ fgrep -B1 -A3 'type forward' dc2*/dc2.blox-1.corp-named.conf
zone "dev.blox-1.net" in {
    type forward;
    forwarders { 10.148.10.53; };
    
};
$ diff dc1*/dc1.blox-1.corp-named.conf dc2*/dc2.blox-1.corp-named.conf 
213a214,219
> };
> 
> zone "dev.blox-1.net" in {
>     type forward;
>     forwarders { 10.148.10.53; };
>     

```
_Customer requires this forward zone_

Two reverse zones allow unauthenticated updates. Same on DC2
```
$ fgrep -B4 -A1 'allow-update { any; };' dc1.blox-1.corp_ib_export/dc1.blox-1.corp-named.conf 
zone "122.10.in-addr.arpa" in {
    type master;
    file "122.10.in-addr.arpa.db";
    allow-transfer { NStab; };
    allow-update { any; };
};
--
zone "111.171.216.in-addr.arpa" in {
    type master;
    file "111.171.216.in-addr.arpa.db";
    allow-transfer { NStab; };
    allow-update { any; };
};
```


## Check for mutually-exclusive zone DB files
```
$ ls -1 dc1*/*db | cut -d/ -f2  > /home/training/Download/dc1_zones
$ ls -1 dc2*/*db | cut -d/ -f2  > /home/training/Download/dc2_zones
$ grep -c -v -f dc1_zones dc2_zones 
0
$ grep -c -v -f dc2_zones dc1_zones 
0
```

## Check for mutually-exclusive records
```
for dbfile in `ls -1 dc1*/*db | cut -d/ -f2`;
do
  echo ${dbfile};
  grep -c -v -f dc1*/${dbfile} dc2*/${dbfile};
  grep -c -v -f dc2*/${dbfile} dc1*/${dbfile};
done
```

Nothing found, but received

```
/usr/bin/grep: dc1.blox-1.corp_ib_export/wl.blox-1.corp.db:29: Invalid back reference
/usr/bin/grep: dc2.blox-1.corp_ib_export/wl.blox-1.corp.db:29: Invalid back reference
```

## Name Servers
```
$ fgrep NS */*db | fgrep -v ';' | cut -d\: -f2- | sort -u
1                       3600	NS	dc1.blox-1.corp.
1                       3600	NS	dc2.blox-1.corp.
ha                      NS	gtm1.ha.blox-1.corp.
ha                      NS	gtm2.ha.blox-1.corp.
@                       NS	dc1.blox-1.corp.
@                       NS	dc2.blox-1.corp.
voip                    3600	NS	dc1.blox-1.corp.
voip                    3600	NS	dc2.blox-1.corp.
```

_srs002/srs008 can be removed_
```
$ fgrep -i -e srs002.blox-1.corp -e srs008.blox-1.corp */*db
dc1.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox-1.corp.
dc1.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc1.blox-1.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc2.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox-1.corp.
dc2.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc2.blox-1.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
```

1.201.10.in-addr.arpa delegation... to itself
- This is an authoritative zone in dc1*named.conf
```
$ grep -e '^1' dc*/*db | fgrep NS
dc1.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc1.blox-1.corp.
dc1.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc2.blox-1.corp.
dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc1.blox-1.corp.
dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc2.blox-1.corp.
```

voip.blox-1.corp delegation... to itself
- This is an authoritative zone in dc1*named.conf
```
$ grep -e '^voip' dc*/*db | fgrep NS
dc1.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc1.blox-1.corp.
dc1.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc2.blox-1.corp.
dc2.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc1.blox-1.corp.
dc2.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc2.blox-1.corp.
```


## WINS
```
$ fgrep -A2 WINS */*db
dc1.blox-1.corp_ib_export/blox-1.corp.db:;  WINS lookup record
dc1.blox-1.corp_ib_export/blox-1.corp.db-;
dc1.blox-1.corp_ib_export/blox-1.corp.db-
dc1.blox-1.corp_ib_export/blox-1.corp.db:@                       WINS	L2 C3600 (
dc1.blox-1.corp_ib_export/blox-1.corp.db-                        		10.61.200.203
dc1.blox-1.corp_ib_export/blox-1.corp.db-                        		10.61.200.202 )
--
dc2.blox-1.corp_ib_export/blox-1.corp.db:;  WINS lookup record
dc2.blox-1.corp_ib_export/blox-1.corp.db-;
dc2.blox-1.corp_ib_export/blox-1.corp.db-
dc2.blox-1.corp_ib_export/blox-1.corp.db:@                       WINS	L2 C3600 (
dc2.blox-1.corp_ib_export/blox-1.corp.db-                        		10.61.200.203
dc2.blox-1.corp_ib_export/blox-1.corp.db-                        		10.61.200.202 )
```

## Invalid Characters
### Underscore
```
$ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u
250.10.in-addr.arpa.db:1.251                   PTR	halab_vip.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:193.1                   PTR	fws_cluster.blox-1.corp.
250.10.in-addr.arpa.db:2.251                   PTR	halab_edge1.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:3.251                   PTR	halab_edge2.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:5.11                    PTR	lbs003_i1.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:6.11                    PTR	lbs003_i2.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:8.11                    PTR	lbs004_i1.mgt.stj.blox-1.corp.
250.10.in-addr.arpa.db:9.11                    PTR	lbs004_i2.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:10.25                   PTR	lbs001_i1.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:12.25                   PTR	lbs002_i1.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:16.25                   PTR	lbs005_i1.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:17.25                   PTR	lbs006_i1.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:248.25                  PTR	lbs005_vl252.mgt.stj.blox-1.corp.
252.10.in-addr.arpa.db:249.25                  PTR	lbs006_vl252.mgt.stj.blox-1.corp.
blox-1.corp.db:cmdbd_dfm               CNAME	srs267.blox-1.corp.
blox-1.corp.db:cmdb_dfm                CNAME	srs345.blox-1.corp.
blox-1.corp.db:condev1_primary         CNAME	srs289.blox-1.corp.
blox-1.corp.db:condev_ws               CNAME	srs1012.blox-1.corp.
blox-1.corp.db:Conprd1_primary         CNAME	srs622.blox-1.corp.
blox-1.corp.db:Conprd1_standby         CNAME	srs623.blox-1.corp.
blox-1.corp.db:Conprd2_primary         CNAME	srs682.blox-1.corp.
blox-1.corp.db:Conprd2_standby         CNAME	srs686.blox-1.corp.
blox-1.corp.db:conprod1_wc             CNAME	srs1037.blox-1.corp.
blox-1.corp.db:conprod1_ws             CNAME	srs1036.blox-1.corp.
blox-1.corp.db:conprod2_wc             CNAME	srs1071.blox-1.corp.
blox-1.corp.db:conprod2_ws             CNAME	srs1070.blox-1.corp.
blox-1.corp.db:consumer_hf_prod1_sql   CNAME	srs1010.blox-1.corp.
blox-1.corp.db:consumer_hf_prod1_ws    CNAME	srs1018.blox-1.corp.
blox-1.corp.db:consumer_hf_stag_ws     CNAME	srs1016.blox-1.corp.
blox-1.corp.db:consumer_hh_prod1_search CNAME	srs1009.blox-1.corp.
blox-1.corp.db:consumer_hh_prod1_sql   CNAME	srs1010.blox-1.corp.
blox-1.corp.db:consumer_hh_prod1_ws    CNAME	srs1007.blox-1.corp.
blox-1.corp.db:consumer_hh_stag_search CNAME	srs1008.blox-1.corp.
blox-1.corp.db:consumer_hh_stag_sql    CNAME	srs1010.blox-1.corp.
blox-1.corp.db:consumer_hh_stag_ws     CNAME	srs1004.blox-1.corp.
blox-1.corp.db:conuat1_primary         CNAME	srs429.blox-1.corp.
blox-1.corp.db:conuat1_standby         CNAME	srs428.blox-1.corp.
blox-1.corp.db:conuat_wc               CNAME	srs1018.blox-1.corp.
blox-1.corp.db:conuat_ws               CNAME	srs1019.blox-1.corp.
blox-1.corp.db:couat_ws                CNAME	srs1019.blox-1.corp.
blox-1.corp.db:dev60_primary           CNAME	srs787.blox-1.corp.
blox-1.corp.db:dev60_standby           CNAME	srs788.blox-1.corp.
blox-1.corp.db:dpdbuat1_nodea          CNAME	srs429.blox-1.corp.
blox-1.corp.db:hh1_dg_node             CNAME	srs638.blox-1.corp.
blox-1.corp.db:lmsprd_primary          CNAME	srs601.blox-1.corp.
blox-1.corp.db:lmsprd_standby          CNAME	srs653.blox-1.corp.
blox-1.corp.db:madbprd                 CNAME	lmsprd_primary.blox-1.corp.
blox-1.corp.db:mavcdbprd               CNAME	vcsprd_primary.blox-1.corp.
blox-1.corp.db:oradb_primary           CNAME	srs610.blox-1.corp.
blox-1.corp.db:oradb_standby           CNAME	srs792.blox-1.corp.
blox-1.corp.db:rmscnv_primary          CNAME	srs788.blox-1.corp.
blox-1.corp.db:rmscnv_standby          CNAME	srs787.blox-1.corp.
blox-1.corp.db:rmsdev_primary          CNAME	srs787.blox-1.corp.
blox-1.corp.db:rmsdev_standby          CNAME	srs788.blox-1.corp.
blox-1.corp.db:rmsenv1_primary         CNAME	srs787.blox-1.corp.
blox-1.corp.db:rmsenv1_standby         CNAME	srs788.blox-1.corp.
blox-1.corp.db:rmsprd_primary          CNAME	srs789.blox-1.corp.
blox-1.corp.db:rmsprd_standby          CNAME	srs790.blox-1.corp.
blox-1.corp.db:vcsprd_primary          CNAME	srs653.blox-1.corp.
blox-1.corp.db:vcsprd_standby          CNAME	srs601.blox-1.corp.

$ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u | wc -l
60
```


### Slashes, etc
```
$ fgrep '\' dc*/*db
dc1.blox-1.corp_ib_export/wl.blox-1.corp.db:dans\302\240bb          900	A	10.150.240.30
dc2.blox-1.corp_ib_export/wl.blox-1.corp.db:dans\302\240bb          900	A	10.150.240.30

$ fgrep '/' dc*/*db
dc1.blox-1.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox-1.corp.
dc1.blox-1.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox-1.corp.
dc1.blox-1.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox-1.corp.
dc1.blox-1.corp_ib_export/blox-1.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc1.blox-1.corp_ib_export/mgt.dallas.blox-1.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc1.blox-1.corp_ib_export/mgt.miami.blox-1.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1
dc2.blox-1.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox-1.corp.
dc2.blox-1.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox-1.corp.
dc2.blox-1.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox-1.corp.
dc2.blox-1.corp_ib_export/blox-1.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc2.blox-1.corp_ib_export/mgt.dallas.blox-1.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc2.blox-1.corp_ib_export/mgt.miami.blox-1.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1

$ fgrep '%' dc*/*db
$ fgrep '$' dc*/*db
$ fgrep '^' dc*/*db
$ fgrep '&' dc*/*db
```

# DHCP

```
$ cd dhcp_export
```

## Convert netsh from UTF16 to UTF8
Now greppable
```
$ iconv -f utf16 -t utf8 netsh-10.61.200.202-dc1.blox.corp.txt > netsh-utf8-10.61.200.202-dc1.blox.corp.txt
$ iconv -f utf16 -t utf8 netsh-10.61.200.203-dc2.blox.corp.txt > netsh-utf8-10.61.200.203-dc2.blox.corp.txt
```

Diffable configs
```
$ fgrep Dhcp netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep -v '#' | cut -d\  -f4- > /home/training/Downloads/dc1_dhcp_config
$ fgrep Dhcp netsh-utf8-10.61.200.203-dc2.blox.corp.txt | fgrep -v '#' | cut -d\  -f4- > /home/training/Downloads/dc2_dhcp_config
```

## Scopes
Unique Scope Count
```
$ fgrep 'add scope' netsh-utf8-* | awk '{print $6" "$7;}' | sort -u | wc -l
53

$ fgrep -A1 '<Scope>' dhcpconfig* | fgrep ScopeId | cut -d\< -f2 | cut -d\> -f2 | sort -u | wc -l
53
```

## Check for mutually-exclusive scopes
```
$ fgrep 'add scope' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | awk '{print $6" "$7;}' | sort > /home/training/Downloads/dc1_scopes
$ fgrep 'add scope' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | awk '{print $6" "$7;}' | sort > /home/training/Downloads/dc2_scopes
```

Missing from DC1
```
$ grep -c -v -f dc1_scopes dc2_scopes 
0
```

Missing from DC2
```
$ grep -c -v -f dc2_scopes dc1_scopes
3
$ grep -v -f dc2_scopes dc1_scopes
10.195.55.0 255.255.255.0
10.201.193.0 255.255.255.0
10.201.194.0 255.255.255.0
```

## Check for mutually-exclusive ranges
```
$ fgrep -i 'Add iprange' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep Dhcp | awk '{print $8" "$9;}' | sort > /home/training/dc1_ranges
$ fgrep -i 'Add iprange' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | fgrep Dhcp | awk '{print $8" "$9;}' | sort > /home/training/dc2_ranges
```

Missing from DC1
```
$ grep -v -f dc1_ranges dc2_ranges
10.201.180.0 range 10.201.180.100 10.201.180.200
```

Missing from DC2
```
$ grep -v -f dc2_ranges dc1_ranges
10.195.55.0 range 10.195.55.1 10.195.55.254
10.201.180.0 range 10.201.180.100 10.201.180.254
10.201.193.0 range 10.201.193.1 10.201.193.254
10.201.194.0 range 10.201.194.1 10.201.194.254
```

Nothing surprising

## Other DNS Servers
Find all domain-name-servers
```
$ fgrep 'optionvalue 6 ' netsh-utf8* | cut -d\: -f2 | fgrep Scope | cut -d\  -f6- | sort -u
set optionvalue 6 IPADDRESS "10.61.200.202" "10.61.200.203" 
set optionvalue 6 IPADDRESS "10.61.200.203" "10.61.200.203" 
set optionvalue 6 IPADDRESS "8.8.8.8" "8.8.4.4"
```

Guest wireless uses Google DNS servers
```
$ for network in `fgrep 8.8. netsh-utf8-10.61.200.20* | cut -d\: -f2- | awk '{print $5;}' | sort -u`; do fgrep ${network} netsh-utf8* | fgrep 'add scope'; done
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.150.208.0 255.255.252.0 "Chicago Guest" "vl108"
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.150.208.0 255.255.252.0 "Chicago Guest" "vl108"
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.150.224.0 255.255.240.0 "guestwifi" ""
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.150.224.0 255.255.240.0 "guestwifi" ""
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.192.51.0 255.255.255.0 "Denver Guest" "vl135"
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.192.51.0 255.255.255.0 "Denver Guest" "vl135"
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.194.51.0 255.255.255.0 "Los Angeles guestwifi" "vl143"
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.194.51.0 255.255.255.0 "Los Angeles Guest" "vl143"
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.195.51.0 255.255.255.0 "Miami Guest" "vl148"
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.195.51.0 255.255.255.0 "Miami Guest" "vl148"
netsh-utf8-10.61.200.202-dc1.blox.corp.txt:Dhcp Server \\10.61.200.202 add scope 10.254.0.0 255.255.240.0 "BYOD" 
netsh-utf8-10.61.200.203-dc2.blox.corp.txt:Dhcp Server \\10.61.200.203 add scope 10.254.0.0 255.255.240.0 "BYOD" 
```

## VoIP TFTP Servers
* DC1 - 10.204.20.34, 10.204.20.35, 10.204.20.41
* DC2 - 10.102.1.34, 10.102.1.35, 10.102.1.41

_204 is new network, 102 is decomissioned - use 204 (DC1)!_
```
$ fgrep 'optionvalue 150' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | cut -d\  -f4-
Scope 10.50.0.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.51.0.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.52.0.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.53.0.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.64.0.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.150.202.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.34" 
Scope 10.192.10.0 set optionvalue 150 IPADDRESS "10.204.20.41" "10.204.20.35" "10.204.20.34" 
Scope 10.192.53.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.41" "10.204.20.34" 
Scope 10.192.60.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.34" "10.204.20.41" 
Scope 10.194.53.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.41" "10.204.20.34" 
Scope 10.194.60.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.41" "10.204.20.34" 
Scope 10.195.53.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.41" "10.204.20.34" 
Scope 10.195.60.0 set optionvalue 150 IPADDRESS "10.204.20.35" "10.204.20.41" "10.204.20.34" 

$ fgrep 'optionvalue 150' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | cut -d\  -f4-
Scope 10.50.0.0 set optionvalue 150 IPADDRESS "10.102.1.41" "10.102.1.35" "10.102.1.34" 
Scope 10.51.0.0 set optionvalue 150 IPADDRESS "10.102.1.41" "10.102.1.35" "10.102.1.34" 
Scope 10.52.0.0 set optionvalue 150 IPADDRESS "10.102.1.41" "10.102.1.35" "10.102.1.34" 
Scope 10.53.0.0 set optionvalue 150 IPADDRESS "10.102.1.41" "10.102.1.35" "10.102.1.34" 
Scope 10.64.0.0 set optionvalue 150 IPADDRESS "10.102.1.41" "10.102.1.35" "10.102.1.34" 
Scope 10.150.202.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.34" 
Scope 10.192.53.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34" 
Scope 10.192.60.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34" 
Scope 10.194.53.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34" 
Scope 10.194.60.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34" 
Scope 10.195.53.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34" 
Scope 10.195.60.0 set optionvalue 150 IPADDRESS "10.102.1.35" "10.102.1.41" "10.102.1.34"
```

## IP Reservations
* As expected, DC1 has reservations for 10.201.193.0
* They do not agree for 10.195.52.248's or 10.195.54.234's MACs
  - _Leave either MAC, provide list to customer to track down_
```
$ fgrep -i 'Add reservedip' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep Dhcp | awk '{print $8" "$9;}' | sort > /home/training/Downloads/dc1_fixed_ips
$ fgrep -i 'Add reservedip' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | fgrep Dhcp | awk '{print $8" "$9;}' | sort > /home/training/Downloads/dc2_fixed_ips
$ diff dc*fixed_ips
303c303
< 10.195.52.248 00d0c9f3cbe2
---
> 10.195.52.248 00d0c9f3c3a7
307c307
< 10.195.54.234 d49a20d0614f
---
> 10.195.54.234 d49a20b0614f
343,353d342
< 10.201.193.32 c48508421b02
< 10.201.193.33 08a3867336ff
< 10.201.193.34 3c970e62c897
< 10.201.193.35 001c23565950
< 10.201.193.36 0800271d5d9f
< 10.201.193.39 90b11c5cf2f6
< 10.201.193.40 0004a31cb195
< 10.201.193.41 001040386b22
< 10.201.193.42 00104024cbd4
< 10.201.193.43 000b6bab80e4
< 10.201.193.44 00726b313936
```

## Global Options
```
$ fgrep Dhcp netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep '\\10.61.200.202 set '
Dhcp Server \\10.61.200.202 set optionvalue 6 IPADDRESS "10.61.200.202" "10.61.200.203" 
Dhcp Server \\10.61.200.202 set optionvalue 15 STRING "blox.corp" 
Dhcp Server \\10.61.200.202 set optionvalue 44 IPADDRESS "10.61.200.202" "10.61.200.203" 
Dhcp Server \\10.61.200.202 set optionvalue 46 BYTE "8" 
```

## SLP-Directory-Agent Option
```
$ for network in `fgrep 'optionvalue 78' netsh-utf8* | cut -d: -f2- | cut -d\  -f5 | sort -u`; do fgrep ${network} netsh-utf8* | fgrep 'add scope' | cut -d: -f2- | cut -d\  -f4- | sort -u; done
add scope 10.150.204.0 255.255.252.0 "Chicago Corp" "vl104"
add scope 10.192.50.0 255.255.255.0 "Denver Corp" "vl134"
add scope 10.192.54.0 255.255.255.0 "Denver Workstations" "vl138"
add scope 10.194.50.0 255.255.255.0 "Los Angeles Corp" "vl142"
add scope 10.194.54.0 255.255.255.0 "Los Angeles Workstations" "vl157"
add scope 10.195.50.0 255.255.255.0 "Miami Corp" "vl147"
add scope 10.195.54.0 255.255.255.0 "Miami Workstations" "vl151"
add scope 10.201.175.0 255.255.255.0 "Desktop Support Vlan" "vl175"
add scope 10.201.200.0 255.255.255.0 "Training Room Vlan" "vl100"
add scope 10.202.224.0 255.255.255.0 "Cafeteria" "vl224"
add scope 10.250.250.0 255.255.255.0 "Retail Store" "vl250"
add scope 10.40.0.0 255.255.0.0 "Chicago Warehouse Subnet" "vl040"
add scope 10.41.0.0 255.255.0.0 "Chicago 1st Floor Subnet" "vl041"
add scope 10.42.0.0 255.255.0.0 "Chicago 2nd Floor Subnet" "vl042"
add scope 10.43.0.0 255.255.0.0 "Chicago 3rd Floor Subnet" "vl043"
```

## Find all exclusion ranges
```
$ fgrep excluderange netsh-utf* | cut -d\: -f2 | cut -d\  -f4- | sort -u
Scope 10.150.202.0 add excluderange 10.150.202.150 10.150.202.250
Scope 10.150.202.0 add excluderange 10.150.202.50 10.150.202.149
Scope 10.150.204.0 add excluderange 10.150.204.159 10.150.204.159
Scope 10.150.204.0 add excluderange 10.150.204.170 10.150.204.170
Scope 10.150.204.0 add excluderange 10.150.204.173 10.150.207.250
Scope 10.150.204.0 add excluderange 10.150.204.96 10.150.207.172
Scope 10.150.204.0 add excluderange 10.150.207.240 10.150.207.249
Scope 10.150.208.0 add excluderange 10.150.208.16 10.150.209.255
Scope 10.150.208.0 add excluderange 10.150.210.0 10.150.211.254
Scope 10.150.217.0 add excluderange 10.150.217.135 10.150.217.254
Scope 10.150.217.0 add excluderange 10.150.217.16 10.150.217.134
Scope 10.150.224.0 add excluderange 10.150.224.16 10.150.231.254
Scope 10.150.224.0 add excluderange 10.150.232.0 10.150.239.254
Scope 10.150.240.0 add excluderange 10.150.240.1 10.150.240.132
Scope 10.150.240.0 add excluderange 10.150.240.1 10.150.240.15
Scope 10.192.10.0 add excluderange 10.192.10.162 10.192.10.254
Scope 10.192.10.0 add excluderange 10.192.10.219 10.192.10.219
Scope 10.192.10.0 add excluderange 10.192.10.71 10.192.10.161
Scope 10.192.12.0 add excluderange 10.192.12.100 10.192.12.107
Scope 10.192.12.0 add excluderange 10.192.12.1 10.192.13.255
Scope 10.192.12.0 add excluderange 10.192.14.0 10.192.15.202
Scope 10.192.50.0 add excluderange 10.192.50.150 10.192.50.250
Scope 10.192.50.0 add excluderange 10.192.50.50 10.192.50.149
Scope 10.192.51.0 add excluderange 10.192.51.150 10.192.51.250
Scope 10.192.51.0 add excluderange 10.192.51.50 10.192.51.149
Scope 10.192.52.0 add excluderange 10.192.52.150 10.192.52.250
Scope 10.192.52.0 add excluderange 10.192.52.240 10.192.52.250
Scope 10.192.52.0 add excluderange 10.192.52.50 10.192.52.149
Scope 10.192.53.0 add excluderange 10.192.53.150 10.192.53.250
Scope 10.192.53.0 add excluderange 10.192.53.50 10.192.53.149
Scope 10.192.54.0 add excluderange 10.192.54.150 10.192.54.250
Scope 10.192.54.0 add excluderange 10.192.54.50 10.192.54.149
Scope 10.192.60.0 add excluderange 10.192.60.150 10.192.60.250
Scope 10.192.60.0 add excluderange 10.192.60.50 10.192.60.149
Scope 10.194.10.0 add excluderange 10.194.10.100 10.194.10.160
Scope 10.194.10.0 add excluderange 10.194.10.100 10.194.10.174
Scope 10.194.10.0 add excluderange 10.194.10.161 10.194.10.161
Scope 10.194.10.0 add excluderange 10.194.10.175 10.194.10.254
Scope 10.194.50.0 add excluderange 10.194.50.150 10.194.50.250
Scope 10.194.50.0 add excluderange 10.194.50.50 10.194.50.149
Scope 10.194.51.0 add excluderange 10.194.51.150 10.194.51.250
Scope 10.194.51.0 add excluderange 10.194.51.50 10.194.51.149
Scope 10.194.52.0 add excluderange 10.194.52.150 10.194.52.250
Scope 10.194.52.0 add excluderange 10.194.52.50 10.194.52.149
Scope 10.194.53.0 add excluderange 10.194.53.100 10.194.53.174
Scope 10.194.53.0 add excluderange 10.194.53.175 10.194.53.250
Scope 10.194.54.0 add excluderange 10.194.54.150 10.194.54.250
Scope 10.194.54.0 add excluderange 10.194.54.50 10.194.54.149
Scope 10.194.60.0 add excluderange 10.194.60.150 10.194.60.250
Scope 10.194.60.0 add excluderange 10.194.60.50 10.194.60.149
Scope 10.195.10.0 add excluderange 10.195.10.100 10.195.10.119
Scope 10.195.10.0 add excluderange 10.195.10.120 10.195.10.149
Scope 10.195.10.0 add excluderange 10.195.10.50 10.195.10.50
Scope 10.195.10.0 add excluderange 10.195.10.77 10.195.10.99
Scope 10.195.50.0 add excluderange 10.195.50.150 10.195.50.250
Scope 10.195.50.0 add excluderange 10.195.50.50 10.195.50.149
Scope 10.195.51.0 add excluderange 10.195.51.150 10.195.51.250
Scope 10.195.51.0 add excluderange 10.195.51.50 10.195.51.149
Scope 10.195.52.0 add excluderange 10.195.52.150 10.195.52.250
Scope 10.195.52.0 add excluderange 10.195.52.50 10.195.52.149
Scope 10.195.53.0 add excluderange 10.195.53.100 10.195.53.174
Scope 10.195.53.0 add excluderange 10.195.53.175 10.195.53.250
Scope 10.195.54.0 add excluderange 10.195.54.150 10.195.54.250
Scope 10.195.54.0 add excluderange 10.195.54.50 10.195.54.149
Scope 10.195.55.0 add excluderange 10.195.55.200 10.195.55.254
Scope 10.195.60.0 add excluderange 10.195.60.150 10.195.60.250
Scope 10.195.60.0 add excluderange 10.195.60.50 10.195.60.149
Scope 10.201.175.0 add excluderange 10.201.175.150 10.201.175.250
Scope 10.201.175.0 add excluderange 10.201.175.50 10.201.175.149
Scope 10.201.200.0 add excluderange 10.201.200.100 10.201.200.150
Scope 10.201.200.0 add excluderange 10.201.200.151 10.201.200.200
Scope 10.202.224.0 add excluderange 10.202.224.100 10.202.224.150
Scope 10.202.224.0 add excluderange 10.202.224.151 10.202.224.200
Scope 10.250.250.0 add excluderange 10.250.250.100 10.250.250.150
Scope 10.250.250.0 add excluderange 10.250.250.151 10.250.250.200
Scope 10.252.0.0 add excluderange 10.252.18.1 10.252.18.250
Scope 10.254.0.0 add excluderange 10.254.0.16 10.254.6.255
Scope 10.254.0.0 add excluderange 10.254.7.0 10.254.15.254
Scope 10.40.0.0 add excluderange 10.40.101.1 10.40.102.255
Scope 10.40.0.0 add excluderange 10.40.103.0 10.40.104.254
Scope 10.41.0.0 add excluderange 10.41.101.1 10.41.102.255
Scope 10.41.0.0 add excluderange 10.41.103.0 10.41.104.254
Scope 10.42.0.0 add excluderange 10.42.101.1 10.42.102.255
Scope 10.42.0.0 add excluderange 10.42.103.0 10.42.104.254
Scope 10.43.0.0 add excluderange 10.43.101.1 10.43.102.255
Scope 10.43.0.0 add excluderange 10.43.103.1 10.43.104.254
Scope 10.50.0.0 add excluderange 10.50.50.1 10.50.51.255
Scope 10.50.0.0 add excluderange 10.50.52.0 10.50.53.254
Scope 10.51.0.0 add excluderange 10.51.50.1 10.51.51.255
Scope 10.51.0.0 add excluderange 10.51.52.0 10.51.53.254
Scope 10.52.0.0 add excluderange 10.52.50.1 10.52.51.255
Scope 10.53.0.0 add excluderange 10.53.50.1 10.53.51.255
Scope 10.53.0.0 add excluderange 10.53.52.0 10.53.53.254
Scope 10.61.202.0 add excluderange 10.61.202.1 10.61.202.1
Scope 10.61.202.0 add excluderange 10.61.202.1 10.61.202.127
Scope 10.61.202.0 add excluderange 10.61.202.128 10.61.202.254
Scope 10.61.203.0 add excluderange 10.61.203.1 10.61.203.1
Scope 10.61.203.0 add excluderange 10.61.203.128 10.61.203.254
Scope 10.61.204.0 add excluderange 10.61.204.1 10.61.204.1
Scope 10.61.204.0 add excluderange 10.61.204.1 10.61.204.127
Scope 10.61.204.0 add excluderange 10.61.204.128 10.61.204.254
Scope 10.61.205.0 add excluderange 10.61.205.1 10.61.205.1
Scope 10.61.205.0 add excluderange 10.61.205.1 10.61.205.127
Scope 10.61.205.0 add excluderange 10.61.205.128 10.61.205.254
Scope 10.64.0.0 add excluderange 10.64.50.1 10.64.51.255
Scope 10.64.0.0 add excluderange 10.64.52.0 10.64.53.254
```

Number of Shared Excluded IPs
```
10.150.202.0    0
10.150.204.0    780
10.150.208.0    0
10.150.217.0    0
10.150.224.0    0
10.150.240.0    15
10.192.10.0     1
10.192.12.0     8
10.192.50.0     0
10.192.51.0     0
10.192.52.0     11
10.192.53.0     0
10.192.54.0     0
10.192.60.0     0
10.194.10.0     62
10.194.50.0     0
10.194.51.0     0
10.194.52.0     0
10.194.53.0     0
10.194.54.0     0
10.194.60.0     0
10.195.10.0     0
10.195.50.0     0
10.195.51.0     0
10.195.52.0     0
10.195.53.0     0
10.195.54.0     0
10.195.55.0     0
10.195.60.0     0
10.201.175.0    0
10.201.200.0    0
10.202.224.0    0
10.250.250.0    0
10.252.0.0      0
10.254.0.0      0
10.40.0.0       0
10.41.0.0       0
10.42.0.0       0
10.43.0.0       0
10.50.0.0       0
10.51.0.0       0
10.52.0.0       0
10.53.0.0       0
10.61.202.0     1
10.61.203.0     0
10.61.204.0     1
10.61.205.0     1
10.64.0.0       0
```

Config diffs
```
$ diff dc*dhcp_config | fgrep -i -v -e excluderange -e 'optionvalue 150' -e 'optionvalue 6' -e dnsconfig
86c86
< add scope 10.150.217.0 255.255.255.0 "Chicago HandHeld" ""
---
> add scope 10.150.217.0 255.255.255.0 "Chicago HandHeld" "vl117"
100c100
< add scope 10.194.51.0 255.255.255.0 "Los Angeles guestwifi" "vl143"
---
> add scope 10.194.51.0 255.255.255.0 "Los Angeles Guest" "vl143"
110c110
< add scope 10.195.53.0 255.255.255.0 "Miami VoIP" ""
---
> add scope 10.195.53.0 255.255.255.0 "Miami VoIP" "vl150"
112d111
< add scope 10.195.55.0 255.255.255.0 "Miami Test" ""
116,117d114
< add scope 10.201.193.0 255.255.255.0 "Test Vlan" "vlan 193"
< add scope 10.201.194.0 255.255.255.0 "vl 194" "vl 194"
136c133
---
141c138
---
146,148c143,144
---
173c169
---
183c179
---
193c189
---
197a194
204c201
---
216c213
---
218c215,216
---
297,298c295
< Scope 10.192.10.0 set optionvalue 15 STRING "mgt.Denver.blox.corp" 
---
> Scope 10.192.10.0 set optionvalue 15 STRING "mgt.denver.blox.corp" 
302,303c299
---
314a311,313
316a316,317
322c323
---
323a325,326
331d333
334c336
---
344c346,347
---
353c356
---
358c361
---
363c366
---
371d373
474c476
---
476c478
---
481c483
---
486,488c488
---
576c576
---
584d583
587c586
---
597c596
---
606c605
---
611c610
---
616c615
---
624d622
638c636
---
643c641
---
648,651d645
686c680
---
694d687
697c690
---
707c700
---
709c702
< Scope 10.195.52.0 Add reservedip 10.195.52.248 00d0c9f3cbe2 "barcode3" "Miami bar code scanner" "DHCP"
---
> Scope 10.195.52.0 Add reservedip 10.195.52.248 00d0c9f3c3a7 "barcode3" "Miami bar code scanner" "DHCP"
713a707
719c713
---
724c718
---
729c723
---
732c726
< Scope 10.195.54.0 Add reservedip 10.195.54.234 d49a20d0614f "linda-bourgeois.blox.corp" "" "DHCP"
---
> Scope 10.195.54.0 Add reservedip 10.195.54.234 d49a20b0614f "linda-bourgeois.blox.corp" "" "DHCP"
736d729
741d733
744,753c736
< Scope 10.195.55.0 Add iprange 10.195.55.1 10.195.55.254 
< Scope 10.195.55.0 set delayoffer 0
< scope 10.195.55.0 set napstate Off
< Scope 10.195.55.0 set optionvalue 3 IPADDRESS "10.195.55.1" 
< Scope 10.195.55.0 set optionvalue 51 DWORD "172800" 
< Scope 10.195.55.0 set state 1
---
758c741
---
763c746
---
773d755
776c758
< Scope 10.201.180.0 Add iprange 10.201.180.100 10.201.180.254 
---
> Scope 10.201.180.0 Add iprange 10.201.180.100 10.201.180.200 
817d798
819,852c800
< Scope 10.201.193.0 Add iprange 10.201.193.1 10.201.193.254 
< Scope 10.201.193.0 Add reservedip 10.201.193.32 c48508421b02 "asus-linux" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.33 08a3867336ff "HOME-5P0C6QA7LI" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.34 3c970e62c897 "minint-iaofoum" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.35 001c23565950 "SRV100" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.36 0800271d5d9f "ubuntu-VirtualBox" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.39 90b11c5cf2f6 "minint-qltosj31" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.40 0004a31cb195 "CK3B23321239021" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.41 001040386b22 "CK3A35810858093" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.42 00104024cbd4 "CK3A00910958040" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.43 000b6bab80e4 "CK3A35310858022" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.44 00726b313936 "CK3A35310858053" "" "BOTH"
< Scope 10.201.193.0 set delayoffer 0
< scope 10.201.193.0 set napstate Off
< Scope 10.201.193.0 set optionvalue 15 STRING "blox.corp" 
< Scope 10.201.193.0 set optionvalue 3 IPADDRESS "10.201.193.1" 
< Scope 10.201.193.0 set optionvalue 51 DWORD "172800" 
< Scope 10.201.193.0 set state 1
< Scope 10.201.194.0 Add iprange 10.201.194.1 10.201.194.254 
< Scope 10.201.194.0 set delayoffer 0
< scope 10.201.194.0 set napstate Off
< Scope 10.201.194.0 set optionvalue 15 STRING "blox.corp" 
< Scope 10.201.194.0 set optionvalue 3 IPADDRESS "10.201.193.1" 
< Scope 10.201.194.0 set optionvalue 51 DWORD "172800" 
< Scope 10.201.194.0 set state 1
---
864c812
---
876c824
---
884d831
887d833
1205c1151
---
1215c1161
---
1227c1173
---
1239c1185
---
1263c1209
---
1273d1218
1276c1221
---
1281c1226
---
1286c1231
---
1291c1236
---
1295a1241
1300c1246
---
1305c1251
---
1310c1256
---
1315,1316c1261
---
1326d1270
1337,1338c1281
---
1348,1349c1291
---
1359c1301
---
1367,1370d1308
1372c1310
---
1389c1327
< set optionvalue 44 IPADDRESS "10.61.200.202" "10.61.200.203" 
---
> set optionvalue 44 IPADDRESS "10.61.200.203" "10.61.200.202" 
1391c1329
---
1676d1613
< v4 Scope 10.195.55.0 Set NameProtection enable= 0
1680,1681d1616
< v4 Scope 10.201.193.0 Set NameProtection enable= 0
< v4 Scope 10.201.194.0 Set NameProtection enable= 0
```

Config unique to DC1
```
$ diff dc*dhcp_config | fgrep -i -v -e excluderange -e 'optionvalue 150' -e 'optionvalue 6' -e dnsconfig | grep -e '< '
< add scope 10.150.217.0 255.255.255.0 "Chicago HandHeld" ""
< add scope 10.194.51.0 255.255.255.0 "Los Angeles guestwifi" "vl143"
< add scope 10.195.53.0 255.255.255.0 "Miami VoIP" ""
< add scope 10.195.55.0 255.255.255.0 "Miami Test" ""
< add scope 10.201.193.0 255.255.255.0 "Test Vlan" "vlan 193"
< add scope 10.201.194.0 255.255.255.0 "vl 194" "vl 194"
< Scope 10.192.10.0 set optionvalue 15 STRING "mgt.Denver.blox.corp" 
< Scope 10.195.52.0 Add reservedip 10.195.52.248 00d0c9f3cbe2 "barcode3" "Miami bar code scanner" "DHCP"
< Scope 10.195.54.0 Add reservedip 10.195.54.234 d49a20d0614f "linda-bourgeois.blox.corp" "" "DHCP"
< Scope 10.195.55.0 Add iprange 10.195.55.1 10.195.55.254 
< Scope 10.195.55.0 set delayoffer 0
< scope 10.195.55.0 set napstate Off
< Scope 10.195.55.0 set optionvalue 3 IPADDRESS "10.195.55.1" 
< Scope 10.195.55.0 set optionvalue 51 DWORD "172800" 
< Scope 10.195.55.0 set state 1
< Scope 10.201.180.0 Add iprange 10.201.180.100 10.201.180.254 
< Scope 10.201.193.0 Add iprange 10.201.193.1 10.201.193.254 
< Scope 10.201.193.0 Add reservedip 10.201.193.32 c48508421b02 "asus-linux" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.33 08a3867336ff "HOME-5P0C6QA7LI" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.34 3c970e62c897 "minint-iaofoum" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.35 001c23565950 "SRV100" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.36 0800271d5d9f "ubuntu-VirtualBox" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.39 90b11c5cf2f6 "minint-qltosj31" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.40 0004a31cb195 "CK3B23321239021" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.41 001040386b22 "CK3A35810858093" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.42 00104024cbd4 "CK3A00910958040" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.43 000b6bab80e4 "CK3A35310858022" "" "BOTH"
< Scope 10.201.193.0 Add reservedip 10.201.193.44 00726b313936 "CK3A35310858053" "" "BOTH"
< Scope 10.201.193.0 set delayoffer 0
< scope 10.201.193.0 set napstate Off
< Scope 10.201.193.0 set optionvalue 15 STRING "blox.corp" 
< Scope 10.201.193.0 set optionvalue 3 IPADDRESS "10.201.193.1" 
< Scope 10.201.193.0 set optionvalue 51 DWORD "172800" 
< Scope 10.201.193.0 set state 1
< Scope 10.201.194.0 Add iprange 10.201.194.1 10.201.194.254 
< Scope 10.201.194.0 set delayoffer 0
< scope 10.201.194.0 set napstate Off
< Scope 10.201.194.0 set optionvalue 15 STRING "blox.corp" 
< Scope 10.201.194.0 set optionvalue 3 IPADDRESS "10.201.193.1" 
< Scope 10.201.194.0 set optionvalue 51 DWORD "172800" 
< Scope 10.201.194.0 set state 1
< set optionvalue 44 IPADDRESS "10.61.200.202" "10.61.200.203" 
< v4 Scope 10.195.55.0 Set NameProtection enable= 0
< v4 Scope 10.201.193.0 Set NameProtection enable= 0
< v4 Scope 10.201.194.0 Set NameProtection enable= 0
```

Config unique to DC2
```
$ diff dc*dhcp_config | fgrep -i -v -e excluderange -e 'optionvalue 150' -e 'optionvalue 6' -e dnsconfig | grep -e '> '
> add scope 10.150.217.0 255.255.255.0 "Chicago HandHeld" "vl117"
> add scope 10.194.51.0 255.255.255.0 "Los Angeles Guest" "vl143"
> add scope 10.195.53.0 255.255.255.0 "Miami VoIP" "vl150"
> Scope 10.192.10.0 set optionvalue 15 STRING "mgt.denver.blox.corp" 
> Scope 10.195.52.0 Add reservedip 10.195.52.248 00d0c9f3c3a7 "barcode3" "Miami bar code scanner" "DHCP"
> Scope 10.195.54.0 Add reservedip 10.195.54.234 d49a20b0614f "linda-bourgeois.blox.corp" "" "DHCP"
> Scope 10.201.180.0 Add iprange 10.201.180.100 10.201.180.200 
> set optionvalue 44 IPADDRESS "10.61.200.203" "10.61.200.202"
```

# Data Correction
## Bash
### DNS
_Customer states all zones' SOA RNAME should be noc.blox.corp_
- Grid DNS Properties -> General -> Basic -> Email Address (for SOA RNAME field)

Removed WINS record from
- dc1.blox-1.corp_ib_export/blox-1.corp.db
- dc2.blox-1.corp_ib_export/blox-1.corp.db

Removed records:
```
dc1.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc1.blox-1.corp.
dc1.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc2.blox-1.corp.
dc1.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc1.blox-1.corp.
dc1.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc2.blox-1.corp.
dc1.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox-1.corp.
dc1.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc1.blox-1.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc1.blox-1.corp_ib_export/wl.blox-1.corp.db:dans\302\240bb          900	A	10.150.240.30
dc1.blox-1.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox-1.corp.
dc1.blox-1.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox-1.corp.
dc1.blox-1.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox-1.corp.
dc1.blox-1.corp_ib_export/blox-1.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc1.blox-1.corp_ib_export/mgt.dallas.blox-1.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc1.blox-1.corp_ib_export/mgt.miami.blox-1.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1

dc2.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc1.blox-1.corp.
dc2.blox-1.corp_ib_export/blox-1.corp.db:voip                    3600	NS	dc2.blox-1.corp.
dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc1.blox-1.corp.
dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db:1                       3600	NS	dc2.blox-1.corp.
dc2.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox-1.corp.
dc2.blox-1.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc2.blox-1.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox-1.corp.
dc2.blox-1.corp_ib_export/wl.blox-1.corp.db:dans\302\240bb          900	A	10.150.240.30
dc2.blox-1.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox-1.corp.
dc2.blox-1.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox-1.corp.
dc2.blox-1.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox-1.corp.
dc2.blox-1.corp_ib_export/blox-1.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc2.blox-1.corp_ib_export/mgt.dallas.blox-1.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc2.blox-1.corp_ib_export/mgt.miami.blox-1.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1
```

Manually added `1.10` to `PTR esd0330.mgt.sjc.blox-1.corp.`

```
$ fgrep '\' dc*/*db | wc -l
0
$ fgrep '/' dc*/*db | wc -l
0
```

Unique A record names in each DC
```
$ fgrep A dc1*/*db | fgrep -v -e AAAA -e SOA -e CNAME | cut -d\: -f2 | awk '{print $1;}' | grep -v -e '^A$' -e '^@$' -e 600 -e 1800 | cut -d\. -f1 | sort -u > /home/training/Downloads/dc1_db_a
$ fgrep A dc2*/*db | fgrep -v -e AAAA -e SOA -e CNAME | cut -d\: -f2 | awk '{print $1;}' | grep -v -e '^A$' -e '^@$' -e 600 -e 1800 | cut -d\. -f1 | sort -u > /home/training/Downloads/dc2_db_a
$ wc -l dc*_db_a
 4053 dc1_db_a
 4053 dc2_db_a
```

## DIW
### DHCP
Fixed-address 10.150.207.243 has invalid MAC 00-00-00-00-20-72-43
- Corrected in dhcpconfig-10.61.200.202-dc1.blox.corp.xml
- Corrected in dhcpconfig-10.61.200.203-dc2.blox.corp.xml

Subnet 10.201.194.0/24 has invalid router option
- Corrected in dhcpconfig-10.61.200.202-dc1.blox.corp.xml
- dhcpconfig-10.61.200.203-dc2.blox.corp.xml does not contain this scope

DC1 Stats after fix
```
   DHCP ( Microsoft (XML) ) Configuration File Summary

   Number of Shared Networks: 0
   Number of Subnets: 53
   Number of Ranges: 53
   Number of Dynamic IP Addresses within Ranges: 17190
   Number of Fixed Addresses: 678
   Number of Roaming Hosts: 0
   Number of MAC Filters: 0
   Number of Option Filters: 0
   Number of Relay Agent Filters: 0
```

Scopes OK
```
$ fgrep -i 'add scope' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | awk '{print $6;}' | sort -u | grep -c -v -e '^$'
53
```

Ranges OK
```
$ fgrep -i 'Add iprange' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep -c Dhcp
53
```

Fixed IPs OK
```
$ fgrep -i 'Add reservedip' netsh-utf8-10.61.200.202-dc1.blox.corp.txt | fgrep -c Dhcp
678
```

DC2 Status after fix
```
   DHCP ( Microsoft (XML) ) Configuration File Summary

   Number of Shared Networks: 0
   Number of Subnets: 50
   Number of Ranges: 50
   Number of Dynamic IP Addresses within Ranges: 16508
   Number of Fixed Addresses: 667
   Number of Roaming Hosts: 0
   Number of MAC Filters: 0
   Number of Option Filters: 0
   Number of Relay Agent Filters: 0
```

Scopes OK
```
$ fgrep -i 'add scope' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | awk '{print $6;}' | sort -u | grep -c -v -e '^$'
50
```

Ranges OK
```
$ fgrep -i 'Add iprange' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | fgrep -c Dhcp
50
```

Fixed IPs OK
```
$ fgrep -i 'Add reservedip' netsh-utf8-10.61.200.203-dc2.blox.corp.txt | fgrep -c Dhcp
667
```

### DNS
DIW Advanced Options
- Create Hosts and Bulk Hosts during import -> UNCHECKED
  - Useful for comparing bare record counts once
- Remove A records that are associated with NS records -> UNCHECKED
  - Will not have A records for DCs otherwise
- Import time values from SOA record -> CHECKED
- Allow users to use the email address from -> Use Grid settings

DC1 Status
```
   DNS ( BIND 9 ) Configuration File Summary

   Total Number of Views: 0
   Total Number of Zones: 54
   Number of Forward Mapping Zones: 9
   Number of IPv4 Reverse Mapping Zones: 45
   Number of IPv6 Reverse Mapping Zones: 0

   Total Number of DNS Records: 10478
   Number of NS Records:  110
   Number of SOA Records:  54
   Number of A Records:  4,144
   Number of AAAA Records:  1
   Number of PTR Records:  5,724
   Number of CNAME Records:  388
   Number of DNAME Records:  0
   Number of MX Records:  1
   Number of SRV Records:  56
   Number of TXT Records:  0
   Number of DS Records:  0
   Number of NAPTR Records:  0
   Number of dropped Records:  0
   Number of other Records:  0
```

10478 - 110 NS - 54 SOA = 10314

Zones OK
```
$ grep -c -e '^zone "' dc1*/dc1.blox-1.corp-named.conf
54
```

Forward zones OK
```
$ grep -e '^zone "' dc1*/dc1.blox-1.corp-named.conf | fgrep -c -v in-addr.arpa
9
```

Reverse zones OK
``` 
$ grep -e '^zone "' dc1*/dc1.blox-1.corp-named.conf | fgrep -c in-addr.arpa
45
```

NS records - missing 2?
```
$ fgrep -e ' NS' -e 'NS ' dc1*/*db | fgrep -c -v ';'
112
$ fgrep NS dc1*/*db | fgrep -v ';' -c
112
```

SOA records - 1 missing? (same as zones, expected)
```
$ fgrep SOA dc1*/*db | fgrep -v ';' -c
55
```

AAAA records OK
```
$ fgrep AAAA  dc1*/*db | fgrep -c -v ';'
1
```

PTR records - missing 39? due to duplicate PTRs?
```
fgrep PTR dc1*/*db | fgrep -v ';' -c
5763
```

CNAME records OK
```
$ fgrep CNAME dc1*/*db | fgrep -v ';' -c
388
```

MX record OK
```
$ fgrep MX dc1*/*db | fgrep -v ';' -c
1
```

SRV records OK
```
$ fgrep SRV dc1*/*db | fgrep -v ';' -c
56
```



# Validation
## DNS - DC1 A/PTR records
DC1 Status
```
   DNS ( BIND 9 ) Configuration File Summary

   Total Number of Views: 0
   Total Number of Zones: 54
   Number of Forward Mapping Zones: 9
   Number of IPv4 Reverse Mapping Zones: 45
   Number of IPv6 Reverse Mapping Zones: 0

   Total Number of DNS Records: 10478
   Number of NS Records:  110
   Number of SOA Records:  54
   Number of A Records:  4,144
   Number of AAAA Records:  1
   Number of PTR Records:  5,724
   Number of CNAME Records:  388
   Number of DNAME Records:  0
   Number of MX Records:  1
   Number of SRV Records:  56
   Number of TXT Records:  0
   Number of DS Records:  0
   Number of NAPTR Records:  0
   Number of dropped Records:  0
   Number of other Records:  0
```

10478 - 110 NS - 54 SOA = 10314

Zone count OK
```
$ grep -c -e '^authzone,' dc1_dns_export_1.csv 
54
$ grep -e '^authzone,' dc1_dns_export_1.csv | fgrep -c ',FORWARD,'
9
$ grep -e '^authzone,' dc1_dns_export_1.csv | fgrep -c ',IPV4,'
45
$ grep -c -e '^forwardzone,' dc1_dns_export_1.csv 
0
```

A records OK
```
$ grep -c -e '^arecord,' dc1_dns_export_1.csv 
4144

$ grep -e '^arecord,' dc1_dns_export_1.csv |  cut -d\, -f4 | cut -d\. -f1 | sort -u > dc1_grid_names
$ wc -l dc1_grid_names dc1_db_a
  4053 dc1_grid_names
  4053 dc1_db_a
```

AAAA record OK
```
$ grep -c -e '^aaaarecord,' dc1_dns_export_1.csv 
1
```

PTR records - 4 extra?
```
$ grep -c -e '^ptrrecord,' dc1_dns_export_1.csv 
5728
```

MX record OK
```
$ grep -c -e '^mxrecord,' dc1_dns_export_1.csv 
1
```

CNAME records OK
```
$ grep -c -e '^cnamerecord,' dc1_dns_export_1.csv 
388
```

SRV records OK
```
$ grep -c -e '^srvrecord,' dc1_dns_export_1.csv 
56
```

Total records - 10 extra?
```
$ fgrep -c 'record,' dc1_dns_export_1.csv 
10324
```

## DNS - DC2 A/PTR records
DC2 Status
```
   DNS ( BIND 9 ) Configuration File Summary

   Total Number of Views: 0
   Total Number of Zones: 55
   Number of Forward Mapping Zones: 10
   Number of IPv4 Reverse Mapping Zones: 45
   Number of IPv6 Reverse Mapping Zones: 0

   Total Number of DNS Records: 10479
   Number of NS Records:  110
   Number of SOA Records:  54
   Number of A Records:  4,144
   Number of AAAA Records:  1
   Number of PTR Records:  5,725
   Number of CNAME Records:  388
   Number of DNAME Records:  0
   Number of MX Records:  1
   Number of SRV Records:  56
   Number of TXT Records:  0
   Number of DS Records:  0
   Number of NAPTR Records:  0
   Number of dropped Records:  0
   Number of other Records:  0
```

10479 - 110 NS - 54 SOA = 10315

Zone count OK
```
$ grep -c -e '^authzone,' dc2_dns_export_1.csv 
54
$ grep -e '^authzone,' dc2_dns_export_1.csv | fgrep -c ',FORWARD,'
9
$ grep -e '^authzone,' dc2_dns_export_1.csv | fgrep -c ',IPV4,'
45
$ grep -c -e '^forwardzone,' dc2_dns_export_1.csv 
1
```

A records OK
```$ grep -c -e '^arecord,' dc2_dns_export_1.csv 
4144

$ grep -e '^arecord,' dc2_dns_export_1.csv |  cut -d\, -f4 | cut -d\. -f1 | sort -u > dc2_grid_names
$ wc -l dc2_grid_names dc2_db_a 
  4053 dc2_grid_names
  4053 dc2_db_a
```

AAAA record OK
```
$ grep -c -e '^aaaarecord,' dc2_dns_export_1.csv 
1
```

PTR records - 4 extra?
```
$ grep -c -e '^ptrrecord,' dc2_dns_export_1.csv 
5729
```

MX record OK
```
$ grep -c -e '^mxrecord,' dc2_dns_export_1.csv 
1
```

CNAME records OK
```
$ grep -c -e '^cnamerecord,' dc2_dns_export_1.csv 
388
```

SRC records OK
```
$ grep -c -e '^srvrecord,' dc2_dns_export_1.csv 
56
```

Total records - 10 extra - same as DC1
```
$ fgrep -c 'record,' dc2_dns_export_1.csv 
10325
```

## DNS - Compare DC1 & DC2
```
$ diff dc1_dns_export_1.csv dc2_dns_export_1.csv 
8547a8548
> ptrrecord,ws1164.ws.blox-1.corp,,10.201.175.100,,,STATIC,,False,False,100.175.201.10.in-addr.arpa,,900,Internal_DNS_View
10392a10394,10395
> header-forwardzone,fqdn*,zone_format*,comment,disable_ns_generation,disabled,forward_to,forwarders_only,forwarding_servers,ns_group,ns_group_external,prefix,_new_prefix,view
> forwardzone,dev.blox-1.net,FORWARD,,False,False,10.148.10.53/10.148.10.53,False,False/False/ibns1.techblue.net/[],,,,,Internal_DNS_View
```

Forward zone expected

DC1 does not have the PTR for ws1164
```
$ fgrep -i ws1164 dc*/*db
dc1.blox-1.corp_ib_export/ws.blox-1.corp.db:WS1164                  900	A	10.201.175.100
dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db:100.175                 900	PTR	ws1164.ws.blox-1.corp.
dc2.blox-1.corp_ib_export/ws.blox-1.corp.db:WS1164                  900	A	10.201.175.100

$ for dbfile in `ls -1 dc*/201.10.in-addr.arpa.db`; do wc -l ${dbfile}; done
98 dc1.blox-1.corp_ib_export/201.10.in-addr.arpa.db
100 dc2.blox-1.corp_ib_export/201.10.in-addr.arpa.db

$ diff dc*/201.10.in-addr.arpa.db
23a24,25
> 
> 100.175                 900	PTR	ws1164.ws.blox-1.corp.
```

Compare DB file lengths
```
$ for dbfile in `ls -1 dc*/*db`; do wc -l ${dbfile}; done | awk '{print $2" "$1;}' | cut -d\/ -f2 | sort
```

Only `201.10.in-addr.arpa.db` is different

**DC2 has the more complete DNS data set**


## DHCP
### DC1
DC1 Status
```
   DHCP ( Microsoft (XML) ) Configuration File Summary

   Number of Shared Networks: 0
   Number of Subnets: 53
   Number of Ranges: 53
   Number of Dynamic IP Addresses within Ranges: 17190
   Number of Fixed Addresses: 678
   Number of Roaming Hosts: 0
   Number of MAC Filters: 0
   Number of Option Filters: 0
   Number of Relay Agent Filters: 0
```

Subnets OK
```
$ grep -c -e '^network,' dc1_dhcp_export_1.csv 
53
```

Ranges OK
```
$ grep -c -e '^dhcprange,' dc1_dhcp_export_1.csv 
53
```

Fixed Addresses OK
```
$ grep -c -e '^fixedaddress,' dc1_dhcp_export_1.csv 
678
```

### DC2
DC2 Status
```
   DHCP ( Microsoft (XML) ) Configuration File Summary

   Number of Shared Networks: 0
   Number of Subnets: 50
   Number of Ranges: 50
   Number of Dynamic IP Addresses within Ranges: 16508
   Number of Fixed Addresses: 667
   Number of Roaming Hosts: 0
   Number of MAC Filters: 0
   Number of Option Filters: 0
   Number of Relay Agent Filters: 0
```

Subnets OK
```
$ grep -c -e '^network,' dc2_dhcp_export_1.csv 
50
```

Ranges OK
```
$ grep -c -e '^dhcprange,' dc2_dhcp_export_1.csv 
50
```

Fixed Addresses OK
```
$ grep -c -e '^fixedaddress,' dc2_dhcp_export_1.csv 
667
```


# Real Import

Data Management -> DNS -> Grid DNS Properties -> Basic -> Email for SOA RNAME
- set to 'noc@blox.corp'

Imported
- dhcpconfig-10.61.200.202-dc1.blox.corp.xml
- dc2.blox-1.corp-named.conf
  - Create Hosts and Bulk Hosts during import -> CHECKED
  - Successful A/PTR import was validated earlier

```
$ fgrep 'hostrecord,' dc2_dns_export_2.csv | fgrep -v 'header-hostrecord,' | cut -d\, -f2 | cut -d\. -f1 > dc2_hosts
$ fgrep 'arecord,' dc2_dns_export_2.csv | fgrep -v -e 'header-arecord,' -e 'aaarecord,' | cut -d\, -f4 | cut -d\. -f1 >> dc2_hosts
$ cat dc2_hosts | sort -u > dc2_hosts_uniq
$ wc -l dc2_hosts_uniq dc2_grid_names dc2_db_a
  4053 dc2_hosts_uniq
  4053 dc2_grid_names
  4053 dc2_db_a
```

Data Management -> DHCP -> Grid DHCP Properties -> General -> Basic
- set Lease Time to 2 Hours

Data Management -> DHCP -> Grid DHCP Properties -> IPv4 DHCP Options -> Basic
- set DNS Servers to
  - 10.100.0.105
  - 10.200.0.105

Data Management -> DNS -> Internal_DNS_View -> Edit dev.blox-1.corp -> Name Servers
- set to ibns1.techblue.net (10.100.0.105) & ibns2.techblue.net (10.200.0.105)

Administration -> Named ACLs -> Add Named ACL
- Name: AD_Controllers
- Entries:
  - 10.61.200.202 (IPv4 Address)
  - 10.61.200.203 (IPv4 Address)

Data Management -> DNS -> Grid DNS Properties -> Updates -> Basic
- Allow updates from: Named ACL
  - AD_Controllers

Grid -> Grid Manager -> Grid Properties -> Security -> Basic
- set Session Timeout to 1800 seconds

Backed up to Basic_DNS-DHCP_Config.bak

**TODO - DHCP Exclusion Ranges**
