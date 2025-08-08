# Chrome_Remote_Desktop_w_Ubuntu22.04

The author has been using Teamviewer or Anydesk for remote working. Although these apps are good for Windows and Mac, they suffer laggyness, disconnection more than they should on Linux.
Chrome Remote Desktop (CRD) gives much better smoothness, crispyness, and responsiveness. It creates a new window session instead of recording the screen. However, I find `gnome` having issue with CRD. 
The solution I find is to use `xfce4` for the CRD.

1. Follow [the guideline](https://remotedesktop.google.com/headless) to install CRD.
2. Then install and set `xfce4`
```
sudo apt install xfce4 xfce4-goodies -y
# echo 'exec /usr/bin/xfce4-session' > ~/.chrome-remote-desktop-session # If you want default to be xfce4
sudo reboot
```

## 🔐 Fixing Annoying Password Prompts in GNOME after Installing XFCE on Ubuntu
When you install `xfce4`, it installs its own polkit agent (xfce-polkit) and possibly overrides or disables GNOME’s more permissive behavior. The Fix: Explicit `.pkla` Rules:

```
sudo mkdir -p /etc/polkit-1/localauthority/50-local.d/
sudo nano /etc/polkit-1/localauthority/50-local.d/95-desktop-privileges.pkla
```
Copy in: (change <your_username> to your username)
```
[Allow NetworkManager actions]
Identity=unix-user:<your_username>
Action=org.freedesktop.NetworkManager.*
ResultAny=yes
ResultInactive=yes
ResultActive=yes

[Allow UDisks2 mounting]
Identity=unix-user:<your_username>
Action=org.freedesktop.udisks2.*
ResultAny=yes
ResultInactive=yes
ResultActive=yes

[Allow ColorManager: create profile]
Identity=unix-user:<your_username>
Action=org.freedesktop.color-manager.*
ResultAny=yes
ResultInactive=yes
ResultActive=yes
```
Restart `polkit` and reboot:
```
sudo systemctl restart polkit
reboot
```

## To enable nvidia gpu redering on gpu instances:
* Run sudo nvidia-xconfig --preserve-busid --enable-all-gpus --connected-monitor=DFP-0,DFP-1,DFP-2,DFP-3
    * There may be a warning message, which can be ignored:
    * WARNING: Unable to locate/open X configuration file.
         sh: 1: pkg-config: not found
* Edit /etc/X11/xorg.conf, add module paths to files section as follows:
```
Section "Files"
        ModulePath "/usr/lib/x86_64-linux-gnu/nvidia/xorg"
        ModulePath "/usr/lib/xorg/modules"
EndSection
```
* After this restart gdm: `sudo systemctl restart gdm` and verify there’s no error message sudo systemctl status gdm

## ⚠️ Prevent Session Termination
This is a known issue on Ubuntu with GNOME where after a long idle period or suspend, the user session is terminated or corrupted in a way that prevents successful login via GUI—even with the correct password. 
```
sudo loginctl enable-linger $USER
```

## 🛑 How to terminate the two sessions?
As you use the CRD, you will find that the two sessions can interfere with each other. 
To close the gnome session:
```
DISPLAY=:0 gnome-session-quit --logout --no-prompt
```
To close the xfce session:
```
DISPLAY=:20 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus xfce4-session-logout --logout --fast
```

## ❌ Known issue with OpenGL
While the GPU is accessible in the remote session, the OpenGL backend has issues and will fall back to the CPU.
