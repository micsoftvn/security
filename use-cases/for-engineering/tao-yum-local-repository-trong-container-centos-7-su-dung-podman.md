# Tạo YUM Local Repository Trong Container CentOS 7 Sử Dụng Podman

## 🐧 Hướng Dẫn&#x20;

Trong quá trình làm việc với môi trường CentOS, việc sử dụng một **YUM local repository** sẽ giúp bạn:

* Cài đặt gói nhanh chóng không phụ thuộc Internet
* Tùy biến repo theo yêu cầu
* Kiểm soát gói phần mềm trong môi trường kiểm thử hoặc offline

Bài viết này hướng dẫn bạn từng bước để tạo một **local YUM repo** bên trong container CentOS 7, sử dụng công cụ **Podman** – một lựa chọn thay thế cho Docker mà không cần quyền root.

***

### ✅ Bước 1: Pull Image CentOS 7

```bash
podman pull centos:7
```

***

### ✅ Bước 2: Chạy Container CentOS 7

Chạy container với quyền tương tác và đặt tên dễ nhớ:

```bash
podman run -it --name centos7-yumrepo centos:7 /bin/bash
```

***

### ✅ Bước 3: Cài Các Gói Cần Thiết

Trong container, cài đặt các công cụ hỗ trợ tạo repo:

```bash
yum install -y yum-utils createrepo httpd
```

***

### ✅ Bước 4: Tạo Thư Mục Localrepo và Copy RPM Vào

Tạo thư mục repo:

```bash
mkdir -p /var/www/html/localrepo
```

Sau đó, từ **host**, bạn có thể **copy các file .rpm** vào container:

```bash
podman cp ./rpms/. centos7-yumrepo:/var/www/html/localrepo/
```

**Hoặc**, nếu bạn đã có thư mục `./rpms` trên máy, mount trực tiếp vào container:

```bash
podman run -it --name centos7-yumrepo \
  -v $(pwd)/rpms:/var/www/html/localrepo \
  centos:7 /bin/bash
```

***

### ✅ Bước 5: Tạo Metadata Cho Repo

Trong container:

```bash
createrepo /var/www/html/localrepo/
```

***

### ✅ Bước 6: Tạo File Repo YUM

Tạo file `/etc/yum.repos.d/local.repo` với nội dung:

```ini
[localrepo]
name=Local YUM Repo
baseurl=file:///var/www/html/localrepo/
enabled=1
gpgcheck=0
```

> Nếu bạn muốn container chạy HTTP để các máy khác cũng có thể truy cập, dùng:

```ini
baseurl=http://<IP>:80/localrepo/
```

***

### ✅ Bước 7: Khởi Chạy HTTP Server (Tùy chọn)

Do Podman không chạy systemd mặc định, bạn có thể bật HTTP server tạm thời:

```bash
cd /var/www/html
python3 -m http.server 80
```

Hoặc:

```bash
httpd -DFOREGROUND
```

***

### ✅ Bước 8: Kiểm Tra Hoạt Động

Làm sạch cache và kiểm tra repo:

```bash
yum clean all
yum repolist
```

***

### ✅ Bước 9: Lưu Container Thành Image Mới

Sau khi cấu hình xong, commit lại container thành image để dùng lại về sau:

```bash
podman commit centos7-yumrepo centos7-with-localrepo
```

***

### 📌 Kết luận

Việc sử dụng Podman để dựng môi trường CentOS 7 có local YUM repo rất phù hợp với các kịch bản:

* Kiểm thử gói nội bộ
* Thiết lập lab offline
* Làm việc trong môi trường tách biệt

