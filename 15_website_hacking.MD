# How to hack a website
-   Since website is an application is an application installed on a "computer" all is applicable as before mentioned in the whole repo (server side atacks / client side attacks)
-   second possible way is the pentesting of the website
-   All was tested on the metasploitable linux running localy inside VMware. 
    -   On DVWA page (login: admin, password) was seurity set to low. 
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
    -   dirb - discover sensitive files and direstories based on wordlist
        -   can be installed on Kali linux
        -   bruteforce search on the website for all the mentioned
        -   for usage manual `man dirb`
        -   search for staff like `phpinfo` or `login` or `robots`