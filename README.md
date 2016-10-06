#Goavail
===============
Goavail is an IP monitoring and fast DNS failover agent written in Go.  

This personal project was done as part of Nitro Software's hack week.  We run our systems across multiple AWS Availability Zones, but are still succeptible if an AZ itself goes down.  This tool is meant to serve as an external monitoring agent, able to trigger DNS A record updates should an AWS zone go down.  The agent continuously pings a set of IP Addresses (EIPs) in a certain domain, noting their health.  After three consecutive misses, the IP and its associated domains are removed from DNS.  The agent will continue to monitor the EIP and should come back alive for some reason, will enter it back into service with the appropriate API calls through the DNS interface.  

These EIPs live in separate AZs, and since we're also proxying these EIPs through our CDN/DNS, this failover should be a matter of seconds.

##Cluster Mode
Cluster mode uses a fork of the [memberlist library] (https://github.com/hashicorp/memberlist) to verify peers' health.  If a node sees an IP failure, it will notify its peers before invoking the DNS interface to take the failed IP out of the Pool.  A node will only remove an IP from the pool once its noted a failure and recieved confirmation from a peer.  

To run in Cluster mode simply add the "peers" and local_addr entries in the toml file.  

##Reload 
The tool also supports configuration reload.  Simply send a SIGHUP signal to the process and it will reload the modified configuration file

##Configuration Options
See goavail.toml for a configration example