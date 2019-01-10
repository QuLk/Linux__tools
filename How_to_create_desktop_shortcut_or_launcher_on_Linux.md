### How to create desktop shortcut or launcher on dock of Ubuntu
QuLk @ 2019.1.10

Use XMind as example. Assume you can start XMind without problem by double clicking XMind in the installation folder. Let's say to double click the file /home/username/apps/XMind_8/XMind_amd64/Xmind, XMind will be launched.

#### 1. create XMind_8.sh file to start XMind 8
```bash
#!/bin/bash
cd /home/username/apps/XMind_8/XMind_amd64
./XMind
```

#### 2. Create launch file
Create launch file XMind_8.desktop in the folder /home/username/.local/share/applications/:
```bash
[Desktop Entry]                                                             
Version=8.0                                                                 # version of an app.
Type=Application                                                            # type.
Terminal=false                                                              # whether an app requires to be run in a terminal.
Exec=/home/username/apps/XMind_8/XMind_amd64/XMind_8.sh                     # command used to launch an app.
Name=XMind 8                                                                # name of an app.
Comment=Mind mapping software                                               # comment which appears as a tooltip.
Icon=/home/username/apps/XMind_8/XMind_amd64/new_branding_new_logo.png      # location of icon file.
```

#### 3. log out and log in again. 
You will find XMind icon in the applications. Use it the same way as other standard applications.    
Done.
