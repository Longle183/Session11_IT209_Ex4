# Bài 4: Khai báo GitHub Secrets và cấu hình Đăng nhập Container Registry

## 1. Mục tiêu
- Tạo mã hóa an toàn (Repository Secret) lưu trữ Token cá nhân trên GitHub.
- Cấu hình bước xác thực đăng nhập tự động vào GitHub Container Registry (GHCR) trong workflow GitHub Actions.

---

## 2. Các bước thực hiện

### Bước 1: Tạo Personal Access Token (classic) trên GitHub
1. Truy cập GitHub: **Avatar góc phải trên** -> **Settings** -> cuộn xuống chọn **Developer Settings** (ở cột bên trái).
2. Chọn **Personal access tokens** -> **Tokens (classic)**.
3. Nhấp vào **Generate new token** -> **Generate new token (classic)**.
4. Đặt tên (Note): `GHCR_ACCESS_TOKEN` (hoặc tên tùy chọn).
5. Tích chọn quyền (Scopes):
   - `write:packages` (sẽ tự động tích thêm `read:packages`).
   - (Tùy chọn) `delete:packages` nếu cần xóa image sau này.
   - (Tùy chọn) `repo` nếu repo ở chế độ private.
6. Nhấp **Generate token** ở cuối trang.
7. **Sao chép và lưu trữ mã Token** (vì token này chỉ hiển thị 1 lần duy nhất).

---

### Bước 2: Khai báo Secret trong Repository
1. Truy cập vào Repository bài tập trên GitHub.
2. Vào **Settings** -> menu bên trái chọn **Secrets and variables** -> **Actions**.
3. Nhấp vào nút **New repository secret**.
4. Điền thông tin:
   - **Name**: `REGISTRY_TOKEN`
   - **Secret**: Dán mã Personal Access Token vừa copy ở Bước 1 vào đây.
5. Nhấp **Add secret**.

---

### Bước 3: Cấu hình Workflow CI (`.github/workflows/ci.yml`)
File kịch bản đã được khởi tạo tại [ci.yml](file:///c:/Users/acer/Desktop/PTIT/rikkei/Dev-ops/btvn/ss11/ex4/.github/workflows/ci.yml):

```yaml
name: CI Workflow

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]
  workflow_dispatch:

jobs:
  login-ghcr:
    name: Build & Login GHCR
    runs-on: ubuntu-latest
    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

### Bước 4: Đẩy code và kiểm tra kết quả
1. Khởi tạo git và commit/push lên GitHub:
   ```bash
   git init
   git add .
   git commit -m "feat: add ghcr login step with secret REGISTRY_TOKEN"
   git branch -M main
   git remote add origin <URL_REPOSITORY_CỦA_BẠN>
   git push -u origin main
   ```
2. Mở tab **Actions** trên GitHub repository.
3. Chọn workflow run vừa được trigger.
4. Nhấp vào job **Build & Login GHCR**, mở rộng bước **Đăng nhập vào GitHub Container Registry**.
5. Xác nhận log hiển thị:
   ```text
   Logging into ghcr.io...
   Login Succeeded
   ```

---

## 3. Sản phẩm nộp bài
1. File workflow: [.github/workflows/ci.yml](file:///c:/Users/acer/Desktop/PTIT/rikkei/Dev-ops/btvn/ss11/ex4/.github/workflows/ci.yml)
2. Ảnh chụp màn hình:
   - Mục **Repository secrets** (Settings -> Secrets and variables -> Actions) hiển thị secret `REGISTRY_TOKEN`.
   - Log chi tiết bước **Đăng nhập vào GitHub Container Registry** trong tab Actions hiển thị **Login Succeeded**.
