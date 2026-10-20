# Predicting Electric Vehicle Purchase Propensity for Customer Targeting

Dự án môn học **ADY201m — AI, Data Science with Python & SQL** (FPT University).

---

## 1. Giới thiệu dự án
Dự án tập trung vào việc nghiên cứu và khai thác dữ liệu từ cuộc thi **Kaggle Playground Series (Season 6, Episode 9)** nhằm xây dựng hệ thống mô hình hóa và dự báo xác suất khách hàng quan tâm / có xu hướng mua xe điện (`Will_Buy_EV`). 

Kết quả phân tích đóng vai trò cốt lõi giúp các đại lý ô tô và doanh nghiệp xe điện:
- Nhận diện và phân tầng khách hàng tiềm năng chính xác.
- Tối ưu hóa ngân sách tiếp thị (marketing ROI) và chi phí tiếp cận (Customer Acquisition Cost - CAC).
- Nâng cao hiệu quả tư vấn chuyên sâu qua chiến lược targeting phân khúc (Lift > 2.5x).

---

## 2. Cấu trúc dự án
Toàn bộ dự án được tổ chức khoa học theo các giai đoạn nghiên cứu (Ongoing phases):

```text
.
├── .gitignore
├── README.md                                          # Tài liệu tổng quan toàn bộ dự án
│
├── Ongoing 1/                                         # GIAI ĐOẠN 1: Business Understanding & Data Preprocessing
│   ├── ADY201m_On_Going_1_Working.ipynb              # Notebook làm việc chính thức (Hoàn thiện 4 Part, 14 Code cells)
│   ├── ADY201m_On Going 1.ipynb                      # Notebook gốc mẫu
│   ├── PROJECT_LANGUAGE_RULES.md                     # Quy chuẩn thuật ngữ & ngôn ngữ học thuật
│   ├── Prompt cho AI khác hiểu ngữ cảnh              # Bối cảnh kỹ thuật và quyết định hệ thống
│   ├── README.md                                     # Chi tiết nội dung Ongoing 1
│   ├── dataset/                                      # Dữ liệu thô gốc từ Kaggle
│   │   ├── train.csv                                 # 668.665 dòng, 15 cột
│   │   ├── test.csv                                  # 286.571 dòng, 14 cột
│   │   └── sample_submission.csv                     # Cấu trúc mẫu submission
│   └── prepared_data/                                # Dữ liệu sạch sau tiền xử lý
│       ├── train_prepared.csv                        # 668.665 dòng, 18 cột (0 missing, 0 duplicates)
│       └── test_prepared.csv                         # 286.571 dòng, 17 cột
│
├── Ongoing 2/                                         # GIAI ĐOẠN 2: EDA, Feature Engineering & Model Benchmarking
│   ├── ADY201m_On_Going_2.ipynb                      # Notebook chính thức Ongoing 2 (28 cells, chạy hoàn tất 100%)
│   ├── ADY201m_On_Going_2_Detailed_Explanation.ipynb # Notebook giải thích chi tiết từng cell, thuật ngữ, công thức, SQL & code
│   ├── code.ipynb                                    # Notebook tham chiếu thực hành của giảng viên
│   ├── ONGOING_2_MASTER_BLUEPRINT.md                 # Tài liệu đặc tả luồng kỹ thuật & kết quả phân tích
│   └── ONGOING_2_MASTER_PIPELINE.md                  # Hướng dẫn chi tiết từng bước theo rubric môn học
│
└── Ongoing 3/                                         # GIAI ĐOẠN 3: Deployment & Executive Presentation
    └── README.md                                     # Placeholder chuẩn bị cho giai đoạn bàn giao tiếp theo
```

---

## 3. Tổng kết kỹ thuật & Kết quả Ongoing 2

