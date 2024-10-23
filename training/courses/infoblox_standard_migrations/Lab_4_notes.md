# DNS
## Zone count
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ ls -1 dc1.blox.corp_ib_export/*db | wc -l
54
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ ls -1 dc2.blox.corp_ib_export/*db | wc -l
54
```

## Name Servers
Number of servers
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep -i -e ' NS' -e 'NS ' */*db | fgrep -v ';' | cut -d\: -f2- | sort -u
ha                      NS	gtm1.ha.blox.corp.
ha                      NS	gtm2.ha.blox.corp.
@                       NS	dc1.blox.corp.
@                       NS	dc2.blox.corp.
@                       NS	srs002.blox.corp.
@                       NS	srs008.blox.corp.
```

ha.blox.corp is delegated, probably OK
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep -i -e gtm1.ha.blox.corp -e gtm2.ha.blox.corp */*db
dc1.blox.corp_ib_export/blox.corp.db:ha                      NS	gtm2.ha.blox.corp.
dc1.blox.corp_ib_export/blox.corp.db:ha                      NS	gtm1.ha.blox.corp.
dc2.blox.corp_ib_export/blox.corp.db:ha                      NS	gtm2.ha.blox.corp.
dc2.blox.corp_ib_export/blox.corp.db:ha                      NS	gtm1.ha.blox.corp.
```

Customer states SRS002 & SRS008 no longer around, from old project.
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep -i -e srs002.blox.corp -e srs008.blox.corp */*db
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox.corp.
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
dc1.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
dc2.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.

training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ grep NS dc*/150.10.in-addr.arpa.db
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:;  Zone NS records
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	dc1.blox.corp.
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	dc2.blox.corp.
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox.corp.
dc1.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:;  Zone NS records
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	dc1.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	dc2.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs002.blox.corp.
dc2.blox.corp_ib_export/150.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ grep NS dc*/253.10.in-addr.arpa.db
dc1.blox.corp_ib_export/253.10.in-addr.arpa.db:;  Zone NS records
dc1.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	dc1.blox.corp.
dc1.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	dc2.blox.corp.
dc1.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
dc2.blox.corp_ib_export/253.10.in-addr.arpa.db:;  Zone NS records
dc2.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	dc1.blox.corp.
dc2.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	dc2.blox.corp.
dc2.blox.corp_ib_export/253.10.in-addr.arpa.db:@                       NS	srs008.blox.corp.
```

## Invalid Records
### WINS
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep -A2 WINS dc*/*db
dc1.blox.corp_ib_export/blox.corp.db:;  WINS lookup record
dc1.blox.corp_ib_export/blox.corp.db-;
dc1.blox.corp_ib_export/blox.corp.db-
dc1.blox.corp_ib_export/blox.corp.db:@                       WINS	L2 C3600 (
dc1.blox.corp_ib_export/blox.corp.db-                        		10.61.200.203
dc1.blox.corp_ib_export/blox.corp.db-                        		10.61.200.202 )
--
dc2.blox.corp_ib_export/blox.corp.db:;  WINS lookup record
dc2.blox.corp_ib_export/blox.corp.db-;
dc2.blox.corp_ib_export/blox.corp.db-
dc2.blox.corp_ib_export/blox.corp.db:@                       WINS	L2 C3600 (
dc2.blox.corp_ib_export/blox.corp.db-                        		10.61.200.203
dc2.blox.corp_ib_export/blox.corp.db-                        		10.61.200.202 )
```

WINS resolution goes to the ADCs
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep -e 10.61.200.202 -e 10.61.200.203 dc*/blox.corp.db | fgrep A | fgrep -v -e '@' -e Zones -e '._'
dc1.blox.corp_ib_export/blox.corp.db:dc1                     1200	A	10.61.200.202
dc1.blox.corp_ib_export/blox.corp.db:dc2                     A	10.61.200.203
dc1.blox.corp_ib_export/blox.corp.db:                        600	A	10.61.200.202
dc1.blox.corp_ib_export/blox.corp.db:                        600	A	10.61.200.202
dc2.blox.corp_ib_export/blox.corp.db:dc1                     1200	A	10.61.200.202
dc2.blox.corp_ib_export/blox.corp.db:dc2                     A	10.61.200.203
dc2.blox.corp_ib_export/blox.corp.db:                        600	A	10.61.200.202
dc2.blox.corp_ib_export/blox.corp.db:                        600	A	10.61.200.202
```

### Invalid Characters
Many non-AD records have underscores
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u
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

training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '_' dc*/*db | fgrep -v -e ':_' -e '._' | cut -d\/ -f2- | sort -u | wc -l
60
```

Some have slashes
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '\' dc*/*db
dc1.blox.corp_ib_export/wl.blox.corp.db:dans\302\240bb          900	A	10.150.240.30
dc2.blox.corp_ib_export/wl.blox.corp.db:dans\302\240bb          900	A	10.150.240.30

