202212201113
Status: #idea
Tags: #work #programming 

# gss_video_logger_update_plan
See also [[gss_video_logger_notes]]
### TODO
- [ ] Don't run `Start()` when the application starts, only have it run on LCM command
	- [ ] Check to see if the stream has been connected prior to running
- [ ] Change names of the Start_input and Stop_input (or maybe remove them if we're not doing anything with them?)
- [ ] Add a periodic check for the stream when we're not logging
	- [ ] Make if configurable by application argument
	- [x] Create get/set functions
- [ ] Make any new running states as appropriate
	- [ ] Pause - for when opened but not started?
		- [ ] Maybe call it 'opened' instead of Pause?
	- [ ] Running - for when opened and started?
- [ ] When stopping logging, only `Stop()`, don't close
- [ ] Change the name of the two `Start_logging()` functions.
	- Video Manager version creates an `m_logger`, which is required to write frames.
	- Nothing will record unless an `m_logger` has been created.
	- We won't move from first_frame into read until a frame has been received
- [ ] Make sure the `Start_recording()` function in the video manager respects the desired states.

### Do it in the state machine:
- Open
	- Call open
- Pause
	- Wait here until we are commanded to record
- Start decoding
	- Call Start on the stream
	- Instanitate a logger and add to `m_loggers`
	- This should replace the `Start_recording()` function
		- The 'start recording' function here should be replaced by something in the state machine
- First Frame
	- If we have a frame, start the read
- Read
	- Just read the stream, this should be the logging/recording step
	- If we get a stop command, call Stop and jump to Pause
	- Use a flag to track the LCM command

### Notes
Create an attr on `m_video_mngr` to manage when/how we run `Start()`.
##### States
- Open: start phase. open the stream
- First Frame: stream is opened, try to get first frame
- Read: nothing special
- Pause: nothing special

Should go:
- Open
- Pause 
	- Call Open
	- If we're to start logging, call start and move to First Frame
	- Make sure the `m_first_frame` variable is handled correctly
- First Frame
	- Only fire if we got a frame
- Read
	- Ensure we close the stream when we stop recording

For recording, we have to be in read or pause state
When logging stops, should drop from Read down to Pause and Stop() (but not close)

Interestingly, there are two `Start_recording()` functions, one for the manager, one for the logger. The manager version will call the logger version if the application is in the right state. 

When we get an LCM command to start logging, we need to:
- Create a logger instance that will go into `m_logger`
	- Change `Video Manager - Start_recording()` to `Init_connection`
- Run `m_video_reader->Start();`
- Read a first frame
- Command the video logger to start recording (ie `Start_recording`)
- Update stats as appropriate
- 
- Actually start recording

Remove `Stop_input`, or at least determine what it should be replaced with.
Same with `Start_input`

have frames - continue
no frame, timeout - back to open
no frame, no timeout - stay here

### Tracking States for Multiple Loggers
- Create and init a logger at the top level, default
- A 'stop' command will just straight up stop and remove the logger
- For 'logger state', every state has to loop through every logger,
```
for (logger in loggers)
{
  if(state = init)
  {
    // init the logger
    // change state to first frame
  }
}

for (logger in loggers)
{
  if(state = first_frame && have_frame)
  {
    // start recording
    // change state to record
  }
}

for (logger in loggers)
{
  if(state = record)
  {
    if(!is_active)
    {
      // close the stream
      // delete the logger
    }
  }
}


```

### change up states:
State
SRC_STATE_Open,
SRC_STATE_Ready
- Check timeout here, move to validate stream if we've timed out
- Move to 'read' if we have a logger in m_queue
SRC_STATE_Validate_stream,
- Jump into 'validate' if there is a timeout
- and there are no active loggers
SRC_STATE_Read
- Start checking loggers

Logger_state (don't need 'wait' since wait is a non-exsistent logger). Each video logger needs its own state).
SRC_STATE_Init_stream_decoding, (don't actually do this, just move the video logger init to the lcm callback).

SRC_STATE_First_frame, (call start recording here)
SRC_STATE_Record

Need to separate the 'record' from the 'write' functions, so we can record without writing to file to support the frame validation

### Updates to Writing


# References

