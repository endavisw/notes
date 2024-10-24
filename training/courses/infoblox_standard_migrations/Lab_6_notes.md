# DNS
## Zone Count
```
$ ls -1 dc1.blox.corp_ib_export/*db | wc -l
54
$ ls -1 dc2.blox.corp_ib_export/*db | wc -l
54
```

## Check for mutually-exclusive zones
```
$ ls -1 dc1*/*db | cut -d/ -f2  > dc1_zones
$ ls -1 dc2*/*db | cut -d/ -f2  > dc2_zones
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

## Name Servers
New delegation has popped up
```
$ fgrep -i -e ' NS' -e 'NS ' */*db | fgrep -v ';' | cut -d\: -f2- | sort -u
ha                      NS	gtm1.ha.blox.corp.
ha                      NS	gtm2.ha.blox.corp.
@                       NS	dc1.blox.corp.
@                       NS	dc2.blox.corp.
@                       NS	srs002.blox.corp.
@                       NS	srs008.blox.corp.
rob			NS      ns2.ddn.com

$ fgrep ns2.ddn.com */*db
dc1.blox.corp_ib_export/blox.corp.db:rob			NS      ns2.ddn.com
```

_srs002/srs008 can be removed_

## WINS
```
$ fgrep -A2 WINS */*db
dc1.blox.corp_ib_export/blox.corp.db:;  WINS lookup record
dc1.blox.corp_ib_export/blox.corp.db-;
dc1.blox.corp_ib_export/blox.corp.db-
dc1.blox.corp_ib_export/blox.corp.db:;@                       WINS	L2 C3600 (
dc1.blox.corp_ib_export/blox.corp.db-;                        		10.61.200.203
dc1.blox.corp_ib_export/blox.corp.db-;                        		10.61.200.202 )
--
dc2.blox.corp_ib_export/blox.corp.db:;  WINS lookup record
dc2.blox.corp_ib_export/blox.corp.db-;
dc2.blox.corp_ib_export/blox.corp.db-
dc2.blox.corp_ib_export/blox.corp.db:@                       WINS	L2 C3600 (
dc2.blox.corp_ib_export/blox.corp.db-                        		10.61.200.203
dc2.blox.corp_ib_export/blox.corp.db-                        		10.61.200.202 )
```

## Invalid Characters
### Underscore
```
$ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u
250.10.in-addr.arpa.db:1.251                   PTR	halab_vip.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:193.1                   PTR	fws_cluster.blox.corp.
250.10.in-addr.arpa.db:2.251                   PTR	halab_edge1.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:3.251                   PTR	halab_edge2.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:5.11                    PTR	lbs003_i1.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:6.11                    PTR	lbs003_i2.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:8.11                    PTR	lbs004_i1.mgt.stj.blox.corp.
250.10.in-addr.arpa.db:9.11                    PTR	lbs004_i2.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:10.25                   PTR	lbs001_i1.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:12.25                   PTR	lbs002_i1.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:16.25                   PTR	lbs005_i1.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:17.25                   PTR	lbs006_i1.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:248.25                  PTR	lbs005_vl252.mgt.stj.blox.corp.
252.10.in-addr.arpa.db:249.25                  PTR	lbs006_vl252.mgt.stj.blox.corp.
blox.corp.db:cmdbd_dfm               CNAME	srs267.blox.corp.
blox.corp.db:cmdb_dfm                CNAME	srs345.blox.corp.
blox.corp.db:condev1_primary         CNAME	srs289.blox.corp.
blox.corp.db:condev_ws               CNAME	srs1012.blox.corp.
blox.corp.db:Conprd1_primary         CNAME	srs622.blox.corp.
blox.corp.db:Conprd1_standby         CNAME	srs623.blox.corp.
blox.corp.db:Conprd2_primary         CNAME	srs682.blox.corp.
blox.corp.db:Conprd2_standby         CNAME	srs686.blox.corp.
blox.corp.db:conprod1_wc             CNAME	srs1037.blox.corp.
blox.corp.db:conprod1_ws             CNAME	srs1036.blox.corp.
blox.corp.db:conprod2_wc             CNAME	srs1071.blox.corp.
blox.corp.db:conprod2_ws             CNAME	srs1070.blox.corp.
blox.corp.db:consumer_hf_prod1_sql   CNAME	srs1010.blox.corp.
blox.corp.db:consumer_hf_prod1_ws    CNAME	srs1018.blox.corp.
blox.corp.db:consumer_hf_stag_ws     CNAME	srs1016.blox.corp.
blox.corp.db:consumer_hh_prod1_search CNAME	srs1009.blox.corp.
blox.corp.db:consumer_hh_prod1_sql   CNAME	srs1010.blox.corp.
blox.corp.db:consumer_hh_prod1_ws    CNAME	srs1007.blox.corp.
blox.corp.db:consumer_hh_stag_search CNAME	srs1008.blox.corp.
blox.corp.db:consumer_hh_stag_sql    CNAME	srs1010.blox.corp.
blox.corp.db:consumer_hh_stag_ws     CNAME	srs1004.blox.corp.
blox.corp.db:conuat1_primary         CNAME	srs429.blox.corp.
blox.corp.db:conuat1_standby         CNAME	srs428.blox.corp.
blox.corp.db:conuat_wc               CNAME	srs1018.blox.corp.
blox.corp.db:conuat_ws               CNAME	srs1019.blox.corp.
blox.corp.db:couat_ws                CNAME	srs1019.blox.corp.
blox.corp.db:dev60_primary           CNAME	srs787.blox.corp.
blox.corp.db:dev60_standby           CNAME	srs788.blox.corp.
blox.corp.db:dpdbuat1_nodea          CNAME	srs429.blox.corp.
blox.corp.db:hh1_dg_node             CNAME	srs638.blox.corp.
blox.corp.db:lmsprd_primary          CNAME	srs601.blox.corp.
blox.corp.db:lmsprd_standby          CNAME	srs653.blox.corp.
blox.corp.db:madbprd                 CNAME	lmsprd_primary.blox.corp.
blox.corp.db:mavcdbprd               CNAME	vcsprd_primary.blox.corp.
blox.corp.db:oradb_primary           CNAME	srs610.blox.corp.
blox.corp.db:oradb_standby           CNAME	srs792.blox.corp.
blox.corp.db:rmscnv_primary          CNAME	srs788.blox.corp.
blox.corp.db:rmscnv_standby          CNAME	srs787.blox.corp.
blox.corp.db:rmsdev_primary          CNAME	srs787.blox.corp.
blox.corp.db:rmsdev_standby          CNAME	srs788.blox.corp.
blox.corp.db:rmsenv1_primary         CNAME	srs787.blox.corp.
blox.corp.db:rmsenv1_standby         CNAME	srs788.blox.corp.
blox.corp.db:rmsprd_primary          CNAME	srs789.blox.corp.
blox.corp.db:rmsprd_standby          CNAME	srs790.blox.corp.
blox.corp.db:vcsprd_primary          CNAME	srs653.blox.corp.
blox.corp.db:vcsprd_standby          CNAME	srs601.blox.corp.

$ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u | wc -l
60
```

### Slashes, etc
```
$ fgrep '\' dc*/*db
dc1.blox.corp_ib_export/wl.blox.corp.db:dans\302\240bb          900	A	10.150.240.30
dc2.blox.corp_ib_export/wl.blox.corp.db:dans\302\240bb          900	A	10.150.240.30

