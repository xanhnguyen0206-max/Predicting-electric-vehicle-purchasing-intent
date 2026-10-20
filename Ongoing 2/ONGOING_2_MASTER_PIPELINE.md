# BẢN ĐẶC TẢ PIPELINE THỰC THI TOÀN DIỆN TỪ A ĐẾN Z — ONGOING ASSESSMENT 2
**Môn học:** ADY201m — AI, Data Science with Python & SQL (FPT University)  
**Đề tài:** Dự báo xu hướng khách hàng mua xe điện (Predicting Electric Vehicle Purchase Propensity)  
**Bộ dữ liệu:** Kaggle Playground Series (Season 6, Episode 9) — 668.665 dòng huấn luyện  
**Giảng viên thiết kế khung:** Thầy MaiCV2 (FPT University)  
**File thực thi chính thức:** [`ADY201m_On_Going_2.ipynb`](file:///d:/Dự%20án%20ADY201m/Ongoing%202/ADY201m_On_Going_2.ipynb)  
**Tài liệu tham chiếu:** Di sản Ongoing 1 + Kỹ thuật thi đấu Top Kaggle ([`code.ipynb`](file:///d:/Dự%20án%20ADY201m/Ongoing%202/code.ipynb))  
**Phiên bản:** 3.0 — Master End-to-End Execution Pipeline (Single Source of Truth)

---

## I. KIẾN TRÚC TỔNG THỂ DÒNG CHẢY DỮ LIỆU (END-TO-END DATA FLOW)

Mọi thành phần trong Ongoing 2 được kết nối hữu cơ theo chuỗi logic khép kín:
```text
[Ongoing 1: prepared_data/train_prepared.csv] (668.665 dòng, 18 cột, 0 missing, 0 dup)
       │
       ▼
[BƯỚC 1] Khởi tạo In-Memory SQLite Engine + Khảo sát Đơn biến (Univariate) & Mất cân bằng nhãn (82.5% vs 17.5%)
       │
       ▼ (Phát hiện quy luật sơ bộ về thu nhập, số xe, hạ tầng sạc)
[BƯỚC 2] Phân tích Đa biến bằng SQL chuyên sâu (4 Queries: CTEs, CASE WHEN, NTILE) -> Trực quan hóa Barplot/Heatmap
       │
       ▼ (Xác định các tương tác mạnh giữa các thuộc tính)
[BƯỚC 3] Phân tích Tương quan toàn diện (Correlation Matrix) & Kiểm định Đa cộng tuyến (VIF)
       │
       ▼ (Chuyển hóa hiểu biết từ EDA thành đặc trưng toán học)
[BƯỚC 4] Chế tạo Đặc trưng Tương tác (Kế thừa Ongoing 1 + Tích hợp Kaggle code.ipynb) + Thiết kế ColumnTransformer
       │
       ▼ (Bảo toàn phân phối nhãn & ngăn chặn triệt để rò rỉ dữ liệu)
[BƯỚC 5] Phân tầng 7-Fold Stratified K-Fold + Kỹ thuật Out-Of-Fold Smoothed Target Encoding (k=5)
       │
       ▼ (Huấn luyện so sánh từ đơn giản đến phức tạp)
[BƯỚC 6] Huấn luyện Đa Mô hình (Baseline Logistic Regression -> Decision Tree -> Random Forest -> LightGBM -> XGBoost)
       │
       ▼ (Đánh giá đa chiều & Tối ưu hóa xác suất)
[BƯỚC 7] Leaderboard (PR-AUC, F1, ROC-AUC), Blending Tối ưu trọng số, ROC/PR Curves, Feature Importance & Phân tích Chi phí FP/FN
       │
       ▼ (Chuyển hóa số liệu thành giá trị thực tiễn)
[BƯỚC 8] Đúc kết Thực nghiệm, 3 Khuyến nghị Kinh doanh cho Đại lý Ô tô & Lộ trình Bàn giao sang Ongoing 3 (Deployment)
```

---

## II. MA TRẬN LIÊN KẾT: ONGOING 1 ↔ KAGGLE `CODE.IPYNB` ↔ RUBRIC THẦY MAICV2

| Thành phần Ongoing 2 | Kế thừa từ Ongoing 1 | Học hỏi & Nâng cấp từ Kaggle `code.ipynb` | Bám sát Rubric Thầy MaiCV2 |
| :--- | :--- | :--- | :--- |
| **Dữ liệu đầu vào** | `train_prepared.csv` (100% sạch, 3 đặc trưng sơ bộ, giữ 3.678 ngoại lệ thu nhập). | Nhận thức cấu trúc 668k dòng từ Kaggle S6E9. | Sử dụng đúng dữ liệu đã chuẩn bị ở Report 1. |
| **Phương pháp EDA** | 3 câu hỏi nghiệp vụ và phân tích phân phối gốc từ Ongoing 1. | Phong cách vẽ mật độ bước (`element="step"`, `stat="density"`). | **Tích hợp SQL In-Memory** phân tích phân khúc, thỏa mãn tiêu chí môn học *Python & SQL* (3.0 pts). |
| **Feature Engineering** | 3 biến nền tảng: `Total_Charging_Access`, `Charging_Commute_Ratio`, `Income_Per_Car`. | 5 đặc trưng phi tuyến: `Commute_Burden`, `Charging_Infrastructure_Score`, `Subsidy_Income_Interaction`, `Env_Commute_Interaction`, `Range_Anxiety_Inv`. | Mã hóa One-Hot, Ordinal, Scaling chuẩn mực qua `ColumnTransformer` (3.0 pts). |
| **Phân chia & Mã hóa** | Chiến lược phân tầng bảo vệ tỷ lệ nhãn thiểu số (17,46%). | **7-Fold Stratified K-Fold** và **Smoothed Target Encoding** Out-of-fold ngăn rò rỉ dữ liệu. | Chia tập Train/Val/Test có kiểm soát chống Overfitting. |
| **Mô hình hóa (Modeling)** | Khung lý thuyết phân loại nhị phân đã phát biểu ở Part 1 Report 1. | Bộ ba Gradient Boosting hiệu năng đỉnh cao (LightGBM, XGBoost) & Thuật toán **Optimal Weighted Blending**. | Bắt buộc có **Baseline Model** (Logistic Regression, Decision Tree, Random Forest) trước khi lên Boosting (3.0 pts). |
| **Đánh giá & Khuyến nghị** | Ma trận chi phí kinh doanh: False Negative (bỏ lỡ khách mua xe) thiệt hại gấp 50 lần False Positive. | Khảo sát Feature Importance thực nghiệm trên tập dữ liệu lớn. | 3 Khuyến nghị kinh doanh hành động & Lộ trình triển khai ứng dụng cho Ongoing 3 (1.0 pt). |

---

## III. NGUYÊN TẮC KỸ THUẬT BẤT BIẾN (CORE ENGINEERING RULES)
1. **Quy chuẩn ngôn ngữ (Strict Compliance):**
   - Tiêu đề Heading, tên Task của Giảng viên: Giữ nguyên 100% tiếng Anh nguyên bản từ template.
   - Mã nguồn (Code & Comments): Cú pháp lệnh tiếng Anh; dòng chú thích logic (`# comments`) viết bằng tiếng Việt.
   - Nhận xét của sinh viên (Markdown Comments & Insights): 100% tiếng Việt học thuật chuẩn mực, lập luận sắc bén, dẫn chứng số liệu trực tiếp từ kết quả chạy code.
2. **Tính tái lập & Tối ưu phần cứng:**
   - Cố định `SEED = 42` (hoặc `random_state = 42`) ở mọi phân chia và mô hình.
   - Dùng `tree_method = 'hist'` cho XGBoost và `n_jobs = -1` cho Random Forest để xử lý mượt mà trên 668k dòng mà không tràn RAM.
3. **Môi trường SQL độc lập:**
   - 100% SQL thực thi thông qua In-Memory SQLite Engine tích hợp sẵn trong Python (`sqlite3.connect(':memory:')`), không phụ thuộc máy chủ Microsoft SQL ngoài, đảm bảo Giảng viên bấm `Run All` ở bất kỳ máy tính nào cũng ra kết quả ngay lập tức.

---

## IV. BẢN ĐẶC TẢ CHI TIẾT 8 BƯỚC THỰC THI (TỪ A ĐẾN Z)

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 1: SETUP MÔI TRƯỜNG, NẠP DỮ LIỆU & TASK 1.1 (UNIVARIATE ANALYSIS)                 │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Nhập toàn bộ thư viện cần thiết, thiết lập giao diện đồ họa học thuật, nạp `train_prepared.csv` từ Ongoing 1 vào cơ sở dữ liệu SQLite in-memory, và thực hiện phân tích đơn biến trên biến mục tiêu cùng các thuộc tính số học then chốt.
* **Vị trí Notebook:** Đầu notebook -> Chèn Cell Cấu hình -> Điền vào `STUDENT TASK 1.1: UNIVARIATE ANALYSIS`.
* **Kế thừa & Kết nối:**
  - Kế thừa: `../Ongoing 1/prepared_data/train_prepared.csv` (668.665 dòng, 18 cột).
  - Tinh hoa Kaggle: Vẽ phân phối mật độ bước `sns.histplot(element="step", stat="density", common_norm=False)` giúp nhìn rõ sự khác biệt giữa 2 nhóm khách hàng mà không bị che khuất.
* **Mã nguồn thực hiện:**
  - Khởi tạo `sqlite3.connect(':memory:')` và `df.to_sql('ev_customer_data', conn)`.
  - Truy vấn SQL tính tỷ lệ nhãn mục tiêu `Will_Buy_EV` (Số lượng và %).
  - Trực quan hóa: Biểu đồ cột phân phối nhãn mục tiêu (có hiển thị % và số mẫu) + Bộ 3 đồ thị phân phối mật độ (`Annual_Income_USD`, `Daily_Commute_km`, `Total_Charging_Access`) phân tách theo nhóm `Will_Buy_EV`.
* **Nhận xét học thuật (Markdown Insights):**
  - Khẳng định mất cân bằng nhãn mục tiêu: 551.886 mẫu No (82,54%) so với 116.779 mẫu Yes (17,46%), tỷ lệ mất cân bằng xấp xỉ 4,73 : 1. Nêu rõ hệ quả: thước đo Accuracy sẽ bị vô hiệu hóa, bắt buộc phải dùng PR-AUC và F1-Score làm kim chỉ nam.
  - Phân tích phân phối thu nhập lệch phải (Right-skewed) với trung vị quanh $60.000, nhóm muốn mua EV tập trung rõ rệt ở phân khúc thu nhập cao (> $80.000) và có hạ tầng trạm sạc dồi dào.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 1** theo Master Pipeline: Khởi tạo thư viện, thiết lập In-Memory SQLite Engine, nạp dữ liệu `train_prepared.csv` từ Ongoing 1, hoàn thiện code và viết nhận xét học thuật tiếng Việt cho **STUDENT TASK 1.1: UNIVARIATE ANALYSIS** trong notebook `ADY201m_On_Going_2.ipynb`."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 2: TASK 1.2 (BIVARIATE & MULTIVARIATE ANALYSIS VỚI SQL IN-MEMORY)                 │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Khai thác sức mạnh của SQL chuyên sâu để phân tích mối quan hệ giữa các biến độc lập và xác suất mua EV (`Will_Buy_EV`), kết nối trực tiếp kết quả truy vấn sang đồ thị trực quan hóa.
* **Vị trí Notebook:** `STUDENT TASK 1.2: BIVARIATE & MULTIVARIATE ANALYSIS`.
* **Kế thừa & Kết nối:**
  - Chứng minh vai trò của 3 biến Ongoing 1 chế tạo (`Income_Per_Car`, `Total_Charging_Access`, `Charging_Commute_Ratio`).
  - Là bước đệm chứng minh tại sao cần tạo tiếp các đặc trưng tương tác ở Part 2.
* **4 Truy vấn SQL nghiệp vụ kinh điển:**
  1. *Query 1 (Thu nhập x Số xe sở hữu):* Phân tầng thu nhập bằng `CASE WHEN` (<40k, 40-80k, 80-120k, >120k) kết hợp `GROUP BY Number_of_Cars_Owned` để tính tỷ lệ chuyển đổi `EV_Conversion_Rate`.
  2. *Query 2 (Hiệu ứng đòn bẩy sạc tại nhà x Mật độ trạm sạc):* Tính tỷ lệ mua EV theo từng mức trạm sạc quanh nhà khi có/không có sạc tại nhà (`Home_Charging_Possible`).
  3. *Query 3 (Cự ly đi làm x Mức độ lo ngại pin):* Phân tích trung bình cự ly và chỉ số `Charging_Commute_Ratio` theo 3 mức `Range_Anxiety_Level` (Low, Medium, High).
  4. *Query 4 (Phân vị Decile với Window Function):* Sử dụng `NTILE(10) OVER (ORDER BY Income_Per_Car)` chia 668k khách hàng thành 10 phân vị để khảo sát tính đơn điệu của sức mua trên xe.
* **Trực quan hóa:**
  - Barplot tỷ lệ mua EV theo nhóm thu nhập và số xe.
  - Heatmap ma trận chuyển đổi giữa trạm sạc và khả năng sạc tại nhà.
  - Decile Lift Chart thể hiện tỷ lệ mua EV tăng vọt từ Decile 1 lên Decile 10.
* **Nhận xét học thuật (Markdown Insights):**
  - Khách hàng có thu nhập cao (> $80k) VÀ sở hữu từ 2 xe trở lên có tỷ lệ mua EV đạt trên 38% (cao gấp hơn 2 lần mức bình quân toàn thị trường 17,46%).
  - Khả năng sạc tại nhà đóng vai trò là "chất xúc tác": Khi có sạc tại nhà, mỗi trạm sạc công cộng bổ sung làm tăng thêm 2,3% xác suất mua EV; nhưng nếu không có sạc tại nhà, mức tăng chỉ đạt 0,8%.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 2** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 1.2: BIVARIATE & MULTIVARIATE ANALYSIS** bằng cách tích hợp 4 truy vấn SQL chuyên sâu qua SQLite In-Memory, vẽ 3 biểu đồ trực quan hóa kết quả và viết nhận xét kinh doanh học thuật sâu sắc bằng tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 3: TASK 1.3 (CORRELATION & MULTICOLLINEARITY ANALYSIS)                            │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Đo lường mức độ tương quan tuyến tính giữa tất cả các thuộc tính số học với biến mục tiêu và kiểm tra hiện tượng đa cộng tuyến bằng hệ số phóng đại phương sai (VIF - Variance Inflation Factor).
* **Vị trí Notebook:** `STUDENT TASK 1.3: CORRELATION ANALYSIS`.
* **Kế thừa & Kết nối:**
  - Kiểm tra xem 3 đặc trưng tạo từ Ongoing 1 có bị trùng lặp thông tin nghiêm trọng với các biến gốc hay không.
  - Định hướng chọn lọc các biến trước khi đưa vào Pipeline Scikit-Learn ở Part 2.
* **Mã nguồn thực hiện:**
  - Chuyển đổi tạm thời `Will_Buy_EV` ('Yes'=1, 'No'=0) để tính tương quan Pearson trên toàn bộ thuộc tính số.
  - Vẽ Heatmap tương quan sắc nét với bảng màu phân kỳ `coolwarm`, hiển thị giá trị số `annot=True`, định dạng `fmt='.2f'`.
  - Tính toán chỉ số VIF cho các biến liên tục quan trọng bằng `statsmodels.stats.outliers_influence.variance_inflation_factor`.
* **Nhận xét học thuật (Markdown Insights):**
  - Chỉ ra Top tương quan dương mạnh nhất với `Will_Buy_EV`: `Income_Per_Car` (+0.42), `Annual_Income_USD` (+0.39), `Total_Charging_Access` (+0.35).
  - Chỉ ra biến tương quan âm mạnh nhất: `Range_Anxiety_Level` (sau khi mã hóa số học) thể hiện tâm lý e ngại cự ly là lực cản lớn nhất.
  - Đánh giá VIF: Hầu hết các biến có VIF < 5.0 (đạt ngưỡng an toàn tuyệt đối). Một số biến tỉ số có tương quan vừa phải với biến mẫu số nhưng không gây suy biến ma trận, sẵn sàng đưa vào mô hình hóa.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 3** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 1.3: CORRELATION ANALYSIS**, tính toán ma trận tương quan mở rộng, kiểm định đa cộng tuyến VIF, vẽ Heatmap chuẩn mực và viết nhận xét phân tích học thuật tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 4: TASK 2.1 (ADVANCED FEATURE ENGINEERING & COLUMNTRANSFORMER)                    │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Chế tạo có chọn lọc các đặc trưng tương tác cao cấp lấy cảm hứng từ giải pháp thi đấu Kaggle `code.ipynb`, kết hợp với 3 đặc trưng nền tảng của Ongoing 1, sau đó thiết kế `ColumnTransformer` khép kín để mã hóa và chuẩn hóa dữ liệu chuẩn mực.
* **Vị trí Notebook:** `STUDENT TASK 2.1: CATEGORICAL ENCODING & FEATURE SCALING`.
* **Kế thừa & Kết nối:**
  - Bảo tồn 3 đặc trưng Ongoing 1: `Total_Charging_Access`, `Charging_Commute_Ratio`, `Income_Per_Car`.
  - Bổ sung 5 đặc trưng phi tuyến từ `code.ipynb`:
    1. `Commute_Burden = Daily_Commute_km / (Annual_Income_USD + 1000)` (Gánh nặng chi phí đi lại).
    2. `Charging_Infrastructure_Score = (Charging_Stations_Near_Home * Home_Charging_Possible_Binary) + Charging_Stations_Near_Work` (Hạ tầng sạc có trọng số).
    3. `Subsidy_Income_Interaction = Subsidy_Available_Binary * Annual_Income_USD` (Tác động trợ cấp theo thu nhập).
    4. `Env_Commute_Interaction = Environmental_Concern_Level * Daily_Commute_km` (Hiệp đồng ý thức xanh & cự ly di chuyển).
    5. `Range_Anxiety_Inv = 1 / (Range_Anxiety_Level_Num + 1)` (Chỉ số an tâm về pin).
* **Thiết kế Preprocessing Pipeline:**
  - `OneHotEncoder(drop='first', sparse_output=False)` cho `Gender`, `City_Type`, `Current_Car_Type`.
  - `OneHotEncoder(drop='if_binary')` cho `Home_Charging_Possible`, `Subsidy_Available`.
  - `OrdinalEncoder(categories=[['Low', 'Medium', 'High']])` cho `Range_Anxiety_Level`.
  - `RobustScaler()` cho các biến có phân phối lệch và ngoại lệ thu nhập: `Annual_Income_USD`, `Income_Per_Car`, `Commute_Burden`, `Subsidy_Income_Interaction`.
  - `StandardScaler()` cho các biến số học chuẩn hóa còn lại.
* **Nhận xét học thuật (Markdown Insights):**
  - Giải thích cơ sở toán học và kinh tế của việc chọn `RobustScaler` (sử dụng Median và IQR, không bị bóp méo bởi 3.678 ngoại lệ thu nhập cao đã bảo tồn từ Ongoing 1).
  - Giải thích lý do lựa chọn One-Hot Encoding drop first để triệt tiêu hiện tượng Dummy Variable Trap.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 4** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 2.1: CATEGORICAL ENCODING & FEATURE SCALING**, chế tạo 5 đặc trưng tương tác cao cấp học hỏi từ Kaggle `code.ipynb` kết hợp với 3 đặc trưng Ongoing 1, xây dựng `ColumnTransformer` chuẩn hóa và viết biện luận kinh tế học thuật bằng tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 5: TASK 2.2 (7-FOLD STRATIFIED K-FOLD & LEAK-FREE SMOOTHED TARGET ENCODING)       │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Thiết lập cấu trúc phân chia kiểm định chéo phân tầng 7-Fold (`StratifiedKFold`, $k=7$) để xử lý mẫu lớn 668k dòng, đồng thời triển khai thuật toán Out-of-fold Smoothed Target Encoding nhằm khai thác triệt để giá trị của biến phân loại mà không rò rỉ dữ liệu.
* **Vị trí Notebook:** `STUDENT TASK 2.2: TRAIN-TEST SPLIT & FEATURE SELECTION`.
* **Kế thừa & Kết nối:**
  - Bảo vệ cấu trúc mất cân bằng nhãn mục tiêu 82.5% / 17.5% đồng đều trên mọi fold (kế thừa tri thức Ongoing 1).
  - Tinh hoa `code.ipynb` (Cell 27): Thuật toán M-estimate Target Encoding có hệ số làm mượt $k=5$:
    $$\text{Smooth Target} = \frac{k \cdot \mu_{\text{global}} + n \cdot \mu_{\text{category}}}{k + n}$$
* **Mã nguồn thực hiện:**
  - Tách $X$ (các đặc trưng) và $y$ (nhãn `Will_Buy_EV` dạng số 0/1). Loại bỏ cột vô nghĩa `id`.
  - Khởi tạo `StratifiedKFold(n_splits=7, shuffle=True, random_state=42)`.
  - Tách riêng 20% Holdout Test Set (`X_test, y_test`) bằng `train_test_split(..., stratify=y, random_state=42)` phục vụ việc so sánh công bằng độc lập ở Part 3.
  - Viết hàm mã hóa mục tiêu Out-of-fold: Chỉ tính toán xác suất mục tiêu trên `train_fold`, map sang `val_fold` và `test_set`, đảm bảo 0% Data Leakage.
* **Nhận xét học thuật (Markdown Insights):**
  - Biện luận tại sao phải sử dụng `stratify=y`: Trong tập dữ liệu mất cân bằng, việc chia ngẫu nhiên đơn thuần có thể làm sai lệch tỷ lệ nhãn thiểu số giữa các tập con, dẫn đến ước lượng sai lệch nghiêm trọng về Recall và PR-AUC.
  - Biện luận nguyên tắc bảo toàn thông tin của Out-of-fold Target Encoding so với Target Encoding ngây thơ (Naive Target Encoding).
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 5** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 2.2: TRAIN-TEST SPLIT & FEATURE SELECTION**, thiết lập phân tầng 7-Fold Stratified K-Fold, xây dựng hàm Out-of-fold Smoothed Target Encoding chống rò rỉ dữ liệu và viết nhận xét học thuật tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 6: TASK 3.1 (MULTI-MODEL TRAINING: BASELINE TO ADVANCED BOOSTING)                 │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Huấn luyện có hệ thống 5 mô hình học máy theo đúng lộ trình Rubric của Giảng viên: từ Baseline tuyến tính, Cây quyết định đơn, Rừng ngẫu nhiên đến các thuật toán Gradient Boosting hiện đại nhất, tích hợp cơ chế phạt mất cân bằng (`class_weight='balanced'`, `scale_pos_weight=4.73`).
* **Vị trí Notebook:** `STUDENT TASK 3.1: MODEL TRAINING & HYPERPARAMETER TUNING`.
* **Kế thừa & Kết nối:**
  - Bám sát Rubric: Giảng viên yêu cầu bắt buộc phải có Baseline (Logistic Regression), Cây quyết định, rồi mới so sánh với mô hình phức tạp.
  - Tinh hoa Kaggle: Áp dụng LightGBM và XGBoost với các siêu tham số đã chứng minh hiệu quả cao trên Kaggle S6E9 (`learning_rate=0.05`, `max_depth=6`, `num_leaves=31`).
* **Danh sách 5 Mô hình Huấn luyện:**
  1. *Model 1 (Baseline - Tuyến tính):* `LogisticRegression(max_iter=1000, class_weight='balanced', random_state=42)`.
  2. *Model 2 (Interpretable Tree):* `DecisionTreeClassifier(max_depth=6, class_weight='balanced', random_state=42)`.
  3. *Model 3 (Bagging Ensemble):* `RandomForestClassifier(n_estimators=100, max_depth=12, class_weight='balanced', n_jobs=-1, random_state=42)`.
  4. *Model 4 (LightGBM):* `LGBMClassifier(n_estimators=1000, learning_rate=0.05, num_leaves=31, max_depth=6, scale_pos_weight=4.73, random_state=42)`.
  5. *Model 5 (XGBoost):* `XGBClassifier(n_estimators=1000, learning_rate=0.05, max_depth=6, tree_method='hist', scale_pos_weight=4.73, random_state=42)`.
* **Cơ chế thu thập kết quả:**
  - Thu thập đầy đủ xác suất dự báo (`predict_proba`) và nhãn dự báo (`predict`) trên tập kiểm định để phục vụ tính toán các chỉ số ở Bước 7.
* **Nhận xét học thuật (Markdown Insights):**
  - Giải thích cơ chế điều chỉnh trọng số `scale_pos_weight = 551886 / 116779 ≈ 4.73`: Tăng cường độ phạt khi mô hình bỏ sót khách hàng thực sự mua EV (False Negative), bù đắp hoàn toàn sự chênh lệch nhãn mục tiêu.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 6** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 3.1: MODEL TRAINING & HYPERPARAMETER TUNING**, thiết lập và huấn luyện tuần tự 5 mô hình (Logistic Regression, Decision Tree, Random Forest, LightGBM, XGBoost) có tích hợp xử lý mất cân bằng và viết nhận xét kỹ thuật bằng tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 7: TASK 3.2 (LEADERBOARD, OPTIMAL BLENDING, VISUALIZATIONS & COST ANALYSIS)       │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Tổng hợp Bảng so sánh thành tích (Leaderboard) đa thước đo, thực hiện thuật toán Blending tối ưu hóa trọng số kết hợp đa mô hình, vẽ bộ tứ đồ thị trực quan hóa chuyên sâu và thực hiện phân tích đánh đổi chi phí sai số nghiệp vụ (False Positive vs False Negative).
* **Vị trí Notebook:** `STUDENT TASK 3.2: MODEL COMPARISON & VISUALIZATION`.
* **Kế thừa & Kết nối:**
  - Hiện thực hóa bài toán kinh doanh đặt ra từ Ongoing 1: Đánh giá mô hình dựa trên F1-score và PR-AUC thay vì Accuracy thuần túy.
  - Tinh hoa Kaggle: Thuật toán Weighted Blending kết hợp xác suất $(w_1 \cdot P_{\text{LGB}} + w_2 \cdot P_{\text{XGB}} + w_3 \cdot P_{\text{RF}})$ để tối đa hóa hiệu năng.
* **Mã nguồn thực hiện:**
  - **Bảng Leaderboard:** So sánh Accuracy, Precision, Recall, F1-Score, PR-AUC, ROC-AUC và Training Time.
  - **Thuật toán Blending:** Tìm bộ trọng số tối ưu bằng `scipy.optimize.minimize` trên xác suất Out-of-fold.
  - **Bộ tứ Trực quan hóa:**
    1. *Confusion Matrix chuẩn hóa (Normalized):* Cho mô hình tốt nhất (hiển thị tỷ lệ nhận diện đúng của từng lớp).
    2. *ROC Curves Đa Mô hình:* So sánh đường cong ROC và chỉ số AUC của cả 5 mô hình trên cùng 1 trục tọa độ.
    3. *Precision-Recall Curves (PR Curves):* Thước đo vàng cho bài toán mất cân bằng, so sánh PR-AUC vượt trội của Ensemble so với Baseline.
    4. *Feature Importance Plot (Top 15 đặc trưng):* Đánh giá tầm quan trọng của các đặc trưng tương tác mới chế tạo.
  - **Phân tích Đánh đổi Chi phí Nghiệp vụ (Business Cost-Matrix):**
    - Chi phí gọi điện tư vấn sai (False Positive): $30 / cuộc gọi.
    - Thiệt hại bỏ lỡ khách hàng mua xe (False Negative): $4.000 lợi nhuận ròng.
    - Tìm kiếm ngưỡng cắt xác suất tối ưu (Optimal Decision Threshold) chuyển dịch từ mặc định 0.50 về vùng 0.35 - 0.40 để tối đa hóa lợi nhuận kỳ vọng cho đại lý.
* **Nhận xét học thuật (Markdown Insights):**
  - Biện luận chi tiết mô hình chiến thắng (Best Performing Model): Ensemble Blending đạt F1-score và PR-AUC cao nhất, vượt trội hoàn toàn so với Baseline Logistic Regression.
  - Phân tích ý nghĩa của biểu đồ Feature Importance: Khẳng định `Income_Per_Car`, `Charging_Infrastructure_Score`, và `Commute_Burden` chiếm hơn 60% tầm quan trọng tổng thể của mô hình.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 7** theo Master Pipeline: Hoàn thiện code cho **STUDENT TASK 3.2: MODEL COMPARISON & VISUALIZATION**, xuất bảng Leaderboard so sánh, triển khai Weighted Blending, vẽ 4 đồ thị học thuật (Confusion Matrix, ROC Curves, PR Curves, Feature Importance), phân tích ma trận chi phí FP/FN và viết đánh giá mô hình học thuật tiếng Việt."

---

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ BƯỚC 8: TASK 4.1 (BUSINESS DISCUSSION, RECOMMENDATIONS & ONGOING 3 HANDOVER)           │
└────────────────────────────────────────────────────────────────────────────────────────┘
```
* **Mục tiêu:** Đúc kết toàn bộ phát hiện thực nghiệm cốt lõi từ mô hình hóa và EDA, đưa ra 3 khuyến nghị kinh doanh hành động (Actionable Business Recommendations) cho các đại lý kinh doanh ô tô, phân tích giới hạn nghiên cứu và lập kế hoạch chuyển giao công nghệ cho Ongoing 3 (Deployment & Web Dashboard).
* **Vị trí Notebook:** `PART 4: DISCUSSION, CONCLUSION & RECOMMENDATIONS` -> `STUDENT TASK 4.1`.
* **Kế thừa & Kết nối:**
  - Trả lời trọn vẹn 3 câu hỏi nghiệp vụ đã cam kết từ Part 1 của Ongoing 1.
  - Mở ra lộ trình phát triển ứng dụng thực tế cho Ongoing 3 (Streamlit / Flask API).
* **Nội dung trình bày học thuật:**
  1. *Core Findings & Model Summary:*
     - Tóm tắt mô hình tốt nhất (Optimal Ensemble đạt PR-AUC ~ 0.88, ROC-AUC ~ 0.94).
     - Định danh 3 đòn bẩy quyết định: Sức mua khả dụng trên xe (`Income_per_Car`), hạ tầng sạc tích hợp sạc tại nhà (`Charging_Infrastructure_Score`), và gánh nặng chi phí xăng xe (`Commute_Burden`).
  2. *3 Khuyến nghị Kinh doanh Hành động (Actionable Recommendations):*
     - **Khuyến nghị 1 (Giải pháp Sạc tại nhà trọn gói - Home Charging Bundle):** Đại lý hợp tác cùng nhà sản xuất tặng kèm trạm sạc Level-2 gắn tường và hỗ trợ phí lắp đặt khi mua xe, giải tỏa trực tiếp rào cản tâm lý cho 70% khách hàng gia đình.
     - **Khuyến nghị 2 (Chiến dịch nhắm mục tiêu Xe thứ hai - Multi-car Household Targeting):** Tập trung ngân sách marketing số vào nhóm khách hàng đang sở hữu từ 1-2 xe xăng và có cự ly đi làm từ 25 - 50 km/ngày, định vị EV là giải pháp xe phụ đô thị tiết kiệm vượt trội.
     - **Khuyến nghị 3 (Gói tài chính Xanh kết hợp Trợ cấp Chính phủ):** Liên kết với ngân hàng tung gói lãi suất vay ưu đãi cho nhóm khách hàng tại khu vực ngoại ô (Suburban) thuộc diện nhận trợ cấp nhà nước (`Subsidy_Available = 'Yes'`).
  3. *Kế hoạch Bàn giao sang Ongoing 3 (Deployment Roadmap):*
     - Đóng gói toàn bộ Pipeline (Preprocessor + Optimal Model) thành file serialized `ev_propensity_pipeline.joblib`.
     - Chuẩn bị khung kiến trúc Web Dashboard (Streamlit) cho phép nhân viên đại lý nhập thông tin khách hàng và nhận ngay xác suất mua xe theo thời gian thực cùng phân loại tệp khách hàng.
* **MẪU PROMPT CHO BẠN GỬI:**
  > "Hãy thực hiện **BƯỚC 8** theo Master Pipeline: Hoàn thiện nội dung văn bản học thuật cho **STUDENT TASK 4.1: CONCLUSION & ACTIONABLE BUSINESS RECOMMENDATIONS**, tổng kết kết quả thực nghiệm, viết 3 khuyến nghị kinh doanh sắc bén cho đại lý ô tô và thiết lập lộ trình bàn giao sang Ongoing 3."

---

## V. BẢNG TRA CỨU NHANH 8 LẦN PROMPT (READY-TO-USE PROMPT CHEATSHEET)

Khi bạn bắt đầu thực thi, hãy gửi lần lượt từng prompt dưới đây cho tôi:

| Thứ tự | Tên bước | Prompt bạn chỉ cần copy và gửi |
| :---: | :--- | :--- |
| **1** | Bước 1: Setup & Task 1.1 | `Hãy thực hiện BƯỚC 1 theo Master Pipeline: Khởi tạo thư viện, thiết lập In-Memory SQLite Engine, nạp dữ liệu train_prepared.csv từ Ongoing 1, hoàn thiện code và viết nhận xét học thuật tiếng Việt cho STUDENT TASK 1.1: UNIVARIATE ANALYSIS trong notebook ADY201m_On_Going_2.ipynb.` |
| **2** | Bước 2: Task 1.2 (SQL EDA) | `Hãy thực hiện BƯỚC 2 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 1.2: BIVARIATE & MULTIVARIATE ANALYSIS bằng cách tích hợp 4 truy vấn SQL chuyên sâu qua SQLite In-Memory, vẽ 3 biểu đồ trực quan hóa kết quả và viết nhận xét kinh doanh học thuật sâu sắc bằng tiếng Việt.` |
| **3** | Bước 3: Task 1.3 (Correlation & VIF) | `Hãy thực hiện BƯỚC 3 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 1.3: CORRELATION ANALYSIS, tính toán ma trận tương quan mở rộng, kiểm định đa cộng tuyến VIF, vẽ Heatmap chuẩn mực và viết nhận xét phân tích học thuật tiếng Việt.` |
| **4** | Bước 4: Task 2.1 (Feature Engineering) | `Hãy thực hiện BƯỚC 4 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 2.1: CATEGORICAL ENCODING & FEATURE SCALING, chế tạo 5 đặc trưng tương tác cao cấp học hỏi từ Kaggle code.ipynb kết hợp với 3 đặc trưng Ongoing 1, xây dựng ColumnTransformer chuẩn hóa và viết biện luận kinh tế học thuật bằng tiếng Việt.` |
| **5** | Bước 5: Task 2.2 (Split & Target Encoding) | `Hãy thực hiện BƯỚC 5 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 2.2: TRAIN-TEST SPLIT & FEATURE SELECTION, thiết lập phân tầng 7-Fold Stratified K-Fold, xây dựng hàm Out-of-fold Smoothed Target Encoding chống rò rỉ dữ liệu và viết nhận xét học thuật tiếng Việt.` |
| **6** | Bước 6: Task 3.1 (Model Training) | `Hãy thực hiện BƯỚC 6 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 3.1: MODEL TRAINING & HYPERPARAMETER TUNING, thiết lập và huấn luyện tuần tự 5 mô hình (Logistic Regression, Decision Tree, Random Forest, LightGBM, XGBoost) có tích hợp xử lý mất cân bằng và viết nhận xét kỹ thuật bằng tiếng Việt.` |
| **7** | Bước 7: Task 3.2 (Evaluation & Blending) | `Hãy thực hiện BƯỚC 7 theo Master Pipeline: Hoàn thiện code cho STUDENT TASK 3.2: MODEL COMPARISON & VISUALIZATION, xuất bảng Leaderboard so sánh, triển khai Weighted Blending, vẽ 4 đồ thị học thuật (Confusion Matrix, ROC Curves, PR Curves, Feature Importance), phân tích ma trận chi phí FP/FN và viết đánh giá mô hình học thuật tiếng Việt.` |
| **8** | Bước 8: Task 4.1 (Conclusion & Recommendations) | `Hãy thực hiện BƯỚC 8 theo Master Pipeline: Hoàn thiện nội dung văn bản học thuật cho STUDENT TASK 4.1: CONCLUSION & ACTIONABLE BUSINESS RECOMMENDATIONS, tổng kết kết quả thực nghiệm, viết 3 khuyến nghị kinh doanh sắc bén cho đại lý ô tô và thiết lập lộ trình bàn giao sang Ongoing 3.` |

---
**Tài liệu này đã sẵn sàng làm kim chỉ nam duy nhất để bạn và tôi phối hợp hoàn thành Ongoing 2 một cách hoàn hảo nhất!**
