202210311400
Status: #idea
Tags: #linux #programming 

# networking
`ip route` to find the default gateway. In this case - `default via...`

```bash
gss@gss:~/gss_logs/system_logging$ ip route
default via 192.168.3.1 dev enp2s0 proto static metric 20100 
169.254.0.0/16 dev enp2s0 scope link metric 1000 
172.31.0.0/16 dev enp2s0 proto kernel scope link src 172.31.0.9 metric 100 
192.168.3.0/24 dev enp2s0 proto kernel scope link src 192.168.3.130 metric 100 
224.0.0.0/4 dev enp2s0 proto static scope link metric 100 
```


# References

