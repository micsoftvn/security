# Cấu hình Config K8s

Để cấu hình `kubectl` kết nối với cluster qua Rancher (hoặc bất kỳ cluster Kubernetes nào), bạn cần chuẩn bị và thực hiện các bước sau:

***

### ✅ **Bước 1: Lấy file `kubeconfig` từ Rancher**

1. Truy cập vào giao diện web Rancher:\
   👉 `https://rancher.demo.com.vn` hoặc địa chỉ Rancher bạn đang dùng.
2. Vào **Cluster** bạn muốn thao tác.
3. Ở góc phải, nhấn **⋮ (biểu tượng 3 chấm)** > **Kubeconfig File** > Tải về file `kubeconfig.yaml`.

***

### ✅ **Bước 2: Cấu hình `kubectl` sử dụng kubeconfig**

#### ✅ Cách 1: Tạm thời trong terminal (chỉ có hiệu lực trong phiên làm việc):

```bash
export KUBECONFIG=~/Downloads/kubeconfig.yaml
```

> ⚠️ Thay `~/Downloads/kubeconfig.yaml` bằng đúng đường dẫn file bạn vừa tải về.

#### ✅ Cách 2: Gán mặc định vĩnh viễn:

Bạn có thể **copy** file này thành file mặc định tại:

```bash
mkdir -p ~/.kube
cp ~/Downloads/kubeconfig.yaml ~/.kube/config
```

***

### ✅ **Bước 3: Kiểm tra kết nối**

```bash
kubectl get nodes
```

* Nếu trả về danh sách node: ✅ kết nối thành công.
* Nếu báo lỗi `Forbidden` hoặc `timeout`, bạn cần kiểm tra:
  * Đã kết nối **VPN** chưa (nếu cụm ở môi trường nội bộ).
  * Bạn có đủ quyền trong cluster không.
  * DNS có phân giải được không (dùng `ping` test tên miền trong kubeconfig).

***

### 🧪 Kiểm tra tên cụm và ngữ cảnh

Xem ngữ cảnh hiện tại:

```bash
kubectl config get-contexts
kubectl config current-context
```

Chuyển context nếu cần:

```bash
kubectl config use-context <context-name>
```

***

### ✅ Gợi ý xử lý nếu lỗi DNS (`no such host`)

Nếu file `kubeconfig` trỏ về địa chỉ như `rancher.demo.com.vn` và bạn bị lỗi `no such host`:

*   Kiểm tra DNS bằng:

    ```bash
    nslookup rancher.demo.com.vn
    ```

***

### ✅ Tóm tắt nhanh

| Hành động                 | Lệnh / Gợi ý                  |
| ------------------------- | ----------------------------- |
| Tải kubeconfig từ Rancher | GUI Rancher                   |
| Sử dụng tạm kubeconfig    | `export KUBECONFIG=...`       |
| Dùng kubeconfig mặc định  | Copy vào `~/.kube/config`     |
| Kiểm tra kết nối          | `kubectl get nodes`           |
| Kiểm tra context          | `kubectl config get-contexts` |

