---
description: >-
  Trong quá trình cấu hình firewall, proxy, WAF (ModSecurity), hay security
  group trên các hệ thống cloud/on-premise, việc phải liệt kê từng địa chỉ IP
  riêng biệt là cực kỳ tốn thời gian và dễ nhầm lẫn.
---

# 🔧 Gom Nhóm IP Thành Subnet CIDR Tối Ưu – Tiết Kiệm Công Sức Quản Lý Hệ Thống!

🎯 Mục tiêu

Tự động gom các IP rời rạc thành **các subnet nhỏ nhất có thể** sử dụng CIDR notation – giúp:

* Giảm dòng cấu hình cần gõ
* Tối ưu rule firewall
* Dễ kiểm soát, bảo trì về sau

***

### 💡 Ví dụ đầu vào:

Danh sách IP ban đầu:

```
10.80.4.69
10.98.8.23
10.80.4.140
10.80.4.141
10.80.4.142
10.80.4.143
10.80.4.51
10.80.4.53
10.80.4.66
10.80.4.67
10.80.4.68
```

***

### 🧠 Kết quả sau khi tối ưu subnet:

| CIDR Subnet      | Bao gồm IP                |
| ---------------- | ------------------------- |
| `10.80.4.51/32`  | 10.80.4.51                |
| `10.80.4.53/32`  | 10.80.4.53                |
| `10.80.4.66/31`  | 10.80.4.66 – 10.80.4.67   |
| `10.80.4.68/31`  | 10.80.4.68 – 10.80.4.69   |
| `10.80.4.140/30` | 10.80.4.140 – 10.80.4.143 |
| `10.98.8.23/32`  | 10.98.8.23                |

✅ Từ 11 dòng IP rời rạc, giảm còn **6 dòng subnet** dễ kiểm soát!

***

### 🛠 Code Python tự động gom subnet

```python
from netaddr import IPAddress, IPSet, cidr_merge

ip_list = [
    '10.80.4.69',
    '10.98.8.23',
    '10.80.4.140',
    '10.80.4.141',
    '10.80.4.142',
    '10.80.4.143',
    '10.80.4.51',
    '10.80.4.53',
    '10.80.4.66',
    '10.80.4.67',
    '10.80.4.68'
]

ip_set = IPSet(IPAddress(ip) for ip in ip_list)
merged_subnets = cidr_merge(ip_set.iter_cidrs())

print("Các subnet tối ưu:")
for subnet in merged_subnets:
    print(subnet)
```

***

### 📦 Cài đặt thư viện cần thiết:

```bash
pip install netaddr
```

***

### 🧩 Ứng dụng thực tế:

* Tạo whitelist IP cho NGINX, Apache, iptables
* Viết rule `SecRule REMOTE_ADDR "@ipMatch 10.80.4.140/30"` trong ModSecurity
* Nhập nhanh security group vào AWS/GCP/Azure
* Giảm tải khi audit hệ thống

***

