# Cài Đặt Và Cấu Hình dnsmasq Trên Ubuntu

### 🧩 dnsmasq Là Gì?

`dnsmasq` là một dịch vụ DNS và DHCP cực nhẹ, được thiết kế cho môi trường nhỏ như mạng gia đình, văn phòng, lab hoặc server cục bộ. Nó có thể:

* Tăng tốc độ truy vấn DNS nhờ cache
* Làm DNS trung gian chuyển tiếp ra ngoài (8.8.8.8, 1.1.1.1,…)
* Trỏ tên miền nội bộ tới IP tuỳ ý (ví dụ: `dev.local → 127.0.0.1`)
* (Tuỳ chọn) Làm máy chủ DHCP cấp IP nội bộ

***

### ⚙️ Bước 1: Cài Đặt dnsmasq

```bash
sudo apt update
sudo apt install dnsmasq -y
```

Kiểm tra phiên bản:

```bash
dnsmasq --version
```

***

### 🛠️ Bước 2: Cấu Hình dnsmasq

Mở file cấu hình:

```bash
sudo nano /etc/dnsmasq.conf
```

Thêm các dòng sau (hoặc bỏ comment nếu đã có):

```conf
# Chuyển tiếp DNS ra ngoài (Google DNS và Cloudflare)
server=8.8.8.8
server=1.1.1.1

# Tăng tốc truy vấn DNS nhờ cache
cache-size=1000

# Trỏ tên miền nội bộ (ví dụ: test.local → 127.0.0.1)
address=/test.local/127.0.0.1

# Ghi log các truy vấn DNS
log-queries
log-facility=/var/log/dnsmasq.log
```

Lưu lại với `Ctrl + O`, Enter, và `Ctrl + X`.

***

### ❌ Bước 3: Khắc Phục Lỗi Port 53 (Nếu Có)

Nếu gặp lỗi như:

```
failed to create listening socket for port 53: Address already in use
```

Điều này có nghĩa **cổng 53 đã bị dịch vụ khác chiếm**, thường là `systemd-resolved`.

#### 👉 Giải pháp: Tắt systemd-resolved

```bash
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
```

Xoá link `/etc/resolv.conf` và trỏ DNS về `dnsmasq`:

```bash
sudo rm /etc/resolv.conf
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
sudo chattr +i /etc/resolv.conf
```

> Nếu không muốn tắt `systemd-resolved`, bạn có thể chỉnh `/etc/systemd/resolved.conf` và đặt `DNSStubListener=no`.

***

### 🔁 Bước 4: Khởi Động Lại dnsmasq

```bash
sudo systemctl restart dnsmasq
sudo systemctl enable dnsmasq
```

Kiểm tra trạng thái:

```bash
sudo systemctl status dnsmasq
```

***

### 🔍 Bước 5: Kiểm Tra Hoạt Động

```bash
dig google.com @127.0.0.1
```

Kiểm tra log:

```bash
tail -f /var/log/dnsmasq.log
```

***

### 💡 Tùy Chọn: Chặn Quảng Cáo hoặc DNS Nội Bộ

Bạn có thể trỏ các tên miền quảng cáo về `0.0.0.0`:

```conf
address=/ads.example.com/0.0.0.0
address=/doubleclick.net/0.0.0.0
```

