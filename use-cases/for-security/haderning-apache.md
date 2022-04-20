---
description: Hướng dẫn haderning cơ bản cho Apache đảm bảo an toàn cho Web Services
---

# Haderning Apache

#### 1. Xoá bỏ các Module không cần thiết

```bash
/usr/sbin/httpd -M
```

Loại bỏ các modules không cần thiết ( mod\_info, mod\_status, mod\_version, mod\_autoindex, mod\_dav ) bằng cách edit file : /etc/httpd/conf/httpd.conf

#### 2. Thay đổi các trang báo lỗi mặc định của Webservices

Add thêm các dòng bên dưới trong file : **httpd.conf**

```bash
ErrorDocument 400 /error.html
ErrorDocument 401 /error.html
ErrorDocument 402 /error.html
ErrorDocument 403 /error.html
ErrorDocument 404 /error.html
ErrorDocument 500 /error.html
ErrorDocument 501 /error.html
ErrorDocument 502 /error.html
```

#### 3. Xoá bỏ banner webservice và thông tin

Add thêm 2 dòng bên dưới trong file : **httpd.conf**

```bash
ServerSignature Off
ServerTokens Prod
```

#### 4. Giới hạn phương thức truyền lên

Thêm LimitExcept vào thêm trong thư mục Directory như bên dưới

```bash
<Directory "/var/www/web">
<LimitExcept GET POST HEAD>
Deny from all
</LimitExcept>
...
</Directory>
```

#### 5. Giới hạn không cho list file và folder trên thư mục web

Thêm Options -Indexes trong Tag Directory của web

```bash
<Directory "/var/www/web">
 Options -Indexes 
</Directory>
```

#### 6. Thiết lập run Webservices với quyền tối thiểu

Không cho phép user www-data được phép login

```bash
useradd –M www-data –s /bin/false
```

```bash
passwd –l www-data
```

#### 7. Phân quyền thư mục web

```bash
chown -R www-data: www-data /var/www 
find /var/www -type d | xargs chmod -R 755
find /var/www -type f  | xargs chmod -R 644
chown -R www-data:www-data
```

#### 8. Cho phép user www-data được phép run services

Tìm đến phần User và Group trong file **httpd.conf**

```bash
User root (Đổi thành user www-data)
Group root (Đổi thành group www-data)
```

#### 9. Thiết lập cấu hình không cho phép chạy CGI

Thêm Options –ExecCGI -Includes vào trong tag Directory chứa thư mục web

```bash
<Directory /var/www/web>
Options –ExecCGI -Includes
</Directory>
```

#### 10. Thiếp lập mã hoá Https

```bash
grep –i –r ”SSLProtocol” /etc/httpd
```

Chỉ cho phép sử dụng các giao thức SSL

Mở file : **/etc/httpd/conf.d/ssl.conf** và đổi thành như bên dưới

```bash
SSLProtocol all –SSLv2 –SSLv3
```

Thiết lập **SSLCipherSuite** như bên dưới

```bash
SSLCipherSuite          ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!3DES:!MD5:!PSK:!SSLv3:!SSLv2
```

#### 11. Thiết lập cấu hình log Apache như bên dưới

```bash
LogFormat "%h %{X-Forwarded-For}i %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" tcbs_combined
```

#### 12. Thiết lập giới hạn php trong php.ini

```bash
open_basedir = /var/www:/path/to/first/folder:/path/to/second/folder
```

Trong đó : /path/to/first/folder và /path/to/second/folder là những thư mục php được phép tác động đến

#### 13. Loại bỏ các module nguy hiểm trong php

Thiết lập được thực hiện trong file **php.ini**

```bash
disable_functions = exec,passthru,shell_exec,system,proc_open,popen,curl_exec,
curl_multi_exec,parse_ini_file,show_source, symlink
```

Check kiểm tra load module

```bash
php -m
```

* Lưu ý khi các thiết lập được cấu hình song cần restart lại dịch vụ
