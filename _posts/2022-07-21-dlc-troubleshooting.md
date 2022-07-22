---
title: QRadar DLC Troublehooting
date: 2022-07-21 10:00:00
categories: [QRadar, DLC]
tags: [qradar, dlc]     # TAG names should always be lowercase
---


# DLC Troubleshooting


## Network checking

### TCPdump

TCPDUMP to see if the networking is OK and you are delivering the logs to the DLC box. In my setup the
IP address of the DLC box is 172.16.60.77 and the logs comes from 172.16.60.10. You may need to install tcpdump by issuing 

````shell
yum install tcpdump
````


So from the DLC machine issue this command"


````shell
tcpdump -i ens33 -n "src host 172.16.60.10 and dst host 172.16.60.77"
````

### Open Ports

Check your ports. So from the DLC machie issue this command:

````shell
netstat -tulnp | grep 32500
netstat -putona | grep 32500
````


You should see something like this:

````
      0      0 :::32500                :::*                                103777/java                     
````


### Firewall

Let us make sure your *firewall setting* are OK. So from the DLC machine issue this command:

````shell
firewall-cmd --list-all
````


You should see something like this:

````
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens33
  sources: 
  services: ssh dhcpv6-client
  ports: 1514/tcp 1514/udp 514/udp 514/tcp
  protocols: 
  masquerade: no
  forward-ports: port=514:proto=tcp:toport=1514:toaddr=
	port=514:proto=udp:toport=1514:toaddr=
  source-ports: 
  icmp-blocks: 
  rich rules: 
````

## DLC - Check incoming events

Command to determine if the logs are getting into the DLC service. So from the DLC machine issue this command:

````shell
 /opt/ibm/si/services/dlc/current/script/jmx.sh -p 7787 "com.q1labs.sem:application=dlc.dlc,type=destinations,id=SECStoreForwardDestination" EventsSeen
````



You should see something like this:


````output
com.q1labs.sem:application=dlc.dlc,type=destinations,id=SECStoreForwardDestination
----------------------------------------------------------------------------------
EventsSeen: 870 
````


## DLC - Check outgoing events

Command to determine if the logs are getting out of the DLC service. So from the DLC machine issue this command:

````shell
 /opt/ibm/si/services/dlc/current/script/jmx.sh -p 7787 "com.q1labs.sem:application=dlc.dlc,type=sources,name=Syslog Source" Posted
````


You should see something like this:

````output
com.q1labs.sem:application=dlc.dlc,type=sources,name=Syslog Source
------------------------------------------------------------------
Posted: 870
````

## EP - Check incoming from DLC

Command to see if the logs are getting to QRadar from DLC. So from the QRadar machine issue this command

````
/opt/qradar/support/jmx.sh -p 7787 "com.q1labs.sem:application=ecs-ec-ingress.ecs-ec-ingress,type=sources,name=IBMQRadarDLC Source" Posted
````


You should see something like this:


````
com.q1labs.sem:application=ecs-ec-ingress.ecs-ec-ingress,type=sources,name=IBMQRadarDLC Source
----------------------------------------------------------------------------------------------
Posted: 870
````

