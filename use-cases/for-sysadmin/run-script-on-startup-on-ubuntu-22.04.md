# Run script on startup on Ubuntu 22.04

First, create a Systemd service file as in an example below. We will store this file as `/etc/systemd/system/disk-space-check.service`.

```plaintext
[Unit]
After=network.target

[Service]
ExecStart=/usr/local/bin/disk-space-check.sh

[Install]
WantedBy=default.target
```

**After**: Instructs systemd on when the script should be run. In our case the script will run after network connection. Other example could be mysql.target etc.\
**ExecStart**: This field provides a full path to the actual script to be executed on startup\
**WantedBy**: Into what boot target the systemd unit should be installed

Create a script to be executed on Ubuntu system startup. As specified in the above Step 1, the path and the name of the new script in our example will be `/usr/local/bin/disk-space-check.sh`.

The below is an example of such script:

```plaintext
#!/bin/bash
date > /root/disk_space_report.txt
du -sh /home/ >> /root/disk_space_report.txt
```

Set appropriate permissions for both, the Systemd service unit and script:

```plaintext
$ sudo chmod 744 /usr/local/bin/disk-space-check.sh 
$ sudo chmod 664 /etc/systemd/system/disk-space-check.service
```

Next, enable the service unit:

```txt
$ sudo systemctl daemon-reload 
$ sudo systemctl enable disk-space-check.service
```
