* Challenge Name: Agent T
* Platform: TryHackMe
* URL: https://tryhackme.com/room/agentt
* Type: Web
* Difficulty: Easy

# Reconnaissance

1. I entered the target URL in the browser and visited the website. It had general information like bootstrap and CSS were used.
<img width="1522" height="674" alt="agent1" src="https://github.com/user-attachments/assets/cad4ec5f-3999-40ab-9c64-86f204e000cc" />


2. I wanted to know about the target's open ports, services running on it and their version.
Therefore, I entered the command "nmap -sV 10.48.177.228" and also "curl -sI 10.48.177.228".
This revealed that php 8.1.0-dev was being used.
<img width="610" height="278" alt="agent2" src="https://github.com/user-attachments/assets/20c5ce02-7aaa-455e-b436-0ab5c0a42bdb" />


3. To do the vulnerability analysis, I used the command "searchsploit php 8.1.0" by which I got to know that the service is vulnerable to Remote Code Execution.
<img width="1524" height="601" alt="agent3" src="https://github.com/user-attachments/assets/9bed3df3-7b01-486b-a536-af25ed0b83c5" />


# Exploitation

1. I located the python exploit code given by searchsploit by "locate 49933.py". It was for Backdoor Remote Code Execution.
<img width="374" height="140" alt="agent4" src="https://github.com/user-attachments/assets/6d9ac686-1341-466e-88bd-3ae8394b3dd0" />


2. The python exploit asked for target URL. After entering that, I got a shell.
<img width="384" height="176" alt="agent5" src="https://github.com/user-attachments/assets/71a337fe-1879-4cae-8161-f3277fa09173" />


3. In the shell I located myself using "pwd" and listed current directory elements by "ls" The flag wasn't there.
But after listing the elements in root(/) directory, I found that the flag was there.
<img width="493" height="533" alt="agent6" src="https://github.com/user-attachments/assets/eee4ead7-0c1d-46cf-a083-4ff4fa4c3742" />


# What I learnt

* Outdated version can hold critical vulnerabilities.
* Flags can also be present in the root(/) directory.

# Remedies

* Use the latest php version.