$ fgrep '/' dc*/*db
dc1.blox.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox.corp.
dc1.blox.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox.corp.
dc1.blox.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox.corp.
dc1.blox.corp_ib_export/blox.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc1.blox.corp_ib_export/mgt.dallas.blox.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc1.blox.corp_ib_export/mgt.miami.blox.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1
dc2.blox.corp_ib_export/192.10.in-addr.arpa.db:1.10                    PTR	den-7500-eth0/0.12-int.mgt.denver.blox.corp.
dc2.blox.corp_ib_export/194.10.in-addr.arpa.db:1.10                    PTR	dal-7500-eth0/0.18-int.mgt.dallas.blox.corp.
dc2.blox.corp_ib_export/195.10.in-addr.arpa.db:1.10                    PTR	mia-7500-eth0/0.12-int.mgt.miami.blox.corp.
dc2.blox.corp_ib_export/blox.corp.db:den-7500-eth0/0.12-int.mgt.denver A	10.192.10.1
dc2.blox.corp_ib_export/mgt.dallas.blox.corp.db:dal-7500-eth0/0.18-int  A	10.194.10.1
dc2.blox.corp_ib_export/mgt.miami.blox.corp.db:mia-7500-eth0/0.12-int  A	10.195.10.1

$ fgrep '%' dc*/*db

$ fgrep '$' dc*/*db
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

## Check for mutally-exclusive ranges
```
$ grep -i iprange netsh-utf8-10.61.200.202-dc1.blox.corp.txt | grep Dhcp | awk '{print $5" range "$8" "$9;}' | sort > dc1_ranges
$ grep -i iprange netsh-utf8-10.61.200.203-dc2.blox.corp.txt | grep Dhcp | awk '{print $5" range "$8" "$9;}' | sort > dc2_ranges
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

DC1 has a wider range for 10.201.80.0

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


# Data Correction
## DNS
_Customer states all zones' SOA RNAME should be noc.blox.corp_
- Grid DNS Properties -> General -> Basic -> Email Address (for SOA RNAME field)

Removed WINS record from blox.corp

Removed
- `1.10.192.10.in-addr.arpa PTR den-7500-eth0/0.12-int.mgt.denver.blox.corp.`
- `1.10.194.10.in-addr.arpa PTR dal-7500-eth0/0.18-int.mgt.dallas.blox.corp.`
- `1.10.195.10.in-addr.arpa PTR mia-7500-eth0/0.12-int.mgt.miami.blox.corp.`
- `den-7500-eth0/0.12-int.mgt.denve.blox.corp.  A 10.192.10.1`
- `dal-7500-eth0/0.18-int.mgt.dallas.blox.corp. A 10.194.10.1`
- `mia-7500-eth0/0.12-int.mgt.miami.blox.corp.  A 10.195.10.1`
- `dans\302\240.bb.wl.blox.corp.                A 10.150.240.30`


## DHCP
Need to check default lease time and default domain-name

Subnet 10.201.194.0/24 - Changed router from 10.201.193.1 to 102.201.194.1

Fixed IP 10.150.207.243 - Changed MAC to 00-00-00-00-00-00


# Validation

Import DC1 DNS - Save Log - Export:
- Zones
- Hosts
- A
- AAAA
- PTR
- NS
- MX
- CNAME
- SRV

Compare numbers from DIW

Restore Grid

Import DC2 DNS - Save Log - Export DNS CSVs

Compare numbers from DIW

Restore Grid

Import DC1 DNS - Save Log - Export:
- Ranges
- Fixed Addresses
- Hosts

Compare numbers from DIW

Restore Grid

Import DC2 DNS - Save Log - Export:
- Ranges
- Fixed Addresses
- Hosts

Compare numbers from DIW

Compare range exclusions from each DHCP. Modify exclusions in DC1 CSV, re-import

## DNS
### DC1

DIW Results
```
   DNS ( BIND 9 ) Configuration File Summary

   Total Number of Views: 0
   Total Number of Zones: 54
   Number of Forward Mapping Zones: 9
   Number of IPv4 Reverse Mapping Zones: 45
   Number of IPv6 Reverse Mapping Zones: 0

   Total Number of DNS Records: 10486
   Number of NS Records:  115
   Number of SOA Records:  54
   Number of A Records:  4,145
   Number of AAAA Records:  2
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

10486 - 115 NS - 54 SOA = 10317

Zone count OK
```
$ grep -c -e '^authzone,' l6_dc1_dns.csv 
54
$ grep -e '^authzone,' l6_dc1_dns.csv | fgrep -c ',FORWARD,'
9
$ grep -e '^authzone,' l6_dc1_dns.csv | fgrep -c ',IPV4,'
45
```

AAAA records OK
```
$ grep -c -e '^aaaarecord,' l6_dc1_dns.csv
2
```

MX records OK
```
$ grep -c -e '^mxrecord,' l6_dc1_dns.csv 
1
```

CNAME records OK
```
$ grep -c -e '^cnamerecord,' l6_dc1_dns.csv 
388
```

SRV records OK
```
$ grep -c -e '^srvrecord,' l6_dc1_dns.csv 
56
```

### If DIW creates Hosts
A records - missing 56!
```
$ grep -c -e '^arecord,' -e '^hostrecord,' l6_dc1_dns.csv 
4089
```

PTR records - missing 52!
```
$ grep -c -e '^ptrrecord,' -e '^hostrecord,' l6_dc1_dns.csv 
5673
```


All records - 101 missing!
```
$ fgrep -c 'record,' l6_dc1_dns.csv 
6382
$ grep -c -e '^hostrecord,' l6_dc1_dns.csv
3834
```
10216

64 A records have multiple IPs, which would mean 131 fewer Host records
```
grep -e '^arecord,' l6_dc1_dns_nohosts.csv | cut -d\, -f4 | sort | uniq -c | sort -r
```

### If DIW does not create Hosts
A records OK
```
$ grep -c -e '^arecord,' -e '^hostrecord,' l6_dc1_dns.csv 
4145
```

PTR records OK - 4 extra?
```
$ grep -c -e '^ptrrecord,' -e '^hostrecord,' l6_dc1_dns.csv 
5729
```

All records OK
```
$ fgrep -c 'record,' l6_dc1_dns.csv 
10327
```

### Compare Number of Unique DNS Names
Unique A records in DC1
```
$ fgrep A dc1*/*db | fgrep -v -e AAAA -e SOA -e CNAME | cut -d\: -f2 | awk '{print $1;}' | grep -v -e '^A$' -e '^@$' -e 600 -e 1800 | cut -d\. -f1 | sort -u > dc1_db_a.txt
```

Any missing from No-Hosts A/PTR import?
```
$ grep -e '^arecord,' l6_dc1_dns_nohosts.csv | cut -d, -f4 | cut -d\. -f1 | sort -u >  grid_names
$ grep -c -i -v -f grid_names dc1_db_a.txt
0
```

Any missing from Host-convered import?
```
$ fgrep -e 'arecord,' l6_dc1_dns_hosts.csv | grep -v -e header -e aaaa | cut -d, -f4 | cut -d\. -f1 | sort -u > grid_hosts
$ fgrep -e 'hostrecord,' l6_dc1_dns_hosts.csv | grep -v -e header | cut -d, -f2 | cut -d\. -f1 | sort -u >> grid_hosts
$ cat grid_hosts | sort -u > grid_hosts_uniq
$ grep -c -i -v -f grid_hosts_uniq dc1_db_a.txt
0
```

Counts of each
```
$ wc -l dc1_db_a.txt grid_names grid_hosts_uniq
  4054 dc1_db_a.txt
  4054 grid_names
  4054 grid_hosts_uniq
```

## DHCP
