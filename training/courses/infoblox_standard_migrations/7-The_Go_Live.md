# 7: The Go-Live

### Addressing "Day Of" Fears
Be the voice of Calm & Reason

"We" have done this thousands of times

This is why the runlist has been gone over multiple times

Backup
Imports
Validation
Backup
Customer pre-test
Service Migration
Infoblox validation (logs, queries, etc)
Customer post-testing
Backup
Sign-off (_in writing_)

### Review the Plan and set Expectations
Review plan _and timing_

## Moving DHCP from Legacy to Infoblox

## Converting MS AD Name Servers

## Converting BIND Name Servers

## Being the Voice of Calm & Reason
Expect the unexpected

You are the expert

Guide them through the process

**DO NOT GUESS** - Use tools to solve issues.

Avoid "I don't know" - describe what you're going to look into

MS DHCP will renew leases & response with global options even if scope is disabled



## Validation
### Monitoring
- Syslog
  - Query or Response logs
  - DHCP DORA
  - DHCP FOA state
- Capture
  - DHCP option concerns
- Lease Viewer
  - Client unable to get address
  - Syslog is faster

