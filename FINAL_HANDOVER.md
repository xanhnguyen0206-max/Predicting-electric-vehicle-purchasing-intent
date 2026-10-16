# TÀI LIỆU HƯỚNG DẪN BÀN GIAO & NỘP BÀI ONGOING 1 (FINAL HANDOVER)
**Môn học:** ADY201m — AI, Data Science with Python & SQL  
**Đơn vị:** FPT University  
**Dự án:** Predicting Electric Vehicle Purchase Propensity for Customer Targeting  
**Giai đoạn:** Ongoing Assessment 1 (Business Understanding, Data Collection & Data Cleaning)  
**Phiên bản:** 1.1 (Release Candidate - Streamlined Part 3 into 6 Core Code Cells)  
**Ngày cập nhật:** 2026-10-16  

---

### 1. Mục tiêu dự án
Dự án nhằm nghiên cứu và xây dựng quy trình phân tích dữ liệu phục vụ bài toán dự báo xu hướng khách hàng quan tâm đến xe điện (EV) dựa trên bộ dữ liệu quan sát từ Kaggle Playground Series s6e9. Trong giai đoạn **Ongoing Assessment 1**, trọng tâm nghiên cứu hoàn toàn tập trung vào việc hiểu bối cảnh nghiệp vụ, kiểm tra cấu trúc dữ liệu thô, thực hiện chuẩn hóa - làm sạch dữ liệu theo đúng 10 bước chuẩn mực của môn học và xuất bản tập dữ liệu chuẩn bị (**Prepared Data**) đạt chuẩn học thuật.

---

### 2. Danh mục các file trong workspace & Vai trò
Cấu trúc workspace bàn giao được thiết kế tối giản, khoa học và dễ dàng di chuyển sang môi trường máy tính khác:

```text
Ongoing 1 rework/
│
├── ADY201m_On Going 1.ipynb          # Notebook gốc do giảng viên cung cấp (Read-only, SHA256: 475e90f9ab7d9cbd7e1195503cdeea28ccaf0ef9860113c4c624086840edb503)
├── ADY201m_On_Going_1_Working.ipynb  # Notebook làm việc chính thức (Hoàn thiện 4 Part, Part 3 tinh gọn 6 Code cells theo Data Preparation flow, 10 Code cells toàn notebook đã chạy tuần tự [1]->[10], 40 cells tổng cộng)
├── PROJECT_LANGUAGE_RULES.md         # Quy định chuẩn ngôn ngữ dự án (Tiếng Việt học thuật cho câu trả lời sinh viên; giữ nguyên template tiếng Anh)
├── FINAL_HANDOVER.md                 # Tài liệu này (Hướng dẫn bàn giao, kiểm định & nộp bài)
│
├── dataset/                          # Thư mục dữ liệu thô gốc (Giữ nguyên vẹn 100%)
│   ├── train.csv                     # 668.665 dòng, 15 cột (chứa nhãn Will_Buy_EV)
│   ├── test.csv                      # 286.571 dòng, 14 cột (tập kiểm thử ngoài mẫu, không có nhãn)
│   └── sample_submission.csv         # 286.571 dòng, 2 cột (cấu trúc mẫu định dạng nộp Kaggle)
│
└── prepared_data/                    # Thư mục dữ liệu đã tiền xử lý & tạo đặc trưng
    ├── train_prepared.csv            # 668.665 dòng, 18 cột (chứa Will_Buy_EV, 0 missing, 0 duplicate)
    └── test_prepared.csv             # 286.571 dòng, 17 cột (không có Will_Buy_EV, 0 missing, 0 duplicate)
```

---

