202309011426
Status: #idea
Tags: #work #programming 

# pikvm
Find pikvm on network: https://10.100.130.39/login/


KVM - multiplex a system. Used the plug one mouse/keyboard to control three computers. Emulates being a hub or docking station. Takes the output and shoves it to the web.

## Prep
- Back up the key
- Back up the network configs


Under 'Drive' (top right) select an iso. Choose 'Flash'. Click 'Connect to Server', should be there as if it were a USB stick. May need to reboot before it will show up.

Should be able to just upload it, and boot the MIO and it will 'just work'. 

Currently the pikvm is connected to the SECO and not the MIO. 

Need an ISO file, which can be downloaded from 'the usual place'. Use 'Flash', not CD-ROM. 'Connect Drive to Server'. Start up, then restart. mash `esc` to get into BIOS, go to boot, 'Boot Override'. Find the drive to boot off the ISO image.
![[Pasted image 20230905100036.png]]
Erase the old drive and reinstall. Last chance to do anything.

Let OPT know that our install won't replace existing utilities.

## Getting UBS onto the MIO
May need to scp the UBS .run over.
- `ip addr` to get local IP address
- Start [[SSH]] Server on the local machine. Run `apt install openssh-server`
- `scp username@ip-address:[remote file path] [local file path]`
Could also just install teamviewer and transfer the files that way.

## Transfer to [[Seco]]
`scp -r /home/gss/gss_updates/postgres_database/dvdrental fconnect@10.1.10.4:/home/fconnect/gss_updates` 
PW: yees*REEG0zoot9urs

## Software, Getting Ready
- Install the key

# References

