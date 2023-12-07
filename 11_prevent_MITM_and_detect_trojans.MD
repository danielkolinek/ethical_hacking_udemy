# Prevention against MITM attacks:
-   ensure you are not MITM (look on the end of MITM lecture)
    -   use tool like xarp
    -   use static tables
    -   don't use untrusted or unknown networks
-   use https pages -> not possible to backdoor https packets on fly or make fake updates
-   check file's MD5 checksum after download
    -   usually provided file has MD5 check sum. There are tools for win and linux to do the check
    -   example for windows: [https://www.winmd5.com/](https://www.winmd5.com/)

# Trojans detection 
-   This works to the Trojans described in notes 06-10_*.MD
-   Check the file properties (it show that the file is executable, if should not be)
-   Check the open connections (for example in Resource monitor)
    -   check the remote addresses (it is possible to use reverse dns lookup on website like https://dnschecker.org/reverse-dns.php)

## Sandbox Trojan detection
-   run file in sandbox service (virtual machine)
-   specific websites are created for this (like `https://www.hybrid-analysis.com/`), just upload the file and let it scan/run the file and see the report