# Wireless networking basics 01

## Intro
- router is the default gateway to the internet
- to get default gateway ip adress use `ip route` command, use this ip to get to router settings in web browser

## Change MAC adress
Can be changed easily in sw (not hw) in command line:

```bash
ifconfig # see the current networks, value “ether” shows the mac adress

ifconfig wlan0 down # shut down the wlan0, always needs to be shut for any configuration

ifconfig wlan0 hw ether 00:11:22:33:44:55 # set new ip address

ifconfig wlan0 up # start up the wlan0 again
```


## Change mode of operation of wireless adapter
All the data "fly arround" the adapter so it can be cofigured to capture all the data, not only the ones, that are ment for him. Types of modes:
- Managed: capture only data, that are ment for you
- Monitor: capture all data

```bash
iwconfig # see only wireless devices, value Mode is the desired value.

ifconfig wlan0 down # changing configuration => shu down the interface

airmon-ng check kill # not mandatory, for better results, kills network manager (disconecting from network)

iwconfig wlan0 mode monitor # change the monde of wlan0 to monitor

ifconfig wlan0 up # start up the wlan0 again

iwconfig # check if the mode was changed
```
