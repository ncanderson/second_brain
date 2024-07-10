202312061146
Status: #idea
Tags: #programming #work 

# target correlation
Posted to Slack:

Looking for some guidance as I embark on a new bit of functionality for OPT. I'm trying to do a combination of target correlation and nav data fusion.I have the following data points. Each of these data source has a lat/lon position:  

- ARPA contact
- AIS contact
- OPENFLS landmark (probably, but it's not implemented yet for our project)

 Let's consider a trivial example, where we only have a single contact for each type. I'd like to take the lat/lon position from each data source, and create a composite target/contact that represents the best estimate for the target's location. This new composite target would then be published (and we'd display it in workspace). The principle reason we're pursuing this is because OPT wants to provide alerts to a user when something that doesn't have any AIS data enters a specified zone of interest. We therefore need to use the available sensor data (ARPA and landmarks), compare that to the available AIS data and determine if there is a thing in the water for which we don't have an AIS contact. Consequently, the target correlation using available data is the more important (and trickier) portion of the task.In reality however, it won't be this simple.  

- There are likely to be multiple ships and/or obstacles within the radar's range (which could be significant).
- We'll have to correlate the available data (probably via range/bearing from the buoy) so a composite target is only generated from the available data for a single real-world object. This obviously isn't going to be perfect.
- We won't always have all of the data sources that correspond to a real-world object.
- Specifically, the OPT MDAS system is being sold as a way to locate and identify 'dark' targets, which are sailing without AIS.
- There are radar tuning parameters to account for sea and rain clutter, but we do still see radar returns in rough seas which we don't want to track.
- My understanding is that the OPENFLS feature tracking can be configured to not generate a target for ephemeral things like waves, but it is a consideration.
- Landforms will have radar returns but shouldn't be treated as targets.

Reviewing OPENINS, it looks like I could use the subscribe channels to pass in pose_stats and get a state estimation, but it doesn't appear that OPENINS can output data for multiple targets like I need.Roughly, this is what I'm thinking about:  

- Subscribe to all available data needed to generate a composite target, and retain it in the application doing this work (probably PCS).
- Correlate that data based on range and bearing from the buoy. I'm envisioning using notional range circles around the buoy and the bearing to the targets to get a best estimate of which things are the same things.
- Two targets that are both 500m away but with a large difference in bearing are unlikely to be the same thing.
- Two targets with similar bearing but a large range difference are unlikely to be the same thing.
- If the positions of two separate data types are within a specified distance, assume they are the same thing.
- Pass the available data points for targets we've determined to be the same thing into something (a Kalman filter?) to arrive at a composite target position.
- Publish the composite target data.

I'm hoping to solicit some feedback on:  

- The overall approach
- Tools we already have available that could support this work
- Other implementations we have for similar work that I can reference as I proceed

Thanks. More details available on request. (edited)


# References