training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '/' dc*/*db
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
```

No % characters
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '\%' dc*/*db | wc -l
0
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '%' dc*/*db | wc -l
0
```

No $ characters
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '\$' dc*/*db | wc -l
0
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets $ fgrep '$' dc*/*db | wc -l
0
```

# DHCP
## Scopes
Unique DHCP scopes
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets/dhcp_export $ fgrep 'add scope' netsh-utf8-* | awk '{print $6" "$7;}' | sort -u 
10.150.202.0 255.255.255.0
10.150.204.0 255.255.252.0
10.150.208.0 255.255.252.0
10.150.217.0 255.255.255.0
10.150.224.0 255.255.240.0
10.150.240.0 255.255.252.0
10.192.10.0 255.255.255.0
10.192.12.0 255.255.252.0
10.192.50.0 255.255.255.0
10.192.51.0 255.255.255.0
10.192.52.0 255.255.255.0
10.192.53.0 255.255.255.0
10.192.54.0 255.255.255.0
10.192.56.0 255.255.252.0
10.192.60.0 255.255.255.0
10.194.10.0 255.255.255.0
10.194.50.0 255.255.255.0
10.194.51.0 255.255.255.0
10.194.52.0 255.255.255.0
10.194.53.0 255.255.255.0
10.194.54.0 255.255.255.0
10.194.56.0 255.255.252.0
10.194.60.0 255.255.255.0
10.195.10.0 255.255.255.0
10.195.50.0 255.255.255.0
10.195.51.0 255.255.255.0
10.195.52.0 255.255.255.0
10.195.53.0 255.255.255.0
10.195.54.0 255.255.255.0
10.195.55.0 255.255.255.0
10.195.60.0 255.255.255.0
10.201.175.0 255.255.255.0
10.201.180.0 255.255.255.0
10.201.193.0 255.255.255.0
10.201.194.0 255.255.255.0
10.201.200.0 255.255.255.0
10.202.224.0 255.255.255.0
10.250.250.0 255.255.255.0
10.252.0.0 255.255.0.0
10.254.0.0 255.255.240.0
10.40.0.0 255.255.0.0
10.41.0.0 255.255.0.0
10.42.0.0 255.255.0.0
10.43.0.0 255.255.0.0
10.50.0.0 255.255.0.0
10.51.0.0 255.255.0.0
10.52.0.0 255.255.0.0
10.53.0.0 255.255.0.0
10.61.202.0 255.255.255.0
10.61.203.0 255.255.255.0
10.61.204.0 255.255.255.0
10.61.205.0 255.255.255.0
10.64.0.0 255.255.0.0
```

Unique scope count
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets/dhcp_export $ fgrep 'add scope' netsh-utf8-* | awk '{print $6" "$7;}' | sort -u | wc -l
53
```

Same number of scopes in dhcpconfig dumps
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets/dhcp_export $ fgrep -A1 '<Scope>' dhcpconfig* | fgrep ScopeId | cut -d\< -f2 | cut -d\> -f2 | sort -u | wc -l
53

training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets/dhcp_export $ fgrep -A1 '<Scope>' dhcpconfig* | fgrep ScopeId | cut -d\< -f2 | cut -d\> -f2 | sort -u
10.150.202.0
10.150.204.0
10.150.208.0
10.150.217.0
10.150.224.0
10.150.240.0
10.192.10.0
10.192.12.0
10.192.50.0
10.192.51.0
10.192.52.0
10.192.53.0
10.192.54.0
10.192.56.0
10.192.60.0
10.194.10.0
10.194.50.0
10.194.51.0
10.194.52.0
10.194.53.0
10.194.54.0
10.194.56.0
10.194.60.0
10.195.10.0
10.195.50.0
10.195.51.0
10.195.52.0
10.195.53.0
10.195.54.0
10.195.55.0
10.195.60.0
10.201.175.0
10.201.180.0
10.201.193.0
10.201.194.0
10.201.200.0
10.202.224.0
10.250.250.0
10.252.0.0
10.254.0.0
10.40.0.0
10.41.0.0
10.42.0.0
10.43.0.0
10.50.0.0
10.51.0.0
10.52.0.0
10.53.0.0
10.61.202.0
10.61.203.0
10.61.204.0
10.61.205.0
10.64.0.0
```

## Other DNS Servers
Guest wireless uses Google DNS servers
```
training@jump-desktop:~/Desktop/ps-nios/Lab Data Sets/Lab-4-bloxcorp_dns_dhcp_datasets/dhcp_export $ for network in `fgrep 8.8. netsh-utf8-10.61.200.20* | cut -d\: -f2- | awk '{print $5;}' | sort -u`; do fgrep ${network} netsh-utf8* | fgrep 'add scope'; done
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
