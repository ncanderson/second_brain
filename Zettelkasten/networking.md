202210311400
Status: #idea
Tags: #linux #programming 

# networking

## Scan for Device
```bash
arp-scan -I wlan0 --srcaddr=e4:5f:01:c6:d3:c7 10.100.130.0/24
```

## View Routes
`ip route` to find the default gateway. In this case - `default via...`

```bash
gss@gss:~/gss_logs/system_logging$ ip route
default via 192.168.3.1 dev enp2s0 proto static metric 20100 
169.254.0.0/16 dev enp2s0 scope link metric 1000 
172.31.0.0/16 dev enp2s0 proto kernel scope link src 172.31.0.9 metric 100 
192.168.3.0/24 dev enp2s0 proto kernel scope link src 192.168.3.130 metric 100 
224.0.0.0/4 dev enp2s0 proto static scope link metric 100 
```
  
Chris Esposito![:spiral_calendar_pad:](https://slack-imgs.com/?c=1&o1=gu&url=https%3A%2F%2Fa.slack-edge.com%2Fproduction-standard-emoji-assets%2F14.0%2Fapple-large%2F1f5d3-fe0f.png)  [2:38 PM](https://greenseasystems.slack.com/archives/D022UTR1K1R/p1685039911046769)  

Try adding the multicast route on each with  
```bash
ip link set dev eth0 multicast on
ip route add 224.0.0.0/4 dev eth0
```


# References