### 3.1. Pipeline 8 bước chuẩn học thuật
1. **Bước 1 — Business Understanding & Workflow Integration**: Định hình mục tiêu kinh doanh, xác định bài toán phân loại nhị phân trên tập dữ liệu mất cân bằng (17.46% Positive), phân tích chi phí sai lầm (False Positive vs. False Negative).
2. **Bước 2 — Exploratory Data Analysis (EDA) với DuckDB SQL & Visual Analytics**: Khảo sát phân phối đơn biến, phân tích hai biến (bivariate), tương quan giữa hạ tầng sạc, thu nhập và xu hướng mua xe điện.
3. **Bước 3 — Multicollinearity Analysis (VIF & Pearson Correlation)**: Kiểm định hiện tượng đa cộng tuyến bằng chỉ số VIF (Variance Inflation Factor), loại bỏ rủi ro bất ổn định trọng số mô hình.
4. **Bước 4 — Feature Engineering & Preprocessing**: Tạo các đặc trưng tương tác phi tuyến (`Total_Charging_Access`, `Charging_Commute_Ratio`, `Income_Per_Car`) và đóng gói qua `ColumnTransformer` (StandardScaler, OneHotEncoder, OrdinalEncoder) chống rò rỉ dữ liệu (data leakage).
5. **Bước 5 — Cross-Validation Setup (Stratified 5-Fold)**: Thiết lập phân tầng 5-fold cross-validation bảo đảm tỷ lệ nhãn mục tiêu đồng nhất trên mọi fold.
6. **Bước 6 — Model Training & Benchmarking**: Huấn luyện và đánh giá công bằng 5 thuật toán Machine Learning:
   - Logistic Regression (Baseline tuyến tính)
   - Decision Tree (Cây đơn phân loại)
   - Random Forest (Bagging ensemble)
   - LightGBM (Gradient boosting theo lá)
   - XGBoost (Gradient boosting tối ưu hóa chính quy)
7. **Bước 7 — Model Evaluation & Business Value Analysis**: So sánh ROC-AUC, PR-AUC, F1-Score, vẽ ROC Curve, PR Curve, Confusion Matrix và phân tích đường cong Cumulative Gains / Lift Curve để chứng minh giá trị kinh doanh.
8. **Bước 8 — Conclusion & Business Recommendations**: Đúc kết insight từ Feature Importance, đề xuất 3 chiến lược định hướng hành động cho đại lý ô tô và kế hoạch bàn giao sang Ongoing 3.

### 3.2. Bảng so sánh hiệu năng các mô hình (Out-of-Fold Evaluation)

| Thuật toán | ROC-AUC | PR-AUC | F1-Score | Độ ổn định (Std Fold) | Đánh giá nghiệp vụ |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **XGBoost** | **0.8166** | **0.5480** | **0.5367** | $\pm 0.0018$ | **Tốt nhất toàn diện**, phân tách phân khúc sắc nét nhất |
| **LightGBM** | 0.8152 | 0.5434 | 0.5323 | $\pm 0.0019$ | Tương đương XGBoost, tốc độ huấn luyện nhanh |
| **Random Forest** | 0.8099 | 0.5372 | 0.5280 | $\pm 0.0022$ | Ổn định cao, chi phí tính toán lớn |
| **Logistic Regression** | 0.7938 | 0.4902 | 0.4907 | $\pm 0.0021$ | Baseline tuyến tính chuẩn xác |
| **Decision Tree** | 0.6970 | 0.3650 | 0.4728 | $\pm 0.0034$ | Dễ bị quá khớp (overfitting), hiệu năng thấp nhất |

> **Business Lift Highlight:** Khi áp dụng mô hình XGBoost để nhắm mục tiêu vào 10% khách hàng có xác suất dự báo cao nhất (Top Decile), doanh nghiệp tiếp cận được **44.8%** tổng số khách hàng thực sự có nhu cầu mua xe điện — đạt hệ số **Lift = 2.57x** so với chiến lược tiếp cận ngẫu nhiên.

---

## 4. Hướng dẫn cài đặt & Thực thi

### 4.1. Môi trường yêu cầu
- **Python**: phiên bản 3.10 trở lên.
- **Thư viện chính**:
  ```bash
  pip install numpy pandas scikit-learn matplotlib seaborn xgboost lightgbm duckdb statsmodels
  ```

### 4.2. Chạy Notebook
1. **Ongoing 1**: Mở `Ongoing 1/ADY201m_On_Going_1_Working.ipynb` và chọn `Restart & Run All`.
2. **Ongoing 2**: Mở `Ongoing 2/ADY201m_On_Going_2.ipynb` và chọn `Restart & Run All`. Toàn bộ 28 cells thực thi tuần tự và hoàn tất trong khoảng 2–3 phút.
3. **Tài liệu phân tích chuyên sâu**: Mở `Ongoing 2/ADY201m_On_Going_2_Detailed_Explanation.ipynb` để tham khảo chi tiết giải thích cho từng dòng code, thuật toán và công thức toán học.

---

## 5. Bản quyền & Thông tin tác giả
- **Môn học**: ADY201m — AI, Data Science with Python & SQL
- **Đơn vị**: Đại học FPT (FPT University)
- **Tác giả**: Nguyễn Thế Anh (`xanhnguyen0206@gmail.com`)
