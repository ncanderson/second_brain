202405061108
Status: #idea
Tags: #work 

# everclean
[[SLAM]], and using data to help plan missions based on previous missions

## Current OPs
- Telemetry log gets upload, Inspire runs a matlab script that generates a coverage pattern
- Want a database of where the vehicle has been 
- Inspire doesn't want to have the GSS matlab scripts, they just want to get the scrubbed data
- LIve writes from the vehicle -> topside -> database
	- Downsampling somehow in a PCS, hoping for 5+ hz
- Brush feedback is a good indicator of the current hull condition
- There is nothing in workspace that's saying anything what vessel or what cleaning
- We need to ensure that our 0,0 reference point makes sense, and it tied into the data that's received
	- "How do we ensure the thing attaches at the same place every time, and how do we enforce that through software"

## Customers
- Want to know every inch of their ship's hull, which is why the brush tables exist
- Clients (like Carnival etc) won't have access to the data store, anything they want will trickle through Inspire

## GSS People
- Paige PM for the whole project
	- Zack will stand in during maternity leave
- Heath - Dev manager
- Brian - De facto leader of the 'above the water stuff'
- James - Chief ME (hardware/design)
- Ian - UI
- Greg - Networking/OPs
- Scott - Lead engineer on the control systems side
- Cody, Harrison - Operations architects
- Naomi - SLAM
- Adam (Inspire)
- Chris (Inspire)

## Long-term Goals
- Using previously gathered data to help plan future missions
- Save/load missions to the database

# References
Conversation with Brian
See also [[everclean initialization]]
