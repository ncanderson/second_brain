202302231231
Status: #idea
Tags: #work #programming #radar

# Furuno Radar
Our [[Furuno]] radar: DRS4D-NXT

FAR - commercial grade radars. Same SDK, different license.

Unusual propagation conditions - weather conditions can affect the return of the pulse. This could cause noticeable changes in the echo return. Important to be aware of the atmospheric conditions when considering radar output.

Range changes cause speed changes of the antenna.

6 NM is a good starting point for making setting adjustments.

When the firmware is updated, reset the radar to the factory defaults.

Use the installation manual to determine what the SDK should be able to do.


#### General Init Procedure
- A/C Sea all the way down
- Gain fully up

#### Auto Modes
Let the radar pick the settings. Assumes the best settings for a small boat, so my not be appropriate for our usage.

#### Frequencies
Bands of frequencies. IEEE and EU have specs. 
S-Band Radar: lower frequency. Better penetrates weather, good at very long ranges since large ships need more time.
X-Band Radar: higher frequency. Lower range, better picture. 8-12 GHz
[[Interference Rejection]] - tweak the frequency to eliminate picking up feedback from other radars 

#### Resolution
Ability to distinctly display two radar targets that are close to each other on the water.
##### Beam Width
Horizontal: .75° - 5°
Vertical: 20° - 25°

All that matters is what range do we want to see an object at? We can use camera lenses as a correlary. A zoom lens allows narrow focus and great range, while a fish-eye lens has wide field of view but little zoom.

There is a min range. Determined by scanner height, vertical beamwidth, blind sector w/in beam, and pulse length.

Radar mountain height very important to consider, as that determines min and max range.

#### Gain
Furuno radars shoot a little hotter, so it's generally better to start tuning at 0, and increment it up from there. Some other manufacturers will recommend starting at 100% and backing off from there.

##### Main Gain
Adjusts receiver's sensitivity. Long range, adjust so background noise is just visible on the display. Shorter range, people tend to increase gain.

##### A/C Rain (FTC)
Rain control, also called FTC (fast time constant) suppresses echos from rain, hail and snow. On X band radar, the short pulselength causes echoes to be lost from precipitation. This is called rain clutter. Adjust the control to break up the clutter. Too much can cause valid targets to be lost.

##### A/C Sea (STC)
Sea control, aka STC (sensitiivity time constant) suppresses reflectioins from waves. In rough waves the reflected echoes are very strong. If this is set too high, both sea clutter and echoes will disappear from the display. At max value, only extremely hard targets remain.

#### Range
Pulselength auto-changes when range is changed. Traditionally a radar display would have 9 range rings.

##### VRM
Variable Range Marker. An adustable marker. You can put in on the edge of something you're trying to measure.

##### EBL
Electronic bearing line. Measures bearing to targets in degrees. There is a bearing switch on the rotation gear that uses a magnet to indicate when the radar passes 0°.

#### Echo Visualization
##### Echo Stretch
Possibly just a post-processing feature.

#### Display Mode
**Relative**: Ship position is fixed, everything else is relative to the ship's position.

**True**: True north. Requires the magnetic variation (magvar) between true north and magnetic north. ARPA targets are displayed in true north. Can we use this to replace the ARPA overlay? Parker things no.

#### Antenna 
##### Rotation
24/36/48 rpm range coupled or 24 rpm fixed.
Higher rpm gives us better resolution without changing pulse width. Dual range mode requires a fixed RPM.
To use the automated RPM/Range relationship, the Antenna RPM setting has to be `auto`.
##### Height
Can be entered as a setting, will help with ARPA targeting.
##### Length
Automatic for dome radars, but commercial radars could have a replacable stick so this config is required.
##### Position
Offsets from bow and port. Offsets relative to the source of positional data.

#### Radar Monitoring
Display various info. Can get voltage from the device.

#### Targets and ARPA
Cranking gain up to 100% can flood radar with targets. Reducing gain again will retain those targets until they are lost. At high gain levels, there can be false positives from the main bang.

ARPA works by using the doppler shift. Turning Auto Doppler off will stop auto target acquisition.

Auto-acquire will pick up junk. Some of these features can be used to help eliminate junk targets.

There are 'Track Target Candidates', which the ARPA engine tracks until it can calculate a vector. This isn't accessible to us.

Sea returns affect ARPA. Sea and Rain clutter have to be properly configured or we'll get a bunch of false positives. If heavy clutter exists, switch to manual acquisition.

