202212121100
Status: #idea
Tags: #work #programming #video-streaming 

# gss_video_logger_notes
See also [[gss_video_logger_update_plan]]
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

## Plan of Action
[[gss_video_logger_update_plan]]

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
##### Questions to Answer:
- [x] What is the CPU impact for workspace when changing what stream it grabs?
	- Don't care, since we're not delivering workspace. We'll give F5 whatever they want.
- [ ] What is the CPU impact from the loggers for the different stream encoder profiles? Testing with the visual logger only
	- MJPEG: 5-10%
	- H.264: Not an option since we want to stream H.265
	- H.265: 15-25 CPU% Or maybe mid to high 40's?
- [x] We can connect to H.265 using `h26x=5`. Is that option only available when the web gui has H.265 checked? 
	- Correct


##### HTOP Review (main logger only)
- When workspace is not running, the main video logger is using consistently mid-to-high 40% CPU
	- But sometimes it's around 15-25%
	- Sometimes it's pushing 80
	- So I don't really know.
- With workspace running, main video logger is using high-50 to low-60%
- Commanding logging spikes usage by 20-40% plus
- But also I think it's making a new thread, so maybe the net increase is whatever that thread shows.

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

Application argument to control the decoding behavior - `--decode_on_startup`  determines if we `Start()` on app startup

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
