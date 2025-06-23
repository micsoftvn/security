---
description: >-
  Firecracker là một Virtual Machine Monitor (VMM) mã nguồn mở do Amazon Web
  Services phát triển. Nó được thiết kế để chạy hàng ngàn máy ảo siêu nhẹ
  (microVM)
---

# Cài Đặt Firecracker v1.12.0 Trên Ubuntu

**Firecracker** là một Virtual Machine Monitor (VMM) mã nguồn mở do Amazon Web Services phát triển. Nó được thiết kế để chạy hàng ngàn máy ảo siêu nhẹ (microVM) một cách nhanh chóng và an toàn, đặc biệt phù hợp với các hệ thống serverless như AWS Lambda hoặc Fargate.

Dưới đây là bài blog hướng dẫn cài đặt **Firecracker v1.12.0** trên Ubuntu bằng file `.tar.gz`, phù hợp để bạn lưu trữ hoặc chia sẻ:

***

### 🧰 Yêu Cầu Hệ Thống

* Ubuntu 20.04 / 22.04 (64-bit)
* CPU hỗ trợ **KVM** (Intel VT-x hoặc AMD-V)
* Kernel hỗ trợ `/dev/kvm`

***

### 1. 🔍 Kiểm Tra KVM và CPU Ảo Hóa

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo  # >0 là OK
ls /dev/kvm                         # tồn tại là OK
```

Nếu không có `/dev/kvm`, thử nạp module:

```bash
sudo modprobe kvm
```

***

### 2. 📦 Tải Và Giải Nén Firecracker v1.12.0

```bash
mkdir -p ~/firecracker-v1.12.0 && cd ~/firecracker-v1.12.0

wget https://github.com/firecracker-microvm/firecracker/releases/download/v1.12.0/firecracker-v1.12.0-x86_64.tgz

tar -xvzf firecracker-v1.12.0-x86_64.tgz
```

Sau khi giải nén, sẽ có các file như:

* `firecracker-v1.12.0-x86_64`
* `jailer-v1.12.0-x86_64`
* `sha256sum`
* `release-ID`, `LICENSE`, `NOTICE`

***

### 3. ⚙️ Cấu Hình Firecracker Binary

```bash
chmod +x firecracker-v1.12.0-x86_64 jailer-v1.12.0-x86_64
ln -s firecracker-v1.12.0-x86_64 firecracker
ln -s jailer-v1.12.0-x86_64 jailer
```

Xác nhận:

```bash
./firecracker --version
# → Firecracker v1.12.0
```

***

### 4. 📥 Tải Kernel Và RootFS Mẫu

```bash
wget https://s3.amazonaws.com/spec.ccfc.min/img/hello/kernel/hello-vmlinux.bin -O vmlinux.bin
wget https://s3.amazonaws.com/spec.ccfc.min/img/hello/fsfiles/hello-rootfs.ext4 -O rootfs.ext4
```

***

### 5. 🚀 Khởi Chạy Firecracker microVM

#### 5.1. Mở Socket Firecracker

```bash
sudo ./firecracker --api-sock /tmp/firecracker.socket
```

#### 5.2. Cấu Hình VM Qua API (Terminal khác)

```bash
# Cấu hình máy ảo
curl --unix-socket /tmp/firecracker.socket -i \
    -X PUT 'http://localhost/machine-config' \
    -H 'Content-Type: application/json' \
    -d '{
  "vcpu_count": 1,
  "mem_size_mib": 128,
  "smt": false
}'

# Gán kernel boot
curl --unix-socket /tmp/firecracker.socket -i \
    -X PUT 'http://localhost/boot-source' \
    -H 'Content-Type: application/json' \
    -d '{
        "kernel_image_path": "vmlinux.bin",
        "boot_args": "console=ttyS0 reboot=k panic=1 pci=off"
    }'

# Gán root filesystem
curl --unix-socket /tmp/firecracker.socket -i \
    -X PUT 'http://localhost/drives/rootfs' \
    -H 'Content-Type: application/json' \
    -d '{
        "drive_id": "rootfs",
        "path_on_host": "rootfs.ext4",
        "is_root_device": true,
        "is_read_only": false
    }'
```

#### 5.3. Khởi động microVM

```bash
curl --unix-socket /tmp/firecracker.socket -i \
    -X PUT 'http://localhost/actions' \
    -H 'Content-Type: application/json' \
    -d '{
        "action_type": "InstanceStart"
    }'
```

Lúc này bạn sẽ thấy console máy ảo chạy ngay trong terminal Firecracker



***

### 📚 Tài Liệu Tham Khảo

* GitHub: [https://github.com/firecracker-microvm/firecracker](https://github.com/firecracker-microvm/firecracker)
* Docs chính thức: [https://firecracker-microvm.github.io](https://firecracker-microvm.github.io)
