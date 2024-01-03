#ifconfig #network #네트워크 

```
% ifconfig
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
	options=1203<RXCSUM,TXCSUM,TXSTATUS,SW_TIMESTAMP>
	inet 127.0.0.1 netmask 0xff000000 
	inet6 ::1 prefixlen 128 
	inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1 
	nd6 options=201<PERFORMNUD,DAD>
gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280
stf0: flags=0<> mtu 1280
anpi0: ...
```

위 내용은 나의 mac에서 ifconfig를 쳤을 때 나오는 정보이다. "..."으로 줄였지만 저것 말고도 NI(Network Interface)가 정말 많다. 유독 mac에서는 더 그렇다. 왜 그럴까?

