# Cấu Hình Tor Trên CLI Ubuntu

## 🧅 Hướng Dẫn Cài Đặt & Cấu Hình Tor Trên CLI Ubuntu

Tor (The Onion Router) là một công cụ mạnh mẽ giúp bảo vệ quyền riêng tư và ẩn danh khi truy cập internet. Bài viết này hướng dẫn cách cài đặt và cấu hình Tor trên dòng lệnh (CLI) trong môi trường Linux (Ubuntu/Debian).

***

### ✅ Bước 1: Cài đặt Tor

Trên Ubuntu hoặc Debian, bạn có thể cài đặt Tor dễ dàng từ kho chính thức:

```bash
sudo apt update
sudo apt install tor -y
```

Sau khi cài đặt, dịch vụ Tor sẽ được khởi động tự động.

***

### ✅ Bước 2: Kiểm tra trạng thái dịch vụ

Kiểm tra xem Tor đã hoạt động chưa:

```bash
sudo systemctl status tor
```

Nếu chưa chạy, bật dịch vụ bằng lệnh:

```bash
sudo systemctl enable --now tor
```

***

### ✅ Bước 3: Cấu hình Tor trong `/etc/tor/torrc`

Để cấu hình Tor, ta chỉnh sửa file cấu hình chính:

```bash
sudo nano /etc/tor/torrc
```

Một số thiết lập cơ bản:

#### 🎯 Cấu hình sử dụng như SOCKS5 proxy:

```bash
SocksPort 127.0.0.1:9050
Log notice file /var/log/tor/notices.log
```

#### 🎯 Cấu hình nâng cao để định tuyến DNS:

```bash
SocksPort 127.0.0.1:9050
DNSPort 53
AutomapHostsOnResolve 1
VirtualAddrNetworkIPv4 10.192.0.0/10
```

Sau khi chỉnh sửa, lưu lại và khởi động lại Tor:

```bash
sudo systemctl restart tor
```

***

### ✅ Bước 4: Kiểm tra hoạt động của Tor

Dùng `curl` hoặc các công cụ hỗ trợ proxy SOCKS5 để kiểm tra:

```bash
curl --socks5 127.0.0.1:9050 https://check.torproject.org/
```

Nếu thấy dòng:

> "Congratulations. This browser is configured to use Tor."\
> → Tor đã hoạt động thành công!

***

### ✅ Bước 5 (Tuỳ chọn): Định tuyến toàn bộ traffic hệ thống qua Tor

Bạn có thể cấu hình để tất cả lưu lượng mạng (bao gồm DNS và TCP) đi qua Tor. Lưu ý: chỉ nên áp dụng trong môi trường thử nghiệm hoặc gateway ảo hóa.

#### ✳️ 1. Bật IP forwarding

```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```

#### ✳️ 2. Cấu hình iptables:

```bash
# Xóa các rule cũ
sudo iptables -F
sudo iptables -t nat -F

# Redirect DNS và TCP
sudo iptables -t nat -A OUTPUT -p udp --dport 53 -j REDIRECT --to-ports 9053
sudo iptables -t nat -A OUTPUT -p tcp --syn -j REDIRECT --to-ports 9040
```

#### ✳️ 3. Chỉnh sửa file torrc tương ứng:

```bash
TransPort 9040
DNSPort 9053
```

Sau đó restart lại dịch vụ Tor.

***

### 🔐 Lưu ý bảo mật

* Không sử dụng Tor để truy cập tài khoản cá nhân hoặc các dịch vụ nhạy cảm nếu không hiểu rõ về mô hình ẩn danh.
* Đảm bảo kiểm soát ứng dụng nào được định tuyến qua Tor.
