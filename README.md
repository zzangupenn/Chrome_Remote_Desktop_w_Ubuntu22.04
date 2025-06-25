# Chrome_Remote_Desktop_w_Ubuntu22.04

The author has been using Teamviewer or Anydesk for remote working. Although these apps are good for Windows and Mac, they suffer laggyness, disconnection more than they should on Linux.
Chrome Remote Desktop (CRD) gives much better smoothness, crispyness, and responsiveness. It creates a new window session instead of recording the screen. However, I find `gnome` having issue with CRD. 
The solution I find is to use `xfce4` for the CRD.

1. Follow [the guideline](https://remotedesktop.google.com/headless) to install CRD.
2. Then install and set `xfce4`
```
sudo apt install xfce4 xfce4-goodies -y
echo 'exec /usr/bin/xfce4-session' > ~/.chrome-remote-desktop-session
sudo reboot
```
