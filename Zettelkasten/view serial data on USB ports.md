202204041036
Status: #idea
Tags: #programming #linux #terminal

# view serial data on USB ports
`ls /dev/ttyUSB*`
list [[USB device|USB devices]]

`cat` a port to see what's connected, sometimes it's not adequate

`cat` won't always work. Use screen if so:
`screen /dev/ttyUSB(...) [baud] (38400?)`
to kill `ctrl+a`, `shift+k` (a chord)

Generally, only one process can access a port, so after confirming the data on a given port, exit the process or the gss application won't be able to use that port.

`udev` rules to tie a specific device to a specific port so it doesn't change all the time. do it in `/tmp/` so you're not stuck with it forever.
  
never assume USB will be on a specific port (likely to be the same, but not for sure)


# References

