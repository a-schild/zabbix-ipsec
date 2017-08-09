# zabbix-ipsec

## Zabbix template for monitoring Openswan and Strongswan IPSEC connections

Written by andre@schild.ws

The configuration files and scripts have to be placed in the correct
directories as shown in the repository.
The locations are for Debian Wheezy with Zabbix 3.2 installed.
In addition you will need a installed fping to monitor the "quality" of your vpn

###### "Template App IPSEC VPN.xml"
    -> Import this template on your Zabbix server and assign it to your
       monitored IPSEC gateways

###### "/etc/zabbix/ipsec.conf"
    -> Place this file on your IPSEC gateway and modify it accordingly.
       This is the tunnel definition to be monitored.
       It is used for autodiscovery.

###### "/etc/zabbix/zabbix_agentd.d/ipsec_checks.conf"
    -> Place in the file on your IPSEC gateway no modification needed, 
       unless you place the external script in another location.

###### "/etc/sudoers.d/zabbix_ipsec"
    -> Make sure to have sudo installed on your IPSEC gateway.
       The Zabbix agent does not have the required privileges to monitor
       the ipsec status. With this sudo config we allow it.

###### "/usr/local/lib/zabbix/externalscripts/check_ipsec.sh"
    -> The script doing the actual ipsec tests.
       If you place this script in another location, you will have to
       change sudoers.d and the zabbix files accordingly

###### Format of ipsec.conf file:

It has to be a valid JSON file, so don't forget the , at the end of the lines

```
{
    "data":[
        { "{#TUNNEL}":"tunnel1","{#TARGETIP}":"192.168.35.1","{#SOURCEIP}":"192.168.230.4","{#RTT_TIME_WARN}":"80","{#RTT_TIME_ERR}":"150" },
        { "{#TUNNEL}":"tunnel2","{#TARGETIP}":"172.18.5.1","{#SOURCEIP}":"192.168.230.4","{#RTT_TIME_WARN}":"80","{#RTT_TIME_ERR}":"150"}
        ]
}
```

   #TUNNEL        is the name of your ipsec tunnel,
                  it has to match the name used in your ipsec config

   #TARGETIP      Use this IP as ping target to check if the tunnel is running
                  and for monitoring the RTT

   #SOURCEIP      Use this IP as ping source IP to check if the tunnel is running
                  and for monitoring the RTT
                  Since VPN gateways ususally have multiple interfaces and
                  IP addresses assigned, you have to define the source IP

   #RTT_TIME_WARN Trigger a warning if the round trip times between 
                  #SOURCEIP and #TARGETIP are higher than this value

   #RTT_TIME_WERR Trigger a error if the round trip times between 
                  #SOURCEIP and #TARGETIP are higher than this value

