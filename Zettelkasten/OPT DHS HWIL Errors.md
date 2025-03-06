202305151530
Status: #idea
Tags: #opt #work 

# OPT DHS HWIL Errors
### Teams, afternoon of 5/11
**Gabe**: Zhang, Charlie so poking around on the Mio a bit I can't find those errors you screenshot in any of the logs. I do see some dmesg warnings about crt controllers but I doubt that relates. Based on the `blk_update_request` errors in your output and the failure to log rotate it /smells/ like disk corruption issues or maybe a loose connection. I ran a short `smartctl` test which didnt turn anything up but maybe a longer one would altho those can take hours to run. if someone could boot another os on the Mio it might be worth running fsck. Could be good to also check the hdd connections are solid and that the drive is seated properly. THose are not things I can do over teamviwer unfortunately. Loose connections are kind of a shot in the dark tho since it is clearly booted now but I'm not seeing any specific issues jump out to me at least that align with what is in your screenshots
**Charlie**: Thanks! I also checked the kernel log, and found the following warning in the 1st failed reboot yesterday. Do you think it's related?
![[Pasted image 20230515153611.png]]
![[Pasted image 20230515153624.png]]
**Gabe**: I'm not sure thats related. I dont know what caused the change to the `ENERGY_PERF_BIAS` tho. i dont think it would be the issue. As far as EISA is the Mio hardware making use of an EISA slot? if so its worth checking but i doubt it is and in which case its probably not the culprit. altho that is a technology i'm not super familiar with. The other thing i'd do if i had the physical device in front of me is like boot it a bunch of times in a row and see if the error continues to occur but i'm hesitant to boot it over team viewer in case it doesnt come back up again. I think its just a the kernel probing for devices there and not finding any which is likely correct. The other thing i'd do if i had the physical device in front of me is like boot it a bunch of times in a row and see if the error continues to occur but i'm hesitant to boot it over team viewer in case it doesnt come back up again

### Teams, morning of 5/11
Good morning! Blane Counsil (Guest) Burggren, Andrius I found Seco in DHS-hat-02 was OFF when I came this morning. Seco SBC didn't respond to PiKVM keyboard, so I had to power cycle it. SECO booted up normally. I have found SECO last boot was at 4:45PM (CDT), looks like Seco didn't boot up successfully. Did F5 try to reboot SECO at that time? In addition, around 4:40PM 5/9 (Tuesday) , SECO SBC also failed to boot up after a power cycle. Could you please investigate and let us know the reason why SECO was failed to boot up? Thanks! Mio has been working well since yesterday mornin

### Email on 5/11: DHS_hat#02 Mio SBC crash and boot failure problem

External
Mio SBC in DHS_hat#02 has been showing several crashes and boot failures since this Tuesday 5/9.   Mio kernel crashed at 5/9 12:35PM, reason is not clear so far.

After 5/10 (Wednesday) boot up,  Mio worked for about 24 hours without obvious issues until this morning.

![[Pasted image 20230515153110.png]]

At ~8:03AM.       I shut down Mio in order to power cycle the failed SECO sbc.   
Both Mio and Seco booted up normally.  But later I found both F5 UI and GSS WS didn’t show Radar stream (Radar power was ON, and showed Radar IP in arp-scan).  The camera and compass displays are normal.

~ 8:30AM.           In Mio ubuntu GUI, I tried to restart ubuntu.   Mio booted up into Ubuntu, but this time, no internet connection.  Both the 2 interface (eno1 and enp2s0) are down. 

~9:00 AM.           I used sudo shutdown -r to reboot Mio.   This time, Mio failed to boot up into GUI.   Please see the screen shot below.

![[Pasted image 20230515153134.png]]
9:14 AM.             I powered down both Mio and Seco for another power cycle, since Mio didn’t respond to PiKVM keyboard.  

9:25 AM.             Powered up both MIO and SECO  (2nd time this morning),  Mio failed to boot up, similar to the above screen shot.

9:31 AM.             I powered down Mio/Seco and all the MDAS-hat#02 components.  (total system shutdown).

9:37AM                Powered up the MDAS-hat#02 and the Mio/Seco.  This is the 3rd time this morning.   Mio still failed to boot up after showing the GRUB.  Similar to the above screen shot. Could you please help in investigating this Mio failure problem?  Can we go through another Mio power up trial together and maybe we can use GRUB/ubuntu tools to do some repair?

### Teams 5/15
Still trying to resolve boot issues.
![[Pasted image 20230515154133.png]]
![[Pasted image 20230515154116.png]]
Nate Anderson (Guest) Blane Counsil (Guest) Looks like Mio-DHS-hat#02 is now crashed and not responding to the PiKVM keyboard. Can I do a hard shutdown and power cycle the Mio now? I also need to power cycle the SECO SBC (DHS-hat#02) since the 2 SBCs are tied to the same power channel.
![[Pasted image 20230515154213.png]]
Mio was connected through SECO from last Thursday(5/11) until it crashed during your investigation earlier today. DHS-hat#02 (Same Mio+Seco) worked well from 5/5 3PM to 5/9 12:35PM when Mio was crashed for unknown reason. I sent out an email about that crash last week. Nate Anderson (Guest)

Maybe before it was put into DHS-hat#02. Once it's in the hat, it is running together with Seco and PiKVM. I don't soft shutdown SECO and power off PiKVM to leave only Mio running if needed.

From 5/5 3PM to 5/9 12:35PM, the whole system worked without any noticeable issues for almost 4 days. On 5/9 12:35PM, the Mio was crashed.

From 5/10 7:41AM to 5/11 8:00AM, Mio-DHS-hat#02 worked without noticeable issues, but SECO SBC was crashed (or failed to reboot) at 5:45PM 5/10. From 5/10 5:45PM to 5/11 8:00AM, Mio GSS workspace seemed to work independently without the SECO VPN/firewall connection.

Observed the same thing happened on OPT-HWIL. 1) When seco is OFF, Mio GSS workspace can't start up showing "Fatal: network interface can't be started"; 2) When both SECO and Mio GSS Workspace are in working condition, the Mio GSS workspace will keep working even when the SECO SBC is turned off. Attached please find the journal file (the SECO is OFF).

![[mio_opt_hwil_journalctl.txt]]
Observed the same thing happened on OPT-HWIL Mio SBC exactly as in DHS-hat-02: When seco is OFF, Mio GSS workspace can't start up, showing "Fatal: network interface can't be started".

### Startup
![[Pasted image 20230515163238.png]]
![[Pasted image 20230515163347.png]]
![[Pasted image 20230515163451.png]]


# References