202310171044
Status: #idea
Tags: #work #opt #serial 

# 2023-10-17 onsite visit
- Seavision. Has a built in correlator for seeing a target from multiple sources (TTM, AIS, etc). Shows also a dark target, where a tracked target doesn't have AIS.
- Class A (?). Class B AIS. Class A, 12 watts
- Why do this? ROA and MDA (marine domain awareness)
	- relay target position info in near realtime to HQ
	- eg Senegal goes out to patrol, looking for illegal fishing vessels. Patrol may not want to broadcast their position and not broadcast AIS
	- Extends range of radar
	- Doesn't require real estate for an emplaced radar, or network infrastructure
	- Mobile radars, using AIS as the transmission
	- Using AIS because it's free, and commercial cell networks can be spotty. Sattelites are expensive. Also uses an existing infrastructure. AIS just as a modem.
- The digi just (maybe more than that, butfor purposes just) combines the TTM and GPRMC NMEA and writes it to the AtoN (which I believe we'e been calling the 'ais device').
# 2023-10-18 onsite visit
- Power usage of the AtoN depends on how often it's squaking.
- Need to balance the power usage when squaking with an adequate refresh rate.
### Collaboration
- BBM (binary broadcast message) has to come from digi
- Writing ttm and rmc to digi over serial
- digi combines rmc and ttm to produce a vdo bbm.
- The AtoN has 4 ports
- RJ45
- RS422
- DB9
- 


# References

