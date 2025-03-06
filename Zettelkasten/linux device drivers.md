202404090943
Status: #idea
Tags: #ubuntu #linux #device_drivers

# linux device drivers
May need to find, download and possibly build linux device drivers. This is the method I used to ensure the proper driver was installed for an i7 [[mio]], which, after a reflash, was not showing the two necessary [[NIC|NICs]].
1. Run `lshw -c network`. The output:
```bash
gss@gss-i7-mio:~$ lshw -c network
WARNING: you should run this program as super-user.
  *-network                 
       description: Ethernet interface
       product: I210 Gigabit Network Connection
       vendor: Intel Corporation
       physical id: 0
       bus info: pci@0000:02:00.0
       logical name: enp2s0
       version: 03
       serial: c4:00:ad:c3:8c:55
       size: 1Gbit/s
       capacity: 1Gbit/s
       width: 32 bits
       clock: 33MHz
       capabilities: bus_master cap_list rom ethernet physical tp 10bt 10bt-fd 100bt 100bt-fd 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=igb driverversion=5.6.0-k duplex=full firmware=3.25, 0x800005d0 ip=172.31.0.9 latency=0 link=yes multicast=yes port=twisted pair speed=1Gbit/s
       resources: irq:17 memory:80480000-804fffff ioport:3000(size=32) memory:80500000-80503fff memory:80400000-8047ffff
  *-network UNCLAIMED
       description: Ethernet controller
       product: Intel Corporation
       vendor: Intel Corporation
       physical id: 1f.6
       bus info: pci@0000:00:1f.6
       version: 20
       width: 32 bits
       clock: 33MHz
       capabilities: cap_list
       configuration: latency=0
       resources: memory:80700000-8071ffff
WARNING: output may be incomplete or inaccurate, you should run this program as super-user.
```
2. Use `uname -r` to see the kernel version for the system
3. Output the boot log: `sudo journalctl --boot > log.txt`. Sample output: ![[log.txt]]
4. From Chris Esposito:
> I see the interface eth0 being detected and using the igb driver, then it gets renamed as enp2s0.  
> The only other one I see it talking about is the AXIS USB Ethernet adapter. The only mention of any kind of the other onboard NIC I see is just the bus ID of it, but no usual info in those messages: 
```bash
Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: [8086:15fb] type 00 class 0x020000  
Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: reg 0x10: [mem 0x80700000-0x8071ffff]  
Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: PME# supported from D0 D3hot D3cold
```
> `8086:15fb` gives us [more info about that card](https://linux-hardware.org/?id=pci:8086-15fb-1028-0a21). "The device is supported by kernel versions [5.5 and newer](https://www.kernel.org/)"
5. [Find the driver you want](https://www.intel.com/content/www/us/en/download/14611/15817/intel-network-adapter-driver-for-pcie-intel-gigabit-ethernet-network-connections-under-linux-final-release.html)
6. if you download that and extract it with `tar xvzf filename` then `cd src`  I think you can just do `sudo make install`. You may need to do `sudo apt install build-essential` first to install the headers for the kernel you're on.

### More Thoughts
> I've done similar before for wireless drivers. Once you have the card ID we got from the syslog, you can look that card up and see what drivers it is supposed to use. At that point I saw that it was supported in kernels 5.5 and newer, but prior to that they released drivers that could be installed manually, so I grabbed the latest one of those that existed. The release notes specify the card we happen to be using. From you `lshw` on the "unclaimed" NIC it showed:  
```bash
physical id: 1f.6
bus info: pci@0000:00:1f.6
```
> So I searched for that in the syslog, which didn't give anything obvious, but turned up:
```bash
Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: [8086:15fb] type 00 class 0x020000 Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: reg 0x10: [mem 0x80700000-0x8071ffff] Apr 05 15:04:28 gss-i7-mio kernel: pci 0000:00:1f.6: PME# supported from D0 D3hot D3cold
```
> And I recognized `8086:15fb` as a hardware ID for the card that would tell us what vendor and model it was. If it breaks after a kernel update on that PC you should just be able to run the same `sudo make install`


# References
Slack conversation with Chris Esposito