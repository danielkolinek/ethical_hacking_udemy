# XSS vulnerabilities
-   cross site scripting vulnerabilities
-   everything was tested with Metasploitable Linux, DVWA
-   allow attacker to inject js code => code is executed when the page loads on the client machine
-   three main types:
    1)  persistent/stored XSS
        -   stored into db
    2)  Reflected XSS
        -   executed only if opened special URL specified by attacker
    3)  DOM based XSS
        -   will be written and run on client side, completely without webserver communication
- to discover XSS vulnerabilities works similar approach as for SQL injection. Try to test input boxes and URL parameters.

## Reflected XSS
-   none persistent, not stored, attacker has to send the code to victim (client) and it will be ran on their machine (not server)
-   example on inject to textbox:
    -   `<script>alert("XSS works")</script>`
-   example in the URL (special characters might be better to convert to encode as described in 16_SQL_injection.MD):
    -   `url/?name=%3Cscript%3Ealert%28%22XSS works%22%29%3C%2Fscript%3E`

## Stored XSS
-   on sites, where input from user is shown, like comments to some post
-   again put any code inside the textbox
-   this can be used also for hooking any browser of any user, that visits the website. To generate hook, use for example BeEF as described in `10_hooking_browsers.MD`

## Prevent XSS vulnerabilities
-   minimalize the usage of user input on html
-   escape any untrusted input before inserting it to page => convert it to html characters, so the script won't be run.