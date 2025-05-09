# Cài Đặt Fluent Bit Trên Amazon Linux 2023 & Tạo Repository Offline

## 🚀 Hướng Dẫn&#x20;

Fluent Bit là một lightweight log processor rất phổ biến trong các hệ thống logging hiện đại. Với việc Amazon Linux 2023 đã chính thức ra mắt, nhiều hệ thống đang chuyển dịch từ AL2/CentOS sang AL2023, và nhu cầu cài Fluent Bit trên nền tảng này cũng tăng theo.

### 🔗 Repository chính thức cho Amazon Linux 2023

Fluent Bit đã **cung cấp repository chính thức** cho Amazon Linux 2023 tại địa chỉ:

```
https://packages.fluentbit.io/amazonlinux/2023/
```

#### ❗ Lưu ý quan trọng

* **Kiến trúc hỗ trợ**: Repo này hiện **chỉ hỗ trợ kiến trúc `x86_64`**. Nếu bạn dùng kiến trúc `aarch64` (như AWS Graviton), repo này **không hoạt động được**.

***

### 🧰 Cài đặt Fluent Bit trên máy có Internet

#### 1. Tạo file cấu hình repo Fluent Bit

```bash
cat > /etc/yum.repos.d/fluent-bit.repo <<EOF
[fluent-bit]
name=Fluent Bit
baseurl=https://packages.fluentbit.io/amazonlinux/2023/
gpgcheck=1
gpgkey=https://packages.fluentbit.io/fluentbit.key
enabled=1
EOF
```

#### 2. Cài đặt Fluent Bit

```bash
sudo yum install -y fluent-bit
```

#### 3. Khởi động Fluent Bit

```bash
sudo systemctl start fluent-bit
```

Bạn có thể kiểm tra trạng thái:

```bash
sudo systemctl status fluent-bit
```

***

### 📦 Tạo repository offline để dùng nội bộ (air-gapped)

Trong nhiều trường hợp bạn không có kết nối mạng (server offline hoặc security zone chặt chẽ), bạn có thể tạo một repo offline như sau:

#### Bước 1: Tải các RPM Fluent Bit và phụ thuộc

Thực hiện trên **máy có internet**:

```bash
dnf install --downloadonly --downloaddir=./fluentbit_rpms fluent-bit
```

#### Bước 2: Tạo metadata với `createrepo`

Cài `createrepo_c` nếu chưa có:

```bash
sudo dnf install -y createrepo_c
```

Tạo metadata cho thư mục RPM:

```bash
createrepo ./fluentbit_rpms
```

#### Bước 3: Chuyển repository đến server nội bộ

Sao chép toàn bộ thư mục `fluentbit_rpms/` sang server cần cài Fluent Bit (dùng USB, SCP, rsync...)

#### Bước 4: Cấu hình repo offline trên server nội bộ

```bash
cat > /etc/yum.repos.d/fluent-bit-offline.repo <<EOF
[fluent-bit-offline]
name=Fluent Bit Offline
baseurl=file:///path/to/fluentbit_rpms
enabled=1
gpgcheck=0
EOF
```

> Thay `/path/to/fluentbit_rpms` bằng đường dẫn thực tế.

#### Bước 5: Cài đặt Fluent Bit từ repo offline

```bash
sudo yum install fluent-bit
```

***

### 🔗 Tài liệu tham khảo

* [Fluent Bit Repo List](https://packages.fluentbit.io/)
* [Gist cài đặt Fluent Bit + cấu hình đầu vào/ra](https://gist.github.com/rupeshtiwari/9c8eeaea037c10466492446adb6293ef)

***

Bạn muốn mình xuất bản bài viết này thành file `.md` hoặc đăng lên blog cá nhân/GitHub Pages không?
