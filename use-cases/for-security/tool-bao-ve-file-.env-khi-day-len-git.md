---
description: >-
  Tool dùng cho mục đích mã hóa file .env có thể đồng bộ lên gits file
  .env.vault  mà không sợ bị lộ thông tin
---

# Tool bảo vệ file .env khi đẩy lên git

```
#!/bin/bash

# Màu sắc giao diện cho đẹp và dễ nhìn
GREEN='\033[0;32m'
BLUE='\033[0;34m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m' # No Color

# File cấu hình mẫu
ENV_FILE=".env"
EXAMPLE_FILE=".env.example"
VAULT_FILE=".env.vault"
KEYS_FILE=".env.keys"

# Hàm hiển thị tiêu đề
show_header() {
    clear
    echo -e "${BLUE}==================================================${NC}"
    echo -e "${BLUE}     HỆ THỐNG QUẢN LÝ & BACKUP BIẾN MÔI TRƯỜNG     ${NC}"
    echo -e "${BLUE}==================================================${NC}"
}

# Hàm tạm dừng để xem kết quả
pause() {
    echo ""
    read -p "Nhấn [Enter] để quay lại Menu..."
}

# 1. Khởi tạo cấu hình ban đầu
init_project() {
    show_header
    echo -e "${YELLOW}[1/3] Kiểm tra & Cấu hình .gitignore...${NC}"
    
    # Tạo .gitignore nếu chưa có
    if [ ! -f ".gitignore" ]; then
        touch .gitignore
    fi

    # Thêm .env và .env.keys vào .gitignore nếu chưa có
    for file in "$ENV_FILE" "$KEYS_FILE"; do
        if ! grep -q "^$file$" .gitignore; then
            echo "$file" >> .gitignore
            echo -e "${GREEN}• Đã thêm $file vào .gitignore${NC}"
        else
            echo -e "• $file đã có trong .gitignore từ trước."
        fi
    done

    echo -e "\n${YELLOW}[2/3] Tạo file .env.example tự động...${NC}"
    if [ -f "$ENV_FILE" ]; then
        # Copy cấu hình nhưng xóa giá trị, giữ lại key
        awk -F= '{print $1 "="}' "$ENV_FILE" > "$EXAMPLE_FILE"
        echo -e "${GREEN}• Đã tạo/cập nhật $EXAMPLE_FILE thành công từ $ENV_FILE.${NC}"
    else
        echo -e "${RED}• Không tìm thấy file .env thô để tạo file mẫu.${NC}"
    fi

    echo -e "\n${YELLOW}[3/3] Kiểm tra môi trường Node.js...${NC}"
    if [ ! -f "package.json" ] && [ -d ".git" ]; then
        echo -e "${YELLOW}• Mẹo: Bản @dotenvx/dotenvx chạy trực tiếp qua npx không cần cài cứng vào package.json.${NC}"
    fi
    
    echo -e "\n${GREEN}==> HOÀN THÀNH KHỞI TẠO! Dự án đã sẵn sàng sử dụng dotenvx.${NC}"
    pause
}

# 2. Mã hóa file .env (Sử dụng OpenSSL AES-256-CBC — có sẵn trên mọi server)
build_vault() {
    show_header
    echo -e "${YELLOW}Đang tiến hành mã hóa file .env bằng OpenSSL AES-256...${NC}"
    if [ ! -f "$ENV_FILE" ]; then
        echo -e "${RED}Lỗi: Không tìm thấy file .env ở local để mã hóa!${NC}"
    else
        read -sp "Nhập mật khẩu mã hóa: " enc_pass
        echo ""
        read -sp "Xác nhận mật khẩu: " enc_pass2
        echo ""
        if [ "$enc_pass" != "$enc_pass2" ]; then
            echo -e "${RED}Lỗi: Mật khẩu không khớp!${NC}"
        elif [ -z "$enc_pass" ]; then
            echo -e "${RED}Lỗi: Mật khẩu không được để trống!${NC}"
        else
            openssl enc -aes-256-cbc -salt -pbkdf2 -in "$ENV_FILE" -out "$VAULT_FILE" -pass "pass:${enc_pass}"
            if [ $? -eq 0 ]; then
                echo -e "\n${GREEN}✓ Thành công! Đã mã hóa .env → ${VAULT_FILE}${NC}"
                echo -e "${YELLOW}Lưu ý: Commit file ${VAULT_FILE} lên Git để backup. KHÔNG commit .env gốc!${NC}"
            else
                echo -e "${RED}Lỗi: Quá trình mã hóa thất bại.${NC}"
            fi
        fi
    fi
    pause
}

# 3. Giải mã file .env.vault
decrypt_vault() {
    show_header
    echo -e "${YELLOW}Giải mã file ${VAULT_FILE}...${NC}"
    if [ ! -f "$VAULT_FILE" ]; then
        echo -e "${RED}Lỗi: Không tìm thấy file ${VAULT_FILE}!${NC}"
    else
        read -sp "Nhập mật khẩu giải mã: " dec_pass
        echo ""
        if [ -z "$dec_pass" ]; then
            echo -e "${RED}Lỗi: Mật khẩu không được để trống!${NC}"
        else
            openssl enc -aes-256-cbc -d -pbkdf2 -in "$VAULT_FILE" -out "$ENV_FILE" -pass "pass:${dec_pass}"
            if [ $? -eq 0 ] && [ -s "$ENV_FILE" ]; then
                echo -e "\n${GREEN}✓ Giải mã thành công! Đã tái tạo file .env${NC}"
            else
                echo -e "${RED}Lỗi: Giải mã thất bại. Sai mật khẩu hoặc file bị hỏng.${NC}"
                rm -f "$ENV_FILE"
            fi
        fi
    fi
    pause
}

# 4. Backup file .env thủ công (OpenSSL)
backup_gpg() {
    show_header
    echo -e "${YELLOW}Sao lưu .env bằng OpenSSL AES-256...${NC}"
    if [ ! -f "$ENV_FILE" ]; then
        echo -e "${RED}Lỗi: Không có file .env để backup!${NC}"
    else
        backup_name=".env.$(date +%Y%m%d_%H%M%S).enc"
        read -sp "Nhập mật khẩu bảo vệ backup: " bk_pass
        echo ""
        if [ -z "$bk_pass" ]; then
            echo -e "${RED}Lỗi: Mật khẩu không được để trống!${NC}"
        else
            openssl enc -aes-256-cbc -salt -pbkdf2 -in "$ENV_FILE" -out "$backup_name" -pass "pass:${bk_pass}"
            if [ $? -eq 0 ]; then
                echo -e "\n${GREEN}✓ Backup thành công! File: ${backup_name}${NC}"
                echo -e "${YELLOW}Lưu trữ file .enc này ở nơi an toàn.${NC}"
            else
                echo -e "${RED}Lỗi: Backup thất bại.${NC}"
            fi
        fi
    fi
    pause
}

# 5. Khôi phục file .env từ backup
restore_gpg() {
    show_header
    echo -e "${YELLOW}Khôi phục file .env từ backup...${NC}"
    
    files=(*.enc *.gpg)
    # Filter out non-existing glob patterns
    existing_files=()
    for f in "${files[@]}"; do
        [ -f "$f" ] && existing_files+=("$f")
    done

    if [ ${#existing_files[@]} -eq 0 ]; then
        echo -e "${RED}Không tìm thấy file backup (.enc/.gpg) nào!${NC}"
        pause
        return
    fi

    echo -e "${BLUE}Các file backup hiện có:${NC}"
    for i in "${!existing_files[@]}"; do
        echo -e " [$i] ${existing_files[$i]}"
    done

    read -p "Chọn số thứ tự file: " file_idx
    selected_file="${existing_files[$file_idx]}"

    if [ -f "$selected_file" ]; then
        read -sp "Nhập mật khẩu giải mã: " rs_pass
        echo ""
        openssl enc -aes-256-cbc -d -pbkdf2 -in "$selected_file" -out "$ENV_FILE" -pass "pass:${rs_pass}"
        if [ $? -eq 0 ] && [ -s "$ENV_FILE" ]; then
            echo -e "\n${GREEN}✓ Khôi phục thành công file .env!${NC}"
        else
            echo -e "${RED}Lỗi: Giải mã thất bại. Sai mật khẩu hoặc file lỗi.${NC}"
            rm -f "$ENV_FILE"
        fi
    else
        echo -e "${RED}Lựa chọn không hợp lệ!${NC}"
    fi
    pause
}

# Vòng lặp Menu chính
while true; do
    show_header
    echo -e " [1] Khởi tạo dự án (Cấu hình gitignore + .env.example)"
    echo -e " [2] Mã hóa .env (Tạo/Cập nhật .env.vault bằng OpenSSL)"
    echo -e " [3] Giải mã .env.vault (Cần nhập mật khẩu)"
    echo -e "--------------------------------------------------"
    echo -e " [4] Backup file .env (Mã hóa OpenSSL AES-256)"
    echo -e " [5] Khôi phục .env từ file backup"
    echo -e "--------------------------------------------------"
    echo -e " [0] Thoát chương trình"
    echo -e "${BLUE}==================================================${NC}"
    read -p "Vui lòng chọn một tính năng [0-5]: " choice

    case $choice in
        1) init_project ;;
        2) build_vault ;;
        3) decrypt_vault ;;
        4) backup_gpg ;;
        5) restore_gpg ;;
        0) 
            echo -e "${GREEN}Cảm ơn bạn đã sử dụng. Tạm biệt!${NC}"
            exit 0 
            ;;
        *) 
            echo -e "${RED}Lựa chọn không hợp lệ, vui lòng thử lại!${NC}"
            sleep 1
            ;;
    esac
done
```
