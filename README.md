# Project-2-MSU-BootCamp
___
### Day 1 - Red Team
___
> To begin this project we had launch an attack againsts a vulnerable machine, we had to gain access to the machine.  We were asked to find the IP address of the vulnerable host, find the secret directory, brute force a log in of one of the servers users, connect to the server using WebDav, upload a PHP reverse shell, use msfvenom to launch an attack against the target to gain a meterpreter session and finally find the flag.

- First I ran a nmap command to discover the hosts available within this network:
![nmap scan](https://user-images.githubusercontent.com/96896057/176231038-50e01034-7818-4772-9f70-7a10cb988e0e.png)
- Eventually the scan returned the target I was looking for:
![nmap_2](https://user-images.githubusercontent.com/96896057/176231730-0db7a09b-d9a4-4fc7-8020-fef48e89cf3e.png)
- Now that we found the target we needed to get a little more information, so we adjusted our nmap command to get a little bit more info:
![nmap_server_apached](https://user-images.githubusercontent.com/96896057/176231440-b2c22547-d190-473e-b334-8e508b907393.png)
- Seeing that the target was running an apache webserver I went to the address and began looking through the directories available until I stumbled across a blog article which mentioned that there was a directory called "secret_folder", unfortunately it appeared this folder was only visible if you were logged in as the user "ashton" so it was time to use hydra to brute force his login:
    ```
    hydra -l ashton -P /usr/shar/wordlists/rockyou.txt -s 80 -f -vV 192.168.1.105 http-get /company_folders/secretfolder
    ```
![hydra](https://user-images.githubusercontent.com/96896057/176233538-21b90ef2-7b7e-401c-a6e2-9856a7b06fb0.png)

- Withi ashton's login information in hand I was able to return to the webpage and input his credentials to view the secret_folder:
![secet_folder](https://user-images.githubusercontent.com/96896057/176232865-376a2a12-945f-4f8a-89c6-05b5d2371bee.png)

___
### Day 2 - Blue Team 
___
> In order to design a mitigation effort it is important that we analyze the events that come into our server.  Once we have the events we must analylze the events as design "Alerts" such as the one below.  Once we have an "Alert" we can then dial in the actions that triggers the alert so we don't have so many alerts coming in that it causes fatigue amoungst the analyst while also ensuring the events that need to trigger the alert are properly getting caught. 
TODO: Add image
___
### Day 3 - Mitigation
___
> How could the company mitigate the risk of these types of attacks in the future? 
TODO::