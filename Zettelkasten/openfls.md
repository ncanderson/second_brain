202211211642
Status: #idea
Tags: #gss #programming #openfls

# openfls
- To run OPENFLS without connecting to a device (at least for the radar branch):
	- Comment out these lines in `furuno_navnet_radar.cpp`: 
		- `gss::NavNet_SDK::FLM_certification(addr_bytes);`
		- `gss::NavNet_SDK::Initialize_navnet_common();`
	- 
- To have openfls output to a window, add `--local_display` to the argument list
- Log replay example - note `--sonar_type`:
```shell
openfls --sonar_type log_replayer --shm_url arpa_overlay_shm --input_file /home/nanderson/projects/opt/logs/20220719_lake_testing/raw_openfls/20220719_raw_openfls_1658254286
```
> In this example, I was streaming to shared mem; that's not required always

## [[Plugins|Plugins]]
- Create libopensea_sonar with just `sonar.h`
- Make libsonar_navnet a [[Plugins]] library
- Add [[Plugins]] infrastructure to openfls
- Create an empty, dummy sonar that has nothing in it so we can test the plugin framework
- Probably need the `autonomy-rc` branch of openfls

## Architecture
`Set_supported()` 
`m_sonar` is a pointer to the sonar base class. `sonar.h` is the base class that has virtual function.
- Create `libopensea_sonar`: image building stuff and sonar.h base class
- Create `libsonar_navnet`: navnet-specific stuff
Can reference `libbehavior_trees` for how a plugin framework is utilized.
Do we have to support windows?

## Plugins
- How to incorporate the navnet SDK
- There is/are scripts in drive that show Chris' work to create a deb of the Furuno SDK.
- Create a new repo like `gss_navnet_radar` to hold the code that's currently in `openfls`
- Scanning image builder and multibuilder need to be available to all radar/sonars. Likely have to move to a new repository.
- `image_building` folder has to end up in a shared location.

## Echo Averaging/Trail/Stretch
Implement `RmcSetFreeCommand()`. Takes:

| Data | Arg |
|------|-----|
| INT32 | _radarNo_ |
| UINT8 | _id_ |
| UINT8 | _text_ |

- Add function call for `RmcSetFreeCommand()`
- Copy the stuff for radar num from the manual arpa bits
- Create an LCM interface. Signals needed (all analogs) are:
	- TILERANGE
	- TILEES
	- TILEEAV
	- TILEDIST

| id |	Text | description | Notes |
|---|---|---|---|
| TILERANGE | See `Range Codes` table | The Range of a tile echo and an echo trail is changed. | ext format is `Range;Unit`, eg `"09;0"`  |
| TILEES   | 0(=off) / 1 / 2 / 3 | The echo stretch of a tile echo is changed.   | |
| TILEEAV  | 0(=off) / 1 / 2 / 3 | The echo average of a tile echo is changed.   | |
| TILEDIST | 0-8 	             | The low level echo of a tile echo is changed. | |

#### Range Codes
This is the same table as the range, so we can make use of the existing stuff
| Code | Range  |
|------|--------|
| 00   | 0.125  |
| 01   | 0.25   |
| 02   | 0.5    |
| 03   | 0.75   |
| 04   | 1      |
| 05   | 1.5    |
| 06   | 2      |
| 07   | 3      |
| 08   | 4      |
| 09   | 6      |
| 10   | 8      |
| 11   | 12     |
| 12   | 16     |
| 13   | 24     |
| 14   | 32     |
| 15   | 48     |
| 16   | 72     |
| 17   | 96     |
| 18   | 120    |
| 19   | 36     |
| 20   | 64     |
| 21   | 0.0625 |
| 22   | 0.8    |
| 23   | 1.2    |
| 24   | 1.6    |

#### Examples
```c++
RmcSetFreeCommand(0, "TILERANGE", "09;0"); 
RmcSetFreeCommand(0, "TILEES", "1");
RmcSetFreeCommand(0, "TILEEAV", "0");
RmcSetFreeCommand(0, "TILEDIST", "8");
```

#### Command Changes
LCM. Analog, name/value.
device.Set_echo_visualization()
gss::NavNet_SDK::Set_echo_visualization()


## [[ARPA]] Target Flushing
`C_CLEAR_TARGET`: Clear by ID
- `Cancel_arpa_target(int ID)`
- Calls `RmcSetArpaCancelTargetId` with the argued ID

`C_CLEAR_LOST_TRGTS`: Clear lost
- `Cancel_lost_targets()`
- `RmcSetLostAck`

`C_CLEAR_ALL_TRGTS`: Clear all
- `Clear_all_arpa_targets()`
- `Cancel_lost_targets()`
- `RmcSetLostAck`

C_CLEAR_ALL_TRGTS will call `RmcSetLostAck`, and also flush the arpa_contact_list that openfls publishes. I'm not sure if anything additional is required, or even if the furuno SDK is sending a valid command to the device.

Radar should track ID internally.

How does the radar define 'stale'?

The radar will re-use target ID's eventually.

## Process Echo Data
We get a vector of scanline data, from 0 - 255. We get a block of data, with intensity values of 0 - 255.
Speical values in intensities?

# References

Slack comment from Shay in the openfls Slack channel
