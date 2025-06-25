# Khắc phục lỗi máy chủ không kết nối Internet và không phân giải DNS do iptables

Dưới đây là bài viết tổng kết lại quá trình xử lý sự cố **host không truy cập được Internet và không phân giải được tên miền** do cấu hình `iptables` và `resolv.conf`. Bạn có thể đăng lên blog kỹ thuật, wiki nội bộ hoặc GitHub Pages.

***

## 🧰 Khắc phục lỗi máy chủ không kết nối Internet và không phân giải DNS do iptables

### Tình huống

Sau khi cấu hình tường lửa bằng `iptables`, hệ thống gặp các sự cố sau:

* `ping 8.8.8.8` không thông
* `dig google.com` không có phản hồi
* Các container Docker cũng không truy cập ra ngoài

Tuy `iptables -P OUTPUT ACCEPT` đã cho phép truy cập ra ngoài, nhưng hệ thống **vẫn không kết nối được**.

***

### 📌 Nguyên nhân và hướng xử lý

#### ✅ 1. `iptables INPUT DROP` nhưng thiếu rule cho kết nối hồi đáp

Nếu bạn đặt policy mặc định `INPUT DROP`, bạn **phải cho phép các kết nối đã được thiết lập (`ESTABLISHED`) quay lại**, ví dụ:

```bash
iptables -I INPUT 1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

Nếu thiếu dòng này, mọi kết nối từ host ra ngoài sẽ **không nhận được phản hồi**, gây lỗi `ping`, `dig`, `curl`,...

***

#### ✅ 2. Thiếu NAT (MASQUERADE) khi có Docker hoặc route nhiều interface

Khi dùng Docker hoặc có mạng nội bộ, bạn cần thêm rule NAT:

```bash
iptables -t nat -A POSTROUTING -o ens5 -j MASQUERADE
```

Thay `ens5` bằng tên interface mạng thật của bạn (`ip a` để kiểm tra). Điều này giúp gói tin từ mạng nội bộ hoặc container có thể đi ra Internet.

***

#### ✅ 3. DNS không phân giải được do `/etc/resolv.conf` cấu hình sai

Khi kiểm tra:

```bash
cat /etc/resolv.conf
```

Thường thấy:

```
nameserver 127.0.0.1
```

→ Nếu hệ thống không có DNS server local như `systemd-resolved`, `dnsmasq`, thì lệnh `dig google.com` sẽ không phân giải được.

**Cách sửa nhanh:**

```bash
echo -e "nameserver 1.1.1.1\nnameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

***

#### ✅ 4. Tắt `systemd-resolved` nếu cần cấu hình DNS thủ công

Nhiều distro mới dùng `systemd-resolved`, gây ghi đè `resolv.conf`. Để tắt hoàn toàn và quản lý DNS thủ công:

```bash
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
sudo rm -f /etc/resolv.conf
echo -e "nameserver 1.1.1.1\nnameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

***

### ✅ Tổng hợp lệnh khắc phục

```bash
# Cho phép phản hồi từ kết nối đã thiết lập
iptables -I INPUT 1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Thêm NAT cho kết nối outbound
iptables -t nat -A POSTROUTING -o ens5 -j MASQUERADE

# Ghi đè cấu hình DNS tạm thời
echo -e "nameserver 1.1.1.1\nnameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

***

### ✅ Kiểm tra lại sau khi khắc phục

```bash
ping 8.8.8.8
dig google.com
curl https://google.com -I
```

***

### 📚 Kinh nghiệm rút ra

* Luôn kiểm tra `iptables -L -v -n` và đảm bảo cho phép `ESTABLISHED,RELATED` nếu bạn dùng `INPUT DROP`
* Không quên kiểm tra bảng `nat` (`iptables -t nat -L -v -n`)
* Kiểm tra DNS từ `resolv.conf`, nhất là khi dùng `systemd-resolved` hoặc DHCP
* Sử dụng `dig @8.8.8.8 domain.com` để tách biệt vấn đề DNS nội bộ

***
