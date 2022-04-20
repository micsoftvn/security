---
description: Simple command-line snippet manager
---

# Sử dụng PET

#### 1. Cài đặt và sử dụng  PET trên Ubuntu&#x20;

```
sudo apt-get install linuxbrew-wrapper
wget https://github.com/knqyf263/pet/releases/download/v0.3.6/pet_0.3.6_linux_amd64.deb
dpkg -i pet_0.3.6_linux_amd64.deb
```

Sau khi đã cài đặt thành công

![](<../../.gitbook/assets/image (1).png>)

![](<../../.gitbook/assets/image (2).png>)

Ví dụ câu lệnh Monitor DDOS

```
netstat -nap | grep \:80\  | awk '{print $5}' | tr ":" " " | awk '{print $1}' | sort | uniq -c | sort -n
```
