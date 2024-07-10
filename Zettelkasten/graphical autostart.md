202401240913
Status: #idea
Tags: #work #greensea #workspace #programming 

# graphical autostart
use graphical autostart to manage workspace startup in fullscreen
graphical startup script, installed as part of UBS

graphical autostart lets a workspace instace to start automatically
- create autostart scripts ./run/grapihcal_autostart
- UBS - gss_graphical_autostart.sh /usr/local/bin
- a .desktop file that the project installs in ~/.config/autostart
  - https://github.com/GreenseaSystems/project_vulcan_petrel/blob/41fe7c204522677bf8b9bb1e4ecf0f2f65101a66/run/gnome_autostart/gss_graphical_autostart.desktop#L7
- all desktop files in ~/.config/autostart run automatically
- /opt/greensea/graphical_autostart is where any graph start scropts need to be installed, like this: https://github.com/GreenseaSystems/project_stidd_rnav/blob/master/run/graphical_autostart/stidd_ui_startup_debian_stretch_x86_64.sh
- Then we have the option to configure an additional process_client instance





# References

