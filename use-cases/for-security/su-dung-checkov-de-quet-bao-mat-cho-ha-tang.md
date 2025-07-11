# Sử dụng Checkov để quét bảo mật cho hạ tầng

Hướng dẫn **cài đặt và sử dụng Checkov** để quét bảo mật cho hạ tầng dạng mã (Infrastructure as Code – IaC), đặc biệt là **Terraform**, **Kubernetes YAML**, **CloudFormation**, v.v.

***

### 🧩 BƯỚC 1: Cài đặt Checkov

#### ✅ Cách 1: Dùng `pip` (Python >= 3.7)

```bash
pip install checkov
```

> Nếu dùng `pipx` thì có thể cài riêng môi trường:

```bash
pipx install checkov
```

#### ✅ Cách 2: Dùng Docker

Không cần cài Python:

```bash
docker run --rm -v $(pwd):/iac bridgecrew/checkov -d /iac
```

***

### 📁 BƯỚC 2: Chuẩn bị file để quét

Bạn có thể test với một file Terraform ví dụ như sau (`main.tf`):

```hcl
resource "aws_s3_bucket" "mybucket" {
  bucket = "my-unsecure-bucket"

  acl    = "public-read"  # Lỗi bảo mật!
}
```

Hoặc với Kubernetes YAML (`nginx.yaml`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
```

***

### 🚀 BƯỚC 3: Chạy Checkov để quét mã

#### 🔹 Với Terraform:

```bash
checkov -d . --framework terraform
```

#### 🔹 Với Kubernetes YAML:

```bash
checkov -d . --framework kubernetes
```

#### 🔹 Quét nhiều loại IaC:

```bash
checkov -d . --framework terraform,kubernetes,cloudformation
```

***

### 📝 BƯỚC 4: Đọc kết quả

Checkov sẽ in ra các rule phát hiện lỗi:

```
Check: CKV_AWS_20: "S3 Bucket has public read ACL."
File: /main.tf:3-7
Severity: MEDIUM

        resource "aws_s3_bucket" "mybucket" {
        +  acl    = "public-read"
        }
```

Bạn sẽ thấy:

* **Check ID** (`CKV_AWS_20`)
* **Mô tả lỗi**
* **Severity** (LOW/MEDIUM/HIGH/CRITICAL)
* **Dòng mã cụ thể**

***

### 📤 BƯỚC 5: Xuất kết quả (tuỳ chọn)

Checkov hỗ trợ nhiều định dạng output:

| Format       | Flag sử dụng         |
| ------------ | -------------------- |
| JSON         | `--output json`      |
| JUnit XML    | `--output junitxml`  |
| GitHub SARIF | `--output sarif`     |
| CycloneDX    | `--output cyclonedx` |

Ví dụ:

```bash
checkov -d . --output json > result.json
```

***

### ⚙️ BƯỚC 6: Một số tùy chọn hữu ích

| Option                        | Tác dụng                                      |
| ----------------------------- | --------------------------------------------- |
| `--quiet`                     | Chỉ in kết quả lỗi                            |
| `--skip-check CKV_AWS_20`     | Bỏ qua 1 check cụ thể                         |
| `--check CKV_AWS_20`          | Chạy 1 check cụ thể                           |
| `--compact`                   | Hiển thị gọn hơn                              |
| `--soft-fail`                 | Không exit code 1 nếu có lỗi (hữu ích cho CI) |
| `--config-file .checkov.yaml` | Cấu hình checkov qua file                     |

***

### 🔧 BƯỚC 7: Tạo rule tùy chỉnh (advanced)

Tạo file YAML như sau để viết policy riêng:

```yaml
metadata:
  name: "S3 Bucket must not be public"
  id: "CUSTOM_AWS_1"
  category: "Security"
definition:
  cond_type: attribute
  resource_types:
    - aws_s3_bucket
  attribute: acl
  operator: not_equals
  value: "public-read"
```

Sau đó chạy:

```bash
checkov -d . --external-checks-dir ./custom-rules
```

***

