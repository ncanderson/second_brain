202209190959
Status: #idea
Tags: 

# Upgrading Obsidian
Want to have Obsidian as an icon on the favorites bar, since it required some work to get it there in the first place.

Make the Appimage file executable. 

There is a .desktop file in ~/.local/share/applications. Update the executed file for that shortcut.

Move the Appimage to /bin

When upgrading, ensure the `.desktop` file is pointing at the right version, as the appimage will likely have a different name when installed.1

Should just work.

# References
https://linuxconfig.org/how-to-create-an-integrated-application-launcher-for-an-appimage-file-in-ubuntu
