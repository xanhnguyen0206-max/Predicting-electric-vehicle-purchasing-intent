# Predicting Electric Vehicle Purchase Propensity for Customer Targeting

Dự án môn học **ADY201m — AI, Data Science with Python & SQL** (FPT University).

## 1. Giới thiệu dự án
Dự án tập trung vào việc nghiên cứu và khai thác dữ liệu từ cuộc thi **Kaggle Playground Series (Season 6, Episode 9)** nhằm mô hình hóa và dự báo xác suất khách hàng quan tâm / có xu hướng mua xe điện (`Will_Buy_EV`). Kết quả này đóng vai trò hỗ trợ các đại lý ô tô trong việc nhận diện và phân tầng khách hàng mục tiêu, tối ưu hóa ngân sách tiếp thị và nâng cao hiệu quả tư vấn chuyên sâu.

## 2. Cấu trúc thư mục
```text
├── ADY201m_On_Going_1_Working.ipynb  # Notebook làm việc chính thức (Hoàn thiện 4 Part, 14 Code cells đã chạy tuần tự)
├── ADY201m_On Going 1.ipynb          # Notebook gốc do giảng viên cung cấp (SHA256 verified)
├── PROJECT_LANGUAGE_RULES.md         # Quy chuẩn ngôn ngữ học thuật của dự án
├── Prompt cho AI khác hiểu ngữ cảnh  # Tài liệu ngữ cảnh tổng quan, quyết định kỹ thuật và quy tắc làm việc
│
├── dataset/                          # Thư mục dữ liệu thô gốc từ Kaggle
│   ├── train.csv                     # 668.665 dòng, 15 cột (có nhãn Will_Buy_EV)
│   ├── test.csv                      # 286.571 dòng, 14 cột (tập kiểm thử ngoài mẫu, không có nhãn)
│   └── sample_submission.csv         # 286.571 dòng, 2 cột (cấu trúc mẫu nộp Kaggle)
│
└── prepared_data/                    # Thư mục dữ liệu đã tiền xử lý & tạo đặc trưng
    ├── train_prepared.csv            # 668.665 dòng, 18 cột (0 missing, 0 duplicate)
    └── test_prepared.csv             # 286.571 dòng, 17 cột (0 missing, 0 duplicate)
```

## 3. Nội dung thực hiện trong Ongoing 1
- **Part 1: Business Understanding:** Xác định bối cảnh kinh doanh, 3 câu hỏi nghiệp vụ cốt lõi, phát biểu bài toán Phân loại nhị phân (Binary Classification) và chiến lược đánh giá (F1-score, PR-AUC, ROC-AUC, Confusion Matrix).
- **Part 2: Data Collection & Initial Inspection:** Thu thập dữ liệu từ Kaggle s6e9, xây dựng Data Dictionary cho 15 thuộc tính, kiểm tra cấu trúc, xác nhận 0% missing values và 0 duplicate rows, phân tích mất cân bằng nhãn mục tiêu (82,54% No vs. 17,46% Yes).
- **Part 3: Data Cleaning & Preprocessing (10 bước bắt buộc):**
  1. *Importing Datasets* & kiểm tra trùng lặp
  2. *Handling Missing Values* (xác minh dữ liệu hoàn thiện 100%)
  3. *Data Formatting* (chuẩn hóa chuỗi văn bản, đồng nhất schema)
  4. *Data Normalization* (khảo sát biên độ, thiết kế pipeline chống rò rỉ dữ liệu)
  5. *Data Binning* (khảo sát phân khúc, bảo tồn biến liên tục gốc)
  6. *Categorical Encoding* (cấu hình ColumnTransformer với OneHotEncoder & OrdinalEncoder)
  7. *Outliers & Noise Handling* (kiểm tra 3.678 điểm thu nhập vượt IQR, bảo tồn nguyên vẹn ngoại lệ kinh tế)
  8. *Data Integration* (kiểm tra kiến trúc dữ liệu đơn bảng)
  9. *Feature Engineering* (xây dựng 3 đặc trưng: `Total_Charging_Access`, `Charging_Commute_Ratio`, `Income_Per_Car`)
  10. *Exporting Prepared Data* (xuất bản `train_prepared.csv` và `test_prepared.csv`)
- **Part 4: Conclusion & Next Steps:** Đánh giá mức độ sẵn sàng của Prepared Data, phân tích các giới hạn (target imbalance, tính chất quan sát) và thiết lập lộ trình 7 bước chi tiết cho Ongoing 2.

## 4. Hướng dẫn chạy Notebook
1. Môi trường: Python 3.10+ với các thư viện: `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`.
2. Mở file `ADY201m_On_Going_1_Working.ipynb`.
3. Chọn `Kernel` -> `Restart Kernel and Run All Cells`. Toàn bộ 14 code cells sẽ thực thi tuần tự từ `[1]` đến `[14]` và hoàn tất trong khoảng 1–2 phút.
