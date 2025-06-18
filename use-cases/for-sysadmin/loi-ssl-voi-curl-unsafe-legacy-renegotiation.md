# Lỗi SSL với Curl ( Unsafe legacy renegotiation )

Dưới đây là bài viết blog tổng hợp về lỗi `unsafe legacy renegotiation` khi dùng `curl` với TLS, hướng tới chia sẻ cho cộng đồng kỹ thuật hoặc DevOps, SysAdmin:

***

### ⚠️ \[LỖI CURL] `unsafe legacy renegotiation disabled` – Nguyên nhân và cách khắc phục với OpenSSL 3

Khi làm việc với các hệ thống sử dụng TLS (HTTPS), bạn có thể gặp lỗi sau khi dùng `curl` để truy cập một API:

```
curl: (35) OpenSSL/3.0.x: error:0A000152:SSL routines::unsafe legacy renegotiation disabled
```

Lỗi này khiến bạn không thể kết nối tới server qua HTTPS, dù API vẫn hoạt động bình thường trên trình duyệt. Vậy chuyện gì đang xảy ra?

***

#### 🔍 Nguyên nhân: **Legacy TLS Renegotiation**

TLS "renegotiation" là quá trình client và server đàm phán lại phiên mã hóa giữa chừng. Nhưng trước năm 2010, cơ chế này có một lỗ hổng nghiêm trọng (CVE-2009-3555) cho phép **kẻ tấn công chèn dữ liệu vào phiên TLS** mà không bị phát hiện.

➡️ Để vá lỗ hổng, **RFC 5746** ra đời và định nghĩa lại cách thực hiện renegotiation một cách **an toàn**.

> ❗ Tuy nhiên, một số máy chủ cũ vẫn dùng cơ chế **legacy renegotiation**, không tương thích với chuẩn bảo mật hiện đại.

***

#### 🔐 OpenSSL 3.x: Không còn cho phép legacy renegotiation

Từ phiên bản **OpenSSL 3.0+**, **chế độ legacy renegotiation bị vô hiệu hóa mặc định** vì lý do bảo mật. Điều đó dẫn đến lỗi khi dùng:

```bash
curl https://your-api.example.com
```

***

#### 💡 Giải pháp tạm thời: Cho phép lại renegotiation **một cách có kiểm soát**

Bạn có thể bật lại renegotiation tạm thời bằng cách override cấu hình OpenSSL khi gọi `curl`:

**✅ Cách dùng:**

```bash
OPENSSL_CONF=<(echo -e '[default_conf]\nssl_conf = ssl_sect\n[ssl_sect]\nsystem_default = profile_sect\n[profile_sect]\nOptions = UnsafeLegacyRenegotiation\nMinProtocol = TLSv1.2\nMaxProtocol = TLSv1.2') \
curl --insecure https://your-api.example.com
```

**Giải thích:**

| Thành phần                            | Mục đích                          |
| ------------------------------------- | --------------------------------- |
| `Options = UnsafeLegacyRenegotiation` | Bật lại renegotiation cũ          |
| `Min/MaxProtocol = TLSv1.2`           | Ép chỉ dùng TLS 1.2               |
| `--insecure`                          | Bỏ qua xác thực chứng chỉ nếu cần |

> ⚠️ **Không dùng trong production hoặc hệ thống có dữ liệu nhạy cảm.** Chỉ dùng để debug/test tạm thời.

***

#### ✅ Giải pháp lâu dài: **Fix từ phía máy chủ**

Nếu bạn quản lý máy chủ (ngược lại là client), hãy đảm bảo:

* Phần mềm web server (Apache/nginx/Java/Tomcat) đã hỗ trợ **RFC 5746**
*   Tắt hẳn renegotiation nếu không cần thiết:

    ```apache
    SSLInsecureRenegotiation off
    ```
* Nâng cấp phần mềm lên phiên bản mới hỗ trợ secure renegotiation (Apache ≥ 2.2.15, nginx ≥ 1.3...)

***

#### 🧪 Cách kiểm tra server có hỗ trợ RFC 5746 hay không

```bash
openssl s_client -connect your-api.example.com:443
```

Nếu kết quả có dòng:

```
Secure Renegotiation IS NOT supported
```

➡️ Máy chủ đang **không hỗ trợ RFC 5746**, cần nâng cấp hoặc cấu hình lại.

***

#### 🛠️ Trường hợp đặc biệt: Palo Alto, F5, HAProxy

* Nếu kết nối HTTPS đi qua thiết bị trung gian như **Palo Alto firewall**, bạn cần kiểm tra:
  * Phiên bản PAN-OS (>= 8.1 sẽ hỗ trợ RFC 5746)
  * Chính sách TLS/SSL profile trong cấu hình
* Tắt hoặc điều chỉnh các chính sách decryption nếu gây lỗi handshake

***

#### 📌 Kết luận

| Tình huống               | Cách xử lý                                                 |
| ------------------------ | ---------------------------------------------------------- |
| Là client gặp lỗi        | Bật lại `UnsafeLegacyRenegotiation` tạm thời như hướng dẫn |
| Là server quản lý        | Cập nhật phần mềm và bật RFC 5746                          |
| Dùng thiết bị trung gian | Kiểm tra SSL/TLS handling của thiết bị                     |

***

#### 📥 Tham khảo nhanh: Lệnh mẫu

```bash
OPENSSL_CONF=<(echo -e '[default_conf]\nssl_conf = ssl_sect\n[ssl_sect]\nsystem_default = profile_sect\n[profile_sect]\nOptions = UnsafeLegacyRenegotiation') \
curl --insecure https://your-api.example.com
```

***

#### 👨‍💻 Thực tế với your-api.example.com

* Server không hỗ trợ RFC 5746
* Bắt buộc phải dùng legacy renegotiation → gây lỗi với OpenSSL 3.x
* Chỉ có thể kết nối thành công bằng cách bật `UnsafeLegacyRenegotiation` tạm thời

