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
192.168.1.1
192.168.1.2
192.168.1.3
192.168.1.4
192.168.1.10
192.168.1.11
192.168.2.100
192.168.2.101
192.168.2.102
192.168.2.103
192.168.2.200
```

***

### 🧠 Kết quả sau khi tối ưu subnet:

| `192.168.1.1/32` | 192.168.1.1 |
| ---------------- | ----------- |

| `192.168.1.2/31` | 192.168.1.2 – 192.168.1.3 |
| ---------------- | ------------------------- |

| `192.168.1.4/32` | 192.168.1.4 |
| ---------------- | ----------- |

| `192.168.1.10/31` | 192.168.1.10 – 192.168.1.11 |
| ----------------- | --------------------------- |

| `192.168.2.100/30` | 192.168.2.100 – 192.168.2.10 |
| ------------------ | ---------------------------- |

| `192.168.2.200/32` | 192.168.2.200 |
| ------------------ | ------------- |

### 🛠 Code Python tự động gom subnet

```python
from netaddr import IPAddress, IPSet, cidr_merge

ip_list = [
    '192.168.1.1',
    '192.168.1.2',
    '192.168.1.3',
    '192.168.1.4',
    '192.168.1.10',
    '192.168.1.11',
    '192.168.2.100',
    '192.168.2.101',
    '192.168.2.102',
    '192.168.2.103',
    '192.168.2.200'
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
* Nhập nhanh security group vào AWS/GCP/Azure
* Giảm tải khi audit hệ thống

***

