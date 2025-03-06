202309061245
Status: #idea
Tags: #linux #programming #work 

# serial
Use [[cutecom]] to mock serial data.

This script can be used to send data to a specific port:
```bash
echo starting socat pair /dev/ttyVSP0=/dev/ttyVSP1
sudo socat -d -d pty,raw,echo=0,link=/dev/ttyVSP0 pty,raw,echo=0,link=/dev/ttyVSP1&

# chown the port so it can be opened
sleep .1
sudo chown -R $USER /dev/ttyVSP* && sudo chown $USER /dev/ttyVSP*
```


# References

