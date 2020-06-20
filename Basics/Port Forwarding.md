# **Port Forwarding**

Port forwarding is a traffic manipulation technique.
```
1. apt-get install rinetd
2. cat /etc/rinetd.conf
3. <bindaddress	bindport	connectaddress	connectport>
	<w.x.y.z		53		a.b.c.d			80>
```
## **SSH Tunneling**
```
1. Local Port forwarding
	a. Allows us to tunnel a local port to remote server, using SSH as the transport protocol.
	b. We can use the SSH local port forwarding feature to bypass the existing restriction using syntax similar to the following:
		1. ssh <gateway> -L <local port to listen>:<remote host>:<remote port>
	c. Once the tunnel is created, we browse to local port 8080, which redirects our traffic through the outbound SSH tunnel on TCP port 53 on our home machine then to the server.
2. Remote Port Forwarding
	a. ssh <gateway> -p 53 	-R <remote port to bind>:<localhost>:<local port>
3. Dynamic Port Forwarding
	a. ssh -D <local proxy port> <target>
4. Reverse SSH Tunnel
	a. ssh -f -N -R 2222:127.0.0.1:22 root@<ip>
```

## **HTTP Tunnelling**
```
1. nc -vvn <IP> <Port>
2. CONNECT <IP>:<Port> HTTP/1.0
```
