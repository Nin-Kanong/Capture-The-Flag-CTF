<h1 align="center"> Silentium </h1>


<img width="1575" height="251" alt="image" src="https://github.com/user-attachments/assets/20176257-184a-447e-8083-2132e96cf118" />

---

## Start Lab

In this first I need to create folder for this lab:
````
mkdir silentium
ls
cd silentium
````
<img width="466" height="251" alt="image" src="https://github.com/user-attachments/assets/a388dc08-c221-473f-b4ee-d00b0bfd9dc1" />



And after i test connection with target:
````
ping 10.129.32.218
````
<img width="547" height="180" alt="image" src="https://github.com/user-attachments/assets/a229476d-5889-40f8-b81d-196a6bd69c2a" />


### Lab:
<img width="1528" height="597" alt="image" src="https://github.com/user-attachments/assets/4435e940-cedf-4b09-b9e8-974d6569ce15" />

After in this i scan to this target:
````
nmap -sCV -T4 -O 10.129.32.218 -Pn
````
<img width="1010" height="380" alt="image" src="https://github.com/user-attachments/assets/6d479006-1453-4df3-bfbc-36e1598bae37" />

Now we see it open port:
- `22/tcp`:	SSH	OpenSSH 9.6p1 Ubuntu
- `80/tcp`:	HTTP	nginx redirecting to `silentium.htb`.


````
echo "10.129.32.218 silentium.htb" | sudo tee -a /etc/hosts
cat/etc/hosts
````
<img width="558" height="375" alt="image" src="https://github.com/user-attachments/assets/ee5b9b26-d66f-417f-bc57-3ec825e497b3" />


After check the website:
````
curl -L http://silentium.htb/
````
<img width="1209" height="800" alt="image" src="https://github.com/user-attachments/assets/2f7aca72-51dc-4aa7-915e-db6b735907ec" />

Now we see source code of this website after i open it in browser: http://silentium.htb/
<img width="1919" height="753" alt="image" src="https://github.com/user-attachments/assets/161b317e-dc6d-48fe-85f5-1aba906f2f88" />


After Fuzzing it:
````
gobuster vhost -u http://silentium.htb -w /usr/share/wordlists/dirb/common.txt --append-domain
````










