202404021513
Status: #idea
Tags: #programming #linux #ubuntu 

# flash linux machine
See [this page](https://help.ubuntu.com/community/Installation/FromUSBStick) for a guide.

- Download the `.iso` you want [from here](https://ubuntu.com/download/desktop)
- Check the hash using the following. Compare to stated values [like you can find here](http://releases.ubuntu.com/bionic/SHA256SUMS)
```bash
nanderson ~/Desktop () $ sha256sum ubuntu-18.04.6-desktop-amd64.iso 
```
- May need to reformat the drive. In my case, the first USB port didn't work, so I had to try the other one before it was recognized by the system. The 'Disks' utility can reformat the drive.
- Open 'Startup Disk Creator', not sure if this comes with Ubuntu or I installed it, but it will let you put the `.iso` onto the thumb drive.


# References
- https://help.ubuntu.com/community/Installation/FromUSBStick
- https://askubuntu.com/questions/22381/how-to-format-a-usb-flash-drive
- https://www.usbmemorydirect.com/blog/how-to-format-flash-drive-on-ubuntu-18-04/
