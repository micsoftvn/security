# PAC Proxy: Tự Động Cấu Hình Proxy Trong Môi Trường Doanh Nghiệp

#### I. PAC Proxy là gì?

PAC (Proxy Auto-Config) là một tệp tin JavaScript đặc biệt được sử dụng để xác định tự động xem một truy cập web nên đi qua proxy nào hoặc đi trực tiếp. Các doanh nghiệp thường dùng PAC file để:

* Chỉnh tuyến linh hoạt theo IP, domain, hoặc thời gian
* Tránh việc áp dụng proxy một cách cứng nhắc cho tất cả kế nối
* Giảm tải cho proxy và nâng cao hiệu suất

PAC file chỉ có duy nhất một hàm:

```
function FindProxyForURL(url, host)
```

Hàm này được trình duyệt gọ i mỗi khi truy cập website, và nó trả về chuỗi proxy hoặc "DIRECT".

***

#### II. Các Cách Trả Về Từ PAC

| Chuỗi trả về               | Ý nghĩa                                                   |
| -------------------------- | --------------------------------------------------------- |
| `DIRECT`                   | Kết nối trực tiếp, không qua proxy                        |
| `PROXY host:port`          | Dùng proxy HTTP tại host và port nhập                     |
| `SOCKS host:port`          | Dùng proxy SOCKS (nếu trình duyệt hỗ trợ)                 |
| `PROXY A; PROXY B; DIRECT` | Thử theo thứ tự động proxy A, rồi đến B, cuối cùng DIRECT |

***

#### III. Các Hàm JavaScript Hỗ Trợ Trong PAC

| Hàm                           | Mô tả                                             |
| ----------------------------- | ------------------------------------------------- |
| `dnsDomainIs(host, domain)`   | Kiểm tra domain có khớp                           |
| `shExpMatch(str, shexp)`      | So khớp theo shell wildcard (VD: `*.example.com`) |
| `isInNet(ip, pattern, mask)`  | Kiểm tra IP thuộc subnet                          |
| `dnsResolve(host)`            | Lây IP từ hostname                                |
| `myIpAddress()`               | IP của thiết bị client                            |
| `timeRange(), weekdayRange()` | Dùng cho logic theo giờ/ngày                      |

***

#### IV. Ví Dụ Thực Tế

**1. Đơn Giản:**

```
function FindProxyForURL(url, host) {
    if (shExpMatch(host, "*.example.com")) {
        return "PROXY proxy.example.com:8080";
    }
    return "DIRECT";
}
```

**2. Phức tạp:**

```
function FindProxyForURL(url, host) {
    if (isInNet(host, "192.168.1.0", "255.255.255.0") ||
        shExpMatch(host, "*.internal.local")) {
        return "DIRECT";
    }
    return "PROXY proxy.mycorp.com:3128; DIRECT";
}
```

***

#### V. Triển Khai PAC File

**1. Tạo file proxy.pac:**

```
nano /var/www/html/proxy.pac
```

**2. Upload lên web server:**

* VD: http://intranet.yourcompany.com/proxy.pac

**3. Cấu hình client:**

* **Windows**: Internet Options > Connections > LAN Settings > Use script: `http://...`
* **macOS**: System Preferences > Network > Proxies > Auto config
* **Linux GNOME**: Settings > Network Proxy > Automatic > Nhập URL

***

#### VI. Gỡ Lỗi và Kiểm Tra PAC File

* **Chrome**: `chrome://net-internals/#proxy`
* **Firefox**: `about:networking#logging`
* **Test nhanh**:

```
curl --proxy "$(cat proxy.pac | grep return)" http://example.com
```

***

#### VII. Kết Luận

PAC proxy mang lại khả năng linh hoạt cao cho việc điều hướng truy cập Internet trong một hệ thống doanh nghiệp. Việc tự động định tuyến giợi quyết nhiều vấn đề về an ninh, tối ưu hóa lưu lượng và tốc độ truy cập.

***

**Gợi Ý**: Hãy thiết kế PAC file theo chiến lược riêng phù hợp với các mức độ bảo mật và logic kinh doanh của tổ chức.

(Nếu bạn muốn viết một PAC file cá nhân hóa cho mạng của bạn, hãy cung cấp các dạng IP/domain/proxy bạn dự định để mình viết giùm!)