### 3. Hướng dẫn mở và kiểm tra Notebook
1. **Môi trường yêu cầu:** Python 3.10 trở lên, cài đặt các thư viện tiêu chuẩn: `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`.
2. **Khởi chạy:** Mở workspace bằng VS Code, Jupyter Lab hoặc Jupyter Notebook tại thư mục gốc `Ongoing 1 rework`.
3. **Mở file notebook chính thức:** Chọn file `ADY201m_On_Going_1_Working.ipynb`.
4. **Đường dẫn dữ liệu:** Notebook được thiết lập toàn bộ bằng đường dẫn tương đối (`dataset/...` và `prepared_data/...`), đảm bảo có thể chạy ngay mà không cần cấu hình lại ổ đĩa.
5. **Cách chạy lại từ đầu:** Chọn menu `Kernel` -> `Restart Kernel and Run All Cells`. Toàn bộ 10 code cells sẽ thực thi tuần tự từ `[1]` đến `[10]` và hoàn tất trong khoảng 1–2 phút.

---

### 4. Checklist thông tin nhóm cần điền trước khi nộp (Tại Cell 0)
Trước khi xuất file hoặc nộp bài chính thức, nhóm sinh viên cần mở **Cell 0** của `ADY201m_On_Going_1_Working.ipynb` và điền chính xác các mục thông tin trong ngoặc vuông:
- [ ] **Class Code:** Điền mã lớp học phần (ví dụ: `AI1801` hoặc `SE1801`).
- [ ] **Group Name/ID:** Điền tên hoặc số thứ tự nhóm (ví dụ: `Group 01`).
- [ ] **Member List:** Cập nhật danh sách tất cả thành viên trong nhóm:
  + `1. [Họ và tên thành viên 1 - MSSV - Vai trò (Nhóm trưởng)]`
  + `2. [Họ và tên thành viên 2 - MSSV - Vai trò (Thành viên)]`
  + `3. [Họ và tên thành viên 3 - MSSV - Vai trò (Thành viên)]`
- [ ] **Submission Date:** Điền ngày nộp bài thực tế theo định dạng `DD/MM/YYYY`.

---

### 5. Phạm vi kỹ thuật & Giới hạn của Ongoing 1
Nhóm cần lưu ý các ranh giới phạm vi học thuật đã được quy định nghiêm ngặt:
- **Tuyệt đối CHƯA thực hiện trong Ongoing 1:**
  1. Chưa huấn luyện bất kỳ mô hình Machine Learning nào (No Model Training).
  2. Chưa đánh giá hay so sánh độ chính xác mô hình (No Model Evaluation).
  3. Chưa tạo file `submission.csv` và không thực hiện thao tác nộp bài lên Kaggle.
- **Lý do khoa học:** Giai đoạn Ongoing 1 chỉ đánh giá năng lực thấu hiểu bài toán nghiệp vụ, kiểm tra cấu trúc dữ liệu và thực hiện làm sạch / tiền xử lý dữ liệu chuẩn mực. Việc huấn luyện mô hình sớm trên toàn bộ dữ liệu trước khi thiết lập Cross-Validation sẽ vi phạm nguyên tắc chống rò rỉ dữ liệu (Data Leakage).

---

### 6. Hướng dẫn chuyển tiếp sang Ongoing 2
Khi bước sang Giai đoạn Ongoing Assessment 2, nhóm sẽ sử dụng hai file trong `prepared_data/` và tiếp tục theo lộ trình:
1. **Xác lập ma trận đặc trưng:** Đọc `train_prepared.csv`, tách riêng $X$ (17 cột đặc trưng) và $y$ (`Will_Buy_EV`). Loại trừ cột `id` khỏi ma trận đầu vào mô hình.
2. **Chiến lược phân tầng:** Áp dụng `StratifiedKFold` (ví dụ 5 folds) để bảo toàn tỷ lệ nhãn ~17,46% lớp `"Yes"`.
3. **Đường ống Preprocessing:** Gọi `ColumnTransformer` đã thiết kế sẵn ở Cell 3 (Giai đoạn 3 / Bước 4 & 6) Part 3, thực hiện `fit_transform` chỉ trên training fold và `transform` sang validation fold.
4. **Mô hình hóa:** Huấn luyện Baseline (Logistic Regression) và thử nghiệm các mô hình phân loại (Random Forest, LightGBM, XGBoost, CatBoost).
5. **Tối ưu hóa ngưỡng quyết định:** Sử dụng PR-AUC, F1-score và hiệu chỉnh decision threshold theo bài toán chi phí nghiệp vụ của đại lý ô tô.