##### Echo Detection Level
Change the floor to determine the threshold of echoes that will be processed as ARPA targets. Defaults to 13. Decrease this value to improve weak echo target acquisition. If this setting is too low, we can rapidly fill up our ARPA target buffer.
##### Echo Detect Size
Change this setting to modify the size of target designated as ARPA
##### Auto Acquire Correlation Count
Number of sweeps required to designate a target as an ARPA target. I believe this is `catch correlation`.
##### Auto Acquire Erasure Count
Catch Cancel. Number of consecutive scans required to determine an echo is a target. If this is too low, a target passing through the shadow of a solid object could be lost and reclassified as a new ARPA target when it reappears. It's better to use other tools - this is a 'big eraser'. Try to tweak gain before messing with this. This does not apply to manual ARPA targets.
##### Gate Size
Change tracking observation area. Increasing this value will make it easier to track faster targets, but may cycle more quickly through ARPA targets, dropping them in favor of another target. Decrease if it switches auto acquired targets too quickly. Indicates the expectd distance a target has travelled between sweeps. Set too high and we'll lose slower targets. Too low and we'll lose faster targets.
##### Filter Responsiveness
`RmcSetArpaFilterRespons`. Increasing this value improves tracking of turning targets. Decrease for non-turning targets
##### Lost Count
Works for manual and auto acquire targets. For active targets, if an echo is lost, this config determines how many sweeps happen before the target is lost. Applies to manual and auto targets.
##### Max Speed
Defaults to 150kts, the highest value. Decrease will make the sensor less sensitive.
##### Tracking Modes
Method for calc. the est. positoin of ARPA. #1 uses the arbitrary settings for the specified values. 2-4 will tweak the subsequent values based on echos.
1. Follow gate size and filter responsiveness. Uses arbitrary settings.
2. Modify both parameters based on the previous echo.
3. Modify the filter responsiveness only based on previous echo.
4. Modify gate size only based on the previous echo.
##### Vector start time/scan
Set the time of the number of scans to start displaying vectors. Something bout how the ARPA engine establishes vector lines to determine where a target will be.
##### Interference Rejection
Stuff can interfere with auto acq. Turn on Interference Rejection if ARPA is borked.

##### Guard Zone

##### Target Analyzer
Should be a function of the radar, still need to determine how the echo data might be different and how we might be able to access the changed data.

##### Echo Stretch, Averaging
Recommends turning off averaging.

##### Video Contrast
Appears to be something that we could accomplish with other radar turning commands. We need to ask OPT specifically what they are looking for; this feature is something specific to TimeZero, so if there is a demonstrable lack that this feature would solve, we can look into it further, otherwise, we should take this off the table.


## Terms
- Attenuation - Relationship between antenna size and resolution of image. Larger antennas (ie greater attenuation) allow for smaller 'bins'.
- Main bang - the optradar picking up its own transmission. Can result in a blob right next to the radar.
- Bearing Resolution - Measure of the capability to display separate targets at the same range and close together. Poor bearing resolution will show 2 distinct targets as a single blob.
- Sector blank - The radar will not transmit at this sector. Defined by angles. There is NO transmission for that sector.
- Interference rejection - reduces or eliminates interference from another ship's radar.
- TX-[number] - Channels to ensure that 2 furuno radars are not interfering with each other.
- Racon - A radio transponder that will reflect a radar signal back
- Dual range mode - When active, the radar can transmit two different ranges in the same sweep.
- CPA - closest point of approach
- TCPA - time to closest point of approach
- Antenna heading align - compensate for any mounting offsets
- MBS - main bang suppression. `RmcSetMBSAdjust`

## Questions
- Questions about the license, and running openfls from other locations or on our local machines.
- What's your preferred method for tuning in the radar.
- We have implemented the rpm changes, but haven't tied that to range. Does the device handle that range-based coupling, or would we have to implement that?

## Actions
- Translate radio waves to audible sound?
- Implement function to get radar status, so we can publish power stats from the radar.
- Add tooltips to ARPA params
- ARPA size == ARPA landsize? says it shouldn't be used normally
- Draw vectors for ARPA targets
- Check the values on the ARPA struct that could indicate that a target doesn't currently have an echo.
- Change name of 'Auto Doppler' toggle to 'Auto ARPA Acquisition'.
- Remove auto doppler and interference rejection from back page radar controls`

# References
On-site radar training with Josh from Furuno