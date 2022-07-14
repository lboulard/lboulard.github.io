## JetBrains Custom VM Options

Timeout value is in milliseconds.
Values here are 1 minutes for connection and 3 minutes for read timeout.

```properties
-Dide.win.frame.decoration=false
-Deditor.full.width.scrollbar=true
-Didea.connection.timeout=60000
-Didea.read.timeout=180000
```

### Reserve TCP ports 6905 - 7004

Ensure that 

```batch
netsh int ipv4 add excludedportrange protocol=tcp startport=6905 numberofports=100
```

References

- <https://github.com/docker/for-win/issues/3171#issuecomment-459205576>
- <https://blogs.msdn.microsoft.com/whereismysolution/2018/05/11/how-to-use-reserved-port-in-nettcpbinding-and-basichttpbinding-in-a-wcf-application/>

### Resolve "Address already in use: bind"

```batch
netsh int ipv4 set dynamicport tcp start=49152 num=16383
netsh int ipv4 set dynamicport udp start=49152 num=16383
```

Or, more aggressively.

```batch
net stop winnat
net start winnat
```
