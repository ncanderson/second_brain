202212151136
Status: #idea
Tags: #work #bosch #camera 

# Bosch MIC 9000i
Arguments to add when connecting to the streams:
![[Pasted image 20221215113727.png]]
This isn't everything: 
| Arg | Format | Notes |
|-----|--------|-------|
| h26x=0 | MJPEG | Works for all settings |
| h26x=3 | MPEG-4 SH++ | Does not appear to work on this camera |
| h26x=4 | H.264 | Requires camera set to H.264 via config menu |
| h26x=5 | HEVC / H.265 | Requires camera set to H.265 via config menu |


# References

