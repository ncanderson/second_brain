git 202212151136
Status: #idea
Tags: #work #bosch #camera 

# Bosch MIC 9000i
## Sending Bicom commands over HTTPS
Camera commands can be sent directly to the device using a web browser. This can be used to quickly test out different commands to the camera.

https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000007

https://172.31.0.8/rcp.xml?command=0x029e&type=T_DWORKD&direction=READ&num=1

https://172.31.0.8/rcp.xml?
**command=0x09A5** - RCP Bicom command
**type=P_OCTET** - data type
**direction=WRITE** - not sure if/when we might use READ
**num=2** - 2 for thermal camera.
**payload=0x800006011085000007** - Bicom command

## Constructing Bicom data payloads

| Bits   | Description     |
|--------|-----------------|
| 81     | Command Flag    |
| 0004   | Cam Server      |
| 0507   | Bicom Command   |
| 02     | Bicom Operation |
| 0000   | Data Payload    |

## Heater Work
#### Bosch said that we shouldn't send heater command!!!
Heater 0x023.0 
SetGet
0 = Off
1 = Auto
2 = On
*2=On only available for Gen5 HD*
#### URL testing
###### Heater Off
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=1&payload=0x8100040230030000
###### Heater On
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=1&payload=0x8100040230030002

##### Attempts using raw RCP+:
No success. Email sent to Bosch - https://greensea.atlassian.net/browse/OPTM-635?atlOrigin=eyJpIjoiZGE5MmY3YTU2YTkzNGRkOTg4NjViYWQwNDBkNmM3MDEiLCJwIjoiaiJ9

https://172.31.0.8/rcp.xml?command=0x0b0c&type=T_OCTET&direction=READ
https://172.31.0.8/rcp.xml?command=0x0b0c&type=T_OCTET&direction=WRITE&payload=1

## Thermal Zoom
#### In Fast
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000087
#### Out Fast
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000007

#### In Slow
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000081
#### Out Slow
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085

Zooms fully in and out but is not selectable (i.e. there no selection for the amount of zoom to apply), and shows OSD 

https://172.31.0.8/rcp.xml?command=0x09a5&type=P_OCTET&direction=WRITE&num=1&payload=0x810004050702000[0-c]

Selectable Zooming, but does not show OSD. Does not have the full range of zoom control that can be acheved via the web gui

in full speed
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000087
in slow speed
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000081
out full speed
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000007
out slow speed
https://172.31.0.8/rcp.xml?command=0x09A5&type=P_OCTET&direction=WRITE&num=2&payload=0x800006011085000001

It looks like the web gui is achieving its behavior by sending 'in full', followed quickly by 'stop'

## Video Stream URL Args
Arguments to add when connecting to the streams:
![[Pasted image 20221215113727.png]]
This isn't everything: 

| Arg | Format | Notes |
|-----|--------|-------|
| h26x=0 | MJPEG | Works for all settings |
| h26x=3 | MPEG-4 SH++ | Does not appear to work on this camera |
| h26x=4 | H.264 | Requires camera set to H.264 via config menu |
| h26x=5 | HEVC / H.265 | Requires camera set to H.265 via config menu |

## Reconnecting to a video stream
Sometimes the video stream craps out.

- go to dashboard
- view active connections - kb value indicated there was visual output but no thermal output on video 2
- video 2 showed 0 resolution (0x0) for video streams
- jpg stream showed something else

something going on with the video encoder output for the video camera streams

- go to config > camera > encoder regions
- there was thermal data showing up, all indicates that the thermal camera was working
- encoder streams, tab 2, click 'set'
- video 2 tab

## Backup Config
You can download a file with all current config

## Azimuth

# References
[[Bosch RCP Sample Application]]