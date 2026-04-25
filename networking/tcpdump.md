tcpdump
=======


## Filter for DHCP/BOOTP

```
tcpdump -i any -vvv -s 1500 '(port 67 or port 68)'
```

```
tcpdump -i any -vvv -s 1500 '((port 67 or port 68) and (udp[8:1] = 0x1))'
```
