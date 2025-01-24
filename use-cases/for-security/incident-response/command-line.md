---
description: A few command line used
---

# Command line

**With Iptables**

* Block Ip, Subnet all in coming

```
iptables -A INPUT -s <ranger-ip> -j DROP
```

* Block DDos DNS Attack

```
iptables -A INPUT -p udp -m length --length 56 -j DROP
```

### On Windows

C. Đánh giá nhanh với hệ thống Windows\*\*

1\. Check kiểm tra sự kiện nhật ký với

```
eventvwr
```

2\. Kiêm tra cấu hình mạng bằng cách sử dụng:

```
arp -a
```

```
netstat -nr
```

3\. Kiểm tra lại các cổng mở và các kết nối liên quan

```
netstat –nao, netstat –vb, net session, net use
```

4\. Kiểm tra nhóm người dùng và quản trị hệ thống

```
lusrmgr, net users, net localgroup administrators, net group administrators
```

5\. Kiểm tra về job, scheduled

```
schtasks
```

6\. Kiểm tra về autostart

```
msconfig
```

7\. Đánh giá kiểm tra sơ bộ về process

```
askmgr, wmic process list full
```

8\. Các services đang chạy trên hệ thống

```
net start, tasklist /svc
```

9\. Kiểm tra DNS và cấu hình file hosts trên hệ thống

```
ipconfig /all, ipconfig /displaydns, more %SystemRoot%\System32\Drivers\etc\hosts
```

10\. Check kiểm tra file hệ thống

```
sigverif
```

11\. Tìm kiếm các file bị thay đổi trên hệ thống

```
dir /a/o-d/p %SystemRoot%\System32
```

### **On Unix**

1\. Kiểm tra đánh giá toàn bộ các file log trong các thư mục bên dưới

```
/var/log, /var/adm, /var/spool
```

2\. Chạy các lệnh kiểm tra log bảo mật trên hệ thống

```
wtmp, who, last, lastlog
```

3\. Check kiểm tra network

```
arp –an, route print
```

4\. Kiểm tra port mạng và kết nối

```
netstat –nap (Linux), netstat –na (Solaris), lsof –i
```

5\. Check kiểm tra user

```
/etc/passwd
```

6\. Kiểm tra các tiến trình tự động

```
/etc/crontab
```

```
ls /etc/cron.*
```

```
ls /var/at/jobs
```

7\. Check kiểm tra DNS settings

```
more /etc/resolv.conf
```

```
more /etc/hosts
```

8\. Kiểm tra lại các gói đã cài đặt trên hệ thống

```
rpm -Va
```

```
pkgchk
```

9\. Kiểm tra autostarts

```
chkconfig --list
```

Với Solaris

```
ls /etc/rc*.d
```

10\. Kiểm tra các tiến trình đang chạy trên hệ thống

```
ps -aux
```

11\. Kiểm tra các file thay đổi gần đây

```
ls -lat
```

```
find / -mtime -2d -ls
```
