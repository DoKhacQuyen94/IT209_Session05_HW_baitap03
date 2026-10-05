# Bài 3: Xử lý xung đột phức tạp trong quá trình Rebase

## 1. Bối cảnh và Chuẩn bị
- Tại nhánh `main`, tạo tệp `config.json` ban đầu và commit `init config` (`beaca42`):
  ```json
  {
    "port": 8080,
    "debug": false
  }
  ```
- Tạo nhánh `feature-api` và thực hiện 2 commit:
  - `feat: change port`: sửa `"port": 9000`
  - `feat: enable debug`: sửa `"debug": true`
- Quay lại nhánh `main` và thực hiện 2 commit:
  - `update port on main`: sửa `"port": 8081`
  - `add env config`: thêm `"env": "production"`

---

## 2. Quá trình Rebase và Giải quyết xung đột từng chặng

Chuyển sang nhánh `feature-api` và tiến hành rebase:
```bash
git checkout feature-api
git rebase main
```

### Chặng 1: Xung đột tại commit `feat: change port`
- **Thông báo xung đột**:
  ```text
  Auto-merging homework/session_05/ex3/config.json
  CONFLICT (content): Merge conflict in homework/session_05/ex3/config.json
  error: could not apply 34f08f0... feat: change port
  ```
- **Nội dung xung đột trong `config.json`**:
  ```json
  {
  <<<<<<< HEAD
    "port": 8081,
    "debug": false,
    "env": "production"
  =======
    "port": 9000,
    "debug": false
  >>>>>>> 34f08f0 (feat: change port)
  }
  ```
- **Xử lý thủ công**: Cập nhật port thành `9000` và bảo toàn trường `env` của nhánh `main`:
  ```json
  {
    "port": 9000,
    "debug": false,
    "env": "production"
  }
  ```
- **Lưu và tiếp tục rebase**:
  ```bash
  git add homework/session_05/ex3/config.json
  git rebase --continue
  ```

---

### Chặng 2: Xung đột tại commit `feat: enable debug`
- **Thông báo xung đột**:
  ```text
  Auto-merging homework/session_05/ex3/config.json
  CONFLICT (content): Merge conflict in homework/session_05/ex3/config.json
  error: could not apply 13958f0... feat: enable debug
  ```
- **Nội dung xung đột trong `config.json`**:
  ```json
  {
    "port": 9000,
  <<<<<<< HEAD
    "debug": false,
    "env": "production"
  =======
    "debug": true
  >>>>>>> 13958f0 (feat: enable debug)
  }
  ```
- **Xử lý thủ công**: Cập nhật `"debug": true` và giữ nguyên cấu hình `"env": "production"`:
  ```json
  {
    "port": 9000,
    "debug": true,
    "env": "production"
  }
  ```
- **Lưu và hoàn tất rebase**:
  ```bash
  git add homework/session_05/ex3/config.json
  git rebase --continue
  ```
- **Thông báo hoàn thành**:
  ```text
  Successfully rebased and updated refs/heads/feature-api.
  ```

---

## 3. Kết quả kiểm tra

### Kiểm tra trạng thái làm việc (`git status`):
```bash
git status
```
**Kết quả hiển thị:**
```text
On branch feature-api
nothing to commit, working tree clean
```

---

### Kiểm tra lịch sử sau khi hoàn thành (`git log --graph --oneline`):
```bash
git log --graph --oneline -n 5
```
**Kết quả hiển thị:**
```text
* ed94ad7 (HEAD -> feature-api) feat: enable debug
* 51ead74 feat: change port
* ba38ab6 (main) add env config
* e976381 update port on main
* beaca42 init config
```
*(Lịch sử Git thẳng tắp, không có commit gộp phụ, các commit của `feature-api` nối tiếp ngay sau các commit mới nhất của `main`).*

---

## 4. Phân biệt giải quyết xung đột: Merge vs Rebase
- **Khi Merge**: Giải quyết xung đột tập trung một lần cho toàn bộ sự khác biệt giữa hai nhánh, tạo ra một merge commit kết nối cả hai nhánh cha.
- **Khi Rebase**: Giải quyết xung đột tuần tự theo từng commit (step-by-step), tua lại và đặt từng commit của nhánh tính năng lên trên đầu nhánh đích, giúp lịch sử commit hoàn toàn tuyến tính.
