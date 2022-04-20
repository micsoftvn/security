---
description: >-
  Thiết lập cấu hình CMD log trên Linux phục vụ việc giám sát các câu lệnh được
  sử dụng trong CLI
---

# Thiết lập cấu hình CMD log

Code file bash

```
#!/bin/sh
echo "#Log cmdlog" >> /etc/rsyslog.conf
echo "local6.*           /var/log/cmdlog.log" >> /etc/rsyslog.conf
echo "
/var/log/cmdlog.log
{
   compress
   weekly
   rotate 12
   sharedscripts	
   postrotate
        /bin/kill -HUP \`cat /var/run/syslogd.pid 2> /dev/null\` 2> /dev/null || true
    endscript
}
" > /etc/logrotate.d/cmdloghttps://github.com/a2o/snoopyecho ""
echo "
export PROMPT_COMMAND='RETRN_VAL=\$?;logger -p local6.debug \"[cmdlog] \$(whoami) [\$\$]: \$(history 1 | sed \"s/^[ ]*[0-9]\+[ ]*//\" ) [\$RETRN_VAL] [\$(echo \$SSH_CLIENT | cut -d\" \" -f1)]\"'
" >> /etc/bashrc
systemctl restart rsyslog
echo "Finish Config CMDlog"
```

Hoặc bạn có thể sử dụng Snnopy

```
https://github.com/a2o/snoopy
```

```
https://github.com/a2o/snoopy/releases/download/snoopy-2.4.14/snoopy-2.4.14.tar.gz
```

```
https://github.com/a2o/snoopy/blob/master/doc/INSTALL.md
```

```
https://github.com/a2o/snoopy/releases/download/snoopy-2.4.14/snoopy-2.4.14.tar.gz
```
