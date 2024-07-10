202302061611
Status: #idea
Tags: #work #opt #ubuntu 

# diamond switch

- Power up
- Connect serial to USB connector
- username `admin`, no password
- Enter `configure terminal`, prompt will change to `(config)#`
- VLANs are virtual networks. The switch allows configuration of the networks those are on. Basically just follow the wires and see what's connected to what. Devices should be on VLAN 200, the internet comes in on VLAN 101.
### Finding the Diamond Switch
When mucking around with these settings, it could make the IP of the Diamond switch change. Run `sudo arp-scan -localnet` to find the switches IP.

# References![[diamond_switch_install.png]]
### Working Config:
Note that if you don't save the config to startup, it will revert when restarted. To change this, click Maintenance -> Configuration -> Save startup config:
![[Pasted image 20240411121151.png]]
This is what the config should look like for the GSS HWIL:
![[Pasted image 20240410160602.png]]
![[Epsilon-Diamond-Switch-24000 User Manual.pdf]]

https://www.diamondsystems.com/files/binaries/Epsilon8100%20UM%20rev%20A.4.pdf