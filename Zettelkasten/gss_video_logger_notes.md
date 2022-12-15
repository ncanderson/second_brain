202212121100
Status: #idea
Tags: #work #programming #video-streaming 

# gss_video_logger_notes

## Dev Notes
Run this:
```bash
gss_video_logger --video_source='rtsp://service:Bosch123!@172.31.0.8:554?h26x=0&line=1&channel=2' --log_suffix=main_video --logging_dir=/home/nanderson/projects/opt/logs/gss_video_logger_dev
 --publish_channel=OPT_MDAS_LOGGER_VISUAL_STAT --subscribe_channel=OPT_MDAS_LOGGER_CMD --logging_level=Info
```
Another set of args:
```bash
--video_source=/dev/video0 --log_suffix=main_video --logging_dir=/home/nanderson/projects/opt/logs/gss_video_logger_dev
 --publish_channel=OPT_MDAS_LOGGER_VISUAL_STAT --subscribe_channel=OPT_MDAS_LOGGER_CMD --logging_level=Info
```

Start a recording:
```bash
qlcm_type_ui logger_cmd_t 3 VIDEO_LOGGER_CMD
```
Sending `1` will start logging, sending `2` will end it.

## Benchmarking
### Pre-updates
Something we're working with is determining who is grabbing the streams and when. When benchmarking the loggers, close workspace, run `lcm-spy` to watch `gss_system_monitor`. This should mean that the loggers have the streams and we are measuring system usage appropriately.

- Start three instances of `gss_video_logger`. 
- Begin an LCM log, ensure it will capture `gss_system_monitor`
- Log for several minutes
- Begin logging
	- Stagger each logger about 2 minutes
	- Turn all three on
	- Let all three run together for several minutes
- Start logging with all three

#### Logging Notes
- Loggers off towards the beginning
- Turned on around `204`
- Workspace closed around `240`
- Re-opened around `276`, loggers turned off. 
- Between about `240` and `270`, and then after `300`, workspace is closed. The difference appears to be about 2% at most for the instances of `gss_video_logger`.
- Workspace re-opened around `450`, all loggers re-started. 
- After about `480`, workspace is closed and all loggers are on. ![[Pasted image 20221214141738.png]] 

##### Disable Extra Stream
Look at the impact of this compared to removing the duplicate stream from OPENFLS.
- Around `100`, turned off workspace after removing UDP URL from OPENFLS
- Around `200`, re-opened workspace. 
- Around `360`, closed workspace
- Around `420`, opened workspace, turned off loggers, closed workspace
![[Pasted image 20221214143451.png]]
- Why the hell is workspace now redlining the CPU?
- Actually this test is likely to be bogus since the loggers were not actually grabbing the video streams

##### HTOP Review (main logger only)
- When workspace is not running, the main video logger is using consistently mid-to-high 40% CPU
- With workspace running, main video logger is using high-50 to low-60%
- Commanding logging spikes usage to 95-120% range


### Post-updates
Make code changes, then repeat this procedure

### Notes from Chris
- High CPU usage happens when the stream is `Open()`ed

While paused, occasionally start the stream, check a few frames to confirm we're good, and then continue. This is to ensure the stream looks good to minimize the re-start time. This is a crucial thing to fix, because the stream can change and then the logger just won't work until restarted. 

Make sure that the logger can recover from the frame size changing

If we move `Open()` to after we command to start recording, it will take 2-30 seconds to start recording after pressing the button.

### Possibilities
- `Open()`
- `Start()` on command
	- If something changes, we close then re-open
	- Possibly put a periodic check in to verify the stream is still streaming as expected which would minimize re-opening

Need to put something in to confirm that the stream is good during runtime
`Open()` early, leave open, check the stream periodically

### ToDO
- Bugfix - check the stream and re-open if it's bad
- Only want to start when we are commanded to
	- Likely going to lose up to 1/3 second worth of frames when doing this
- check the stream when opened, but either streaming or not streaming
- When done, `Stop()` but don't `Close()`
- Has to be configurable via arg, so one could just leave the streams running
- Create a pause state
- Create a running state
	- On entry - set last rx timer
	- Write timeout when we get a frame
	- Check timeout in run
- Critical - while recording, check that we're receiving frames. If we're not, need to reopen the stream
	- Check `workspace`, `gss_video_player.cpp`, `run_video_rx()` there is an example in the read state.
		- Do with some sort of timer, reset it in the frame callback
		- In callback, set member attr when we get a frame


# References
Chris Burnham
