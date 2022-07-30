# Project-2-MSU-BootCamp
___
***Links:***
- [Day 1](https://github.com/RyanLinscott/Project-2-MSU-BootCamp#day-1---red-team)
- [Day 2](https://github.com/RyanLinscott/Project-2-MSU-BootCamp#day-2---blue-team)
- [Day 3](https://github.com/RyanLinscott/Project-2-MSU-BootCamp#day-3---mitigation)

### Day 1 - Red Team
___
> To begin this project we had launch an attack againsts a vulnerable machine, we had to gain access to the machine.  We were asked to find the IP address of the vulnerable host, find the secret directory, brute force a log in of one of the servers users, connect to the server using WebDav, upload a PHP reverse shell, use msfvenom to launch an attack against the target to gain a meterpreter session and finally find the flag.

- First I ran a nmap command to discover the hosts available within this network:
    - "-sS" quickly sends TCP SYN stealth scan
    - "-Pn" tells nmap you already assume the host is up so there is no need to ping it first
    - "-vv" gives verbose output

![nmap scan](https://user-images.githubusercontent.com/96896057/176231038-50e01034-7818-4772-9f70-7a10cb988e0e.png)
- Eventually the scan returned the target I was looking for:
![nmap_2](https://user-images.githubusercontent.com/96896057/176231730-0db7a09b-d9a4-4fc7-8020-fef48e89cf3e.png)
- Now that we found the target we needed to get a little more information, so we adjusted our nmap command to get a little bit more info:
![nmap_server_apached](https://user-images.githubusercontent.com/96896057/176231440-b2c22547-d190-473e-b334-8e508b907393.png)
    - "-Pn" tells nmap you already assume the host is up so there is no need to ping it first
    - "-p 80" tells nmap specifically which port to scan
    - "-sT" tells nmap to use a TCP scan, which is done by default and could have been ommitted
    - "-sV" tells nmap to check for the service version of what it is scanning
    
- Seeing that the target was running an apache webserver I went to the address and began looking through the directories available until I stumbled across a blog article which mentioned that there was a directory called "secret_folder", unfortunately it appeared this folder was only visible if you were logged in as the user "ashton" so it was time to use hydra to brute force his login:
    ```
    hydra -l ashton -P /usr/shar/wordlists/rockyou.txt -s 80 -f -vV 192.168.1.105 http-get /company_folders/secretfolder
    ```
![hydra](https://user-images.githubusercontent.com/96896057/176233538-21b90ef2-7b7e-401c-a6e2-9856a7b06fb0.png)

- Within ashton's login information in hand I was able to return to the webpage and input his credentials to view the secret_folder:
![secet_folder](https://user-images.githubusercontent.com/96896057/176232865-376a2a12-945f-4f8a-89c6-05b5d2371bee.png)
- Once inside the folder, "ashton" gave us the exact instructions on how to connect to the server using WebDav:
![secret_folder](https://user-images.githubusercontent.com/96896057/176234505-173b6d1b-f0fd-408c-bc0d-c39aff3a534a.png)
- Inside of the WebDav connections we found the passwd.dav file, which contained the password to "ryan's" account:
![ryan_passwd_dav](https://user-images.githubusercontent.com/96896057/176234787-0731b214-c701-4775-97e1-28240cc1da7b.png)
- Viewing the contents it gave me a MD5 hash we could then crack using a online service such as CrackStation:
![ryan_password](https://user-images.githubusercontent.com/96896057/176235042-50d11fcf-f147-4a70-981f-c13df6866abe.png)
- Using MSFVenom a new shell script was created that when loaded to the server would allow me to gain access, once I added it to the server by simply dragging and dropping it from my host:
![php_shell](https://user-images.githubusercontent.com/96896057/176235590-e6abd385-62b2-4b26-9762-3d6f96e84adc.png)
- Once the script was loaded to the server all that was left was to click on the script which would give me access to the server I loaded the reverse shell onto and from there I simply had to find the file, which happened to be name "flag.txt".  Upon running the cat command it presented the flag "b1ng0w@5h1sn@n0":
![flag_found](https://user-images.githubusercontent.com/96896057/176236349-5cc8ae0f-c2f1-4a5b-a953-a5464dc3132f.png)

___
### Day 2 - Blue Team 
___
> In order to design a mitigation effort it is important that we analyze the events that come into this server.  Once the events were inside the SIEM, then "Alerts" such as the one below could be carefully crafted.  Once an "Alert" has been created it is then possible to dial in the threshold for actions that triggers the alert.  It is important to find the balance between having too many alerts and alerting on actual issues which need further attention.  Without properly calibrated alerts it can cause fatigue within the analysts and possibly allow a perpetrator to move practically unchecked within a network. 

![Part2_BruteForce](https://user-images.githubusercontent.com/96896057/176238112-bd541620-84de-4b6a-a697-415505d96e14.png)

- As you can see above the alert was created with Splunk that would alert the analyst anytime a IP address received more than ten, 404 status codes within a ten minute window.  This limit was created after judging events created following a directory traversal attack against the website.  The perpetrator was trying to find or gain access to all available webpages, many of which did not exist using the tool "dirbuster".  Even though not all 404 status codes are a bad, the average user would not be very likely to receive them that frequently in such a short span of time.  In the case of the attack which was analyzed there was over 3000 within a 30 second window, this most definitely tips off to the use of a automated process to launch this specific attack.
___
### Day 3 - Mitigation
___
> How could the company mitigate the risk of these types of attacks in the future?  How does a company go about setting up alerts within a new monitoring system?
-   As technology continues to grrow and threats continue to grow with it, companies must ramain vigilant to protect not only themselves and their employees, but also their customers.  When deciding how to dial in alerts it is important that the company understands what their "normal" network traffic looks like.  This data should not just be and average, but should also depict what the network is experiencing on it's busiest days and well as it's slower days.  This baseline is a crucial step in properly setting up a SIEM to monitor your network.  Unfortunately as automated as a compaany would like it to be, the data moving through the wire is live and therefore this is not a situation where you can set it and forget it.  As attacks and methods continue to get more and more advanced their ability to hide in plain sight also continues to grow.  This requires a constant watchful eye to alert and restrict actions which might lead to any part of the network being compromised.  In some of the examples of attacks used during this project at various steps of the process there was a multitude of things the company could have done to prevent further access from happening in their system.  For instance when trying to brute force into the account belonging to "ashton" a company could set a standardized policy that limits the number of logins and user can fail within an hour.  The compay could have also set up some sort of multi-factor authentification that would require the user to have more than just the log in credentials.

    In another attack that was launch against the server the method of Cross Site Scripting was used.  This allowed the threat actor to insert potentially malicious code directly into the website, which would then load whenever the user accessed the website.  This could range all the way from a simple annoying pop up, to a full on malicioud code that could steal information.  To prevent somthing like this the analyst must realize the attack is happening and tune the alerts so that any input from the client which contains characters the user should not be entering such as "<" and ">" along with their coressponding hexadecimal characters. It is important input validation is in place not only to prevet this kind of attack, but also to protect against other forms of attacks such as SQL injections.  Using MariaDB it would be crucial for an alert be set to trigger anytime the "%" character is entered and that is the wildcard that can allow the actor to gain even further access to parts of the database they already should not be able to see.  Once again it is important that when an event such as this is found, to immediately create an alert to help block and prevent a simliar event from happening again.  User's entering the unapproved character's should recieve error codes and hopefully have their IP addresses logged to help prevent further hassle from that IP.  Of course hackers can always gain new IP's, but they are most likely trying to find the quickest in and out they can.  If they have to jump through the hoop of getting a mulitude of IP addresses just to attack a single target, they are most likely going to move onto a target that doesn't have restrictions in place and will be a little easier to get  into.

    The process of fine tuning alerts comes down to experience, unfortunately there is no one size fits all that can cover every organization or even every instance.  With enough time and familiarity with the network being monitored it should be able though to get it "in the ball park" so to speak with regards to baselines.  Will there be alerts that trigger that shouldn't have? Yes, but this serves as a good oppurtunity to further improve your thresholds and decide if that alert was too close for comfort and maybe you need to decrease the limit or if the alert just clearly missed the mark and was set way to high.  Once again it cannot be stressed enough that too many alerts might cause actual alerts to get missed, but not enough alerts might allow a malicioud attack to happen.  A company must decide how it wishes to handle these alerts and what quantity they deem as just the right amount.  In my own personal opinion as someone who is just beginning in this field I probably would err on the side of having just over the amoung of alerts I would deem enough.  I would rather start with slightly too many and work at narrowing it down to what is the highest severity than to limit myself and receive not enough that an actual attack or threat got through.   