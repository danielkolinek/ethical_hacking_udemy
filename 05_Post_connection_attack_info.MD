# Gathering informatioon after connecting to the network

```bash
ifconfig # discover the network ip range you are connected to

netdiscover -r 192.168.0.1/24 # see the IP and MAC of all the connected devices to the network
#-r = ip range (use the one from previous command)
```

## Zenmap
-   graphical interface of the nmap
-   generates commands for nmap
-   can use for the same scan as netdiscover to gather even more information
-   Usage:
    -   Target, can be specified as same as above for our range scan (192.168.0.1/24) or can be specific IP such as 192.168.72.130
    -   profiles
        -   Ping scan: pings every address in range, if responded, then printed 
        -   Quick scan: ping scan + shows open ports
        -   Quick scan plus: + info about OS, running services (ssh, ssl) with versions (can be used to find vulnerabilities)