202311150948
Status: #idea
Tags: #work #meetings 

# 2023-11-15 Vermont visit

## Attending
#### OPT
- David G
- James C
- Sean
#### GSIQ
- Greg
- Paige
- Jeff
- Nate
## Notes
- Tracks are very important
- Let's get rid of one of the SBC's
- Layering all contacts
	- AIS
	- ARPA
	- Non-ARPA targets that we see 
	- Turn layers on and off
- We're focusing on DHS because it's in front of us, but this is not a DHS product. Need to make sure any/all users get the functionality they need.
- An API is critical. Some clients will not want the UI, but just the raw data
- FConnect 
	- wirewall comms manager between the cloud and the buoy. Wraps the connection in a Palo Alto firewall. MQ message broker.
	- Multiplex connections if there are multiple connections.
- Desire for automated and configurable alerts
- 'Pattern of Life' - patterns observed by ships. Could be normal or unusual and worth investigating. 
- Look up targets in a database, to determine if something is repeating.
- Zones of interest have to support arbitrary shapes.
- The concept of ARPA isn't thats important, just that there is a target there
- NATS.io appears to just be JSON
- Simrad - another radar with SDK support

## Action Items
- Better ARPA tuning, especially for small, fast-moving targets
- Reconfigure our workspace layout to more closely align with timezero
- Remind David G. about the interconnect agreement with DHS
- Goal is the have the full GSIQ solution deployed on Feb 1.
-  Start to look at the OPENFLS feature tracking
- Consider an application (probably PCS actually) that will determine 'All Clear' and perform actions, like:
	- Shutdown camera
	- Sync db to cloud
	- Erase logs that have been backed up
- What's the min amount of data to save that will be useful
	- Save the radar tracks, no the entire radar stream
- Move to 22.04?
- Explore on/off control of the camera
- Explore feature tracking. Create a new application that incorporates AIS, ARPA and feature detection.
	- What support for multiple layers do we have in workspace? 
- What can we do now with AWS?
- ECTIS - military standards for display, colors, etc

## MVP
- Move some of our stuff onto the Seco, maybe leave the radar alone on the MIO
- Make the camera streams configurable, possibly turn them off
- MDAS today is a 1:1 relationship
- A target track is fundamentally important. 
- Modify `gss_arpa_overlay` to draw additional targets like AIS and `landmark_t`s