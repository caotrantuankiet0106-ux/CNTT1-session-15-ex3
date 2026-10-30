# Kiến Trúc Đồng Bộ Hybrid Workflow (README)

## 1. Báo cáo phân tích I/O (Luồng `Ctrl + S`)
1. **Input:** Nhấn `Ctrl + S` $\rightarrow$ Lưu file (vd: `src/App.js`) vào **SSD Local**.
2. **File Watcher:** Tiến trình Cloud phát hiện thay đổi tệp.
3. **Exclusion Filter:** Bỏ qua `node_modules` (dựa trên `.gitignore` hoặc cấu hình Ignore của Cloud); các file code hợp lệ được đưa vào hàng đợi.
4. **Network Upload:** Gói dữ liệu code được đẩy lên Cloud qua giao thức HTTPS.
5. **Output:** Phản hồi thành công $\rightarrow$ Biểu tượng chuyển sang **Tích xanh (Sync Complete)**.

---

## 2. Xử lý bẫy `node_modules` & Hybrid Workflow
* **Vấn đề:** `node_modules` chứa hàng chục nghìn file nhỏ làm nghẽn (freeze) Cloud Sync và tràn dung lượng.
* **Giải pháp:**
  * **Loại trừ (Exclude):** Cấu hình chặn đồng bộ `node_modules` lên Cloud; quản lý mã nguồn chính qua Git (`.gitignore`).
  * **Tái tạo (Restore):** Chỉ lưu `package.json` trên Cloud. Chạy lệnh `npm install` để tái tạo `node_modules` tại máy Local.
  * **Files On-Demand:** Bật `Always Keep on This Device` cho dự án đang chạy và `Free Up Space` cho tài liệu/assets cũ.

---

## 3. Sơ đồ tổ chức thư mục Cloud chuẩn
```text
[ Cloud Workspace Root ]
├── 01_Code/                          # Source code dự án (Active: Always Keep on Local)
│   └── Startup_Project/              # Chứa package.json, src/ | [EXCLUDED: node_modules]
├── 02_Docs/                          # Tài liệu & quy trình (Files On-Demand)
└── 03_Assets/                        # Tài nguyên thiết kế, hình ảnh (Files On-Demand)