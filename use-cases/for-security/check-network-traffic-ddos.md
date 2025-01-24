# Check network traffic ( Ddos )

Các câu lệnh thường được dùng khi kiểm tra check lưu lượng request tới

1. `netstat` command:

```
netstat -na
```

```
netstat -an | grep :80 | sort
netstat -an | egrep ":80|:443" | sort
```

3. To see if many active `SYNC_REC` :

```
netstat -n -p|grep SYN_REC | wc -l
```

4. To list out all the IP addresses sending `SYNC_REC` statuses, use the command:

```
netstat -n -p | grep SYN_REC | sort -u
```

5. To further list all the unique IP addresses sending `SYNC_REC` statuses, use the command:

```
netstat -n -p | grep SYN_REC | awk '{print $5}' | awk -F: '{print $1}'
```

```
netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n
```

```
netstat -anp |grep 'tcp|udp' | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n
```

```
netstat -ntu | grep ESTAB | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr
```

```
netstat -plan|grep :80|awk {'print $5'}|cut -d: -f 1|sort|uniq -c|sort -nk 1
```
