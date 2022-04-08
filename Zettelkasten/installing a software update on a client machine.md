202204061623
Status: #idea
Tags: #programming #software #ubuntu #linux

# installing a software update on a client machine
- deliver an update to F5
  - email javad
    - dpkg -l | grep opensea-environment-last-installed
    - need javad for sudo pw
  - install same update to F5 and OPT
  - team viewer into their system, to install
    - take a backup of current install, put in a safe place
      - ~/gss_backups/YYYYMMDD_buoy_1_(am/pm)
      - copy over to my machine
      - put in google drive
    - [[dpkg]] -l > dpkg_l.txt
      - put in the backup folder
    - verify the installer of the project.run was built against same version of POE F5 have installed
    - copy update to the target in computer into ~/gss_updates
    - run killall.sh to ensure all previous software isn't running (make sure to run as sudo)
    
    - double check that the previous install was backed up
    - install project
    - have them reboot (since the project starts up with autostart) or re-run the autostart script with &> /dev/null &
    - turn of sonar transmitting (currently defaults on) CHECK WITH CHRIS
  - quick deck checks to make sure everything is working correctly
    - address any obvious config issues on the system


# References

