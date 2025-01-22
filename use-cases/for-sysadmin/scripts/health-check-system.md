# Health check System

#### 1.  Create script ( **check\_load.sh )**

```
#!/bin/bash
trig=5.00

#Get current load on the server
load=`cat /proc/loadavg | awk '{print $1}'`

#Check both
if [[ $load > $trig ]];
then
echo -e "\n\nYour server load is High. Check Immediatly\n\nServer load : $load " | mail -s "High Server Load [$load]" user@example.com
fi
```

#### 2. Create cronjob

```
*/2   *   *   *   *  /bin/bash   /path/to/the/folder/check_load.sh
```



