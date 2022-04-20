# Cấu hình CLI kết nối đến AWS

## 1. Get Access ID và KEY

Đăng nhập vào AWS - > IAM -> Users

Chọn User -> Tab Security Credential - > cần tạo kéo tới Access keys, click chọn Create Access Key.

![](<../../.gitbook/assets/image (3).png>)

Sau khi tạo song bạn sẽ có 1 Access key ID và một Private key dùng cho việc kết nối qua AWS CLI. Lưu ý việc file Key của AWS rất quan trọng nên bạn cần lưu trữ tốt key tránh làm mất file CVS hoặc Key.

### 2. Cấu hình AWS CLi

Để cấu hình sử dụng AWS Cli bạn có 2 cách dùng cho việc cấu hình, ở đây mình sẽ hướng dẫn 2 cách đơn giản nhất.

**Cách 1: Cấu hình qua dòng lệnh**

```
aws configure
AWS Access Key ID [None]: ***************
AWS Secret Access Key [None]: ***********
Default region name [None]: us-west-2
Default output format [None]: json
```

Default output format : Kiểu dữ liệu show ra khi get lệnh

**Cách 2: Cấu hình bằng cách import file CSV**

```
aws configure import –csv file://credentials.csv
```

&#x20;Để kiểm tra bạn đã cấu hình thành công chưa

```
aws configure list
```

Hoặc để cấu hình nhanh hơn như thường lệ làm trên Linux hay Mac các bạn có thể cấu hình thẳng trong file config như sau:

**`~/.aws/credentials`**

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

**`~/.aws/config`**

```
[default]
region=us-west-2
output=json
```
