# BÁO CÁO TOÀN DIỆN DỰ ÁN ADY201m (ONGOING 1)
## DỰ ĐOÁN XU HƯỚNG MUA XE ĐIỆN PHỤC VỤ TIẾP THỊ MỤC TIÊU
*(Predicting Electric Vehicle Purchase Propensity for Customer Targeting)*

---

## MỤC LỤC
1. [Bức tranh tổng thể: Dự án này làm gì, cho ai và để giải quyết việc gì?](#1-bức-tranh-tổng-thể)
2. [Input & Output của bài toán là gì?](#2-input--output-của-bài-toán)
3. [Tại sao TUYỆT ĐỐI KHÔNG DÙNG Accuracy? Thảm họa của bài toán mất cân bằng](#3-tại-sao-tuyệt-đối-không-dùng-accuracy)
4. [Hiểu tường tận về Precision, Recall & F1-Score](#4-hiểu-tường-tận-về-precision-recall--f1-score)
5. [ROC-AUC trong bài toán này cho biết điều gì?](#5-roc-auc-trong-bài-toán-này-cho-biết-điều-gì)
6. [Giải thích chi tiết từng Code Cell & Biểu đồ theo luồng xử lý (Flow)](#6-giải-thích-chi-tiết-từng-code-cell--biểu-đồ)
   - [Part 2 — Khởi tạo và khám phá ban đầu (Cells 1, 2, 3)](#part-2--khởi-tạo-và-khám-phá-ban-đầu)
   - [Part 3 — Tiền xử lý & Chuẩn bị dữ liệu (6 Cells chính)](#part-3--tiền-xử-lý--chuẩn-bị-dữ-liệu-6-cells-chính)
   - [Part 4 — Đánh giá mức độ sẵn sàng & Xuất bản (Cell 10)](#part-4--đánh-giá-mức-độ-sẵn-sàng--kiểm-định-cuối-cùng)
7. [Tóm tắt bộ câu hỏi vấn đáp nhanh (Cheat Sheet)](#7-tóm-tắt-bộ-câu-hỏi-vấn-đáp-nhanh)

---

## 1. BỨC TRANH TỔNG THỂ
### Dự án này làm gì, cho ai và để giải quyết việc gì?

Hãy tưởng tượng bạn là Giám đốc Kinh doanh của một **Hệ thống Đại lý Phân phối Ô tô**. 
Hiện tại, đại lý của bạn đang muốn mở rộng sang mảng **Xe Điện (Electric Vehicle - EV)**. 

Trong tay bạn có danh sách thông tin của hàng trăm nghìn khách hàng (từ khảo sát, khách hàng cũ hoặc dữ liệu đăng ký lái thử).
Nếu bạn cho đội ngũ nhân viên kinh doanh (Telesale) bốc máy gọi điện ngẫu nhiên cho tất cả mọi người:
- **Tốn kém chi phí cực lớn:** Tiền cước viễn thông, lương nhân viên gọi điện cả ngày.
- **Khách hàng khó chịu:** 80% người được gọi không có nhu cầu hoặc sợ xe điện hết pin giữa đường, dẫn đến cảm giác bị làm phiền.
- **Nhân viên nản lòng:** Gọi 100 cuộc chỉ chốt được vài người quan tâm.

> [!NOTE]
> **Mục tiêu của dự án:** 
> Dùng Khoa học Dữ liệu (Data Science) và Học máy (Machine Learning) để phân tích chân dung khách hàng, từ đó **dự đoán trước ai là người có khả năng mua xe điện cao nhất**. Khi đó, đại lý chỉ cần tập trung 100% nguồn lực (gọi điện chuyên sâu, mời lái thử, gửi voucher giảm giá) vào đúng nhóm khách hàng tiềm năng này!

---

## 2. INPUT & OUTPUT CỦA BÀI TOÁN

Mỗi bài toán học máy đều bắt đầu từ việc xác định rõ hai thành phần: **Đầu vào (Input)** và **Đầu ra (Output)**.

```
       [ INPUT: Bảng dữ liệu quan sát ]
  (Tuổi, Thu nhập, Cự ly, Hạ tầng trạm sạc,...)
                      │
                      ▼
            ┌──────────────────┐
            │ MÔ HÌNH HỌC MÁY  │
            └──────────────────┘
                      │
                      ▼
     [ OUTPUT: Dự đoán Will_Buy_EV ]
           ( "Yes" hoặc "No" )
```

### 2.1. Input (Đầu vào - Ma trận đặc trưng $X$)
Input là toàn bộ các thông tin mô tả về khách hàng mà đại lý thu thập được:
1. **Thông tin nhân khẩu học (Demographics):**
   - `Age`: Độ tuổi khách hàng (từ 18 đến 70 tuổi).
   - `Gender`: Giới tính (`Male`, `Female`, `Other`).
   - `Annual_Income_USD`: Thu nhập hàng năm tính bằng USD ($30.000 đến $188.549).
   - `City_Type`: Khu vực sinh sống (`Urban` - Đô thị, `Suburban` - Ngoại ô, `Rural` - Nông thôn).
2. **Thói quen di chuyển & Phương tiện hiện có (Mobility & Assets):**
   - `Daily_Commute_km`: Quãng đường đi lại hàng ngày bằng ô tô (từ 5 đến 98,7 km).
   - `Number_of_Cars_Owned`: Số lượng xe hơi đang có trong nhà (từ 1 đến 4 xe).
   - `Current_Car_Type`: Dòng xe đang chạy (`Sedan`, `SUV`, `Hatchback`, `Truck`).
3. **Điều kiện sạc & Chính sách hỗ trợ (Infrastructure & Policy):**
   - `Charging_Stations_Near_Home`: Số trạm sạc quanh nhà (0 đến 14 trạm).
   - `Charging_Stations_Near_Work`: Số trạm sạc quanh nơi làm việc (0 đến 19 trạm).
   - `Home_Charging_Possible`: Nhà có chỗ cắm sạc qua đêm không? (`Yes`/`No`).
   - `Subsidy_Available`: Có được hưởng trợ cấp/ưu đãi thuế không? (`Yes`/`No`).
4. **Tâm lý & Nhận thức (Attitudes & Perception):**
   - `Environmental_Concern_Level`: Mức độ quan tâm môi trường (thang điểm 1 đến 5).
   - `Range_Anxiety_Level`: Mức độ lo ngại xe hết pin giữa đường (`Low`, `Medium`, `High`).
5. **3 Đặc trưng mới do nhóm tạo ra (Engineered Features):**
   - `Total_Charging_Access`: Tổng số trạm sạc quanh nhà và cơ quan.
   - `Charging_Commute_Ratio`: Mật độ trạm sạc chia cho quãng đường đi làm (+1 km).
   - `Income_Per_Car`: Thu nhập bình quân chia cho mỗi chiếc xe đang sở hữu.

### 2.2. Output (Đầu ra - Nhãn mục tiêu $y$)
- Tên biến: **`Will_Buy_EV`** (Khách hàng có dự định mua xe điện hay không?).
- Dạng bài toán: **Phân loại nhị phân (Binary Classification)**.
- Giá trị:
  - `"Yes"` (Lớp tích cực / Positive Class): Khách hàng có xu hướng mua.
  - `"No"` (Lớp tiêu cực / Negative Class): Khách hàng không có ý định mua.
- Ở giai đoạn Ongoing 2, đầu ra của mô hình không chỉ là chữ `"Yes"` hay `"No"`, mà là **xác suất $P(y = \text{"Yes"} | X)$ từ 0,0 đến 1,0**. Đại lý có thể xếp thứ tự từ người có xác suất cao nhất xuống thấp nhất để gọi điện.

---

## 3. TẠI SAO TUYỆT ĐỐI KHÔNG DÙNG ACCURACY?
### Thảm họa của bài toán mất cân bằng nhãn (Class Imbalance)

Rất nhiều người mới học Data Science thường nghĩ ngay đến **Accuracy (Độ chính xác tổng thể)**. Công thức của Accuracy rất đơn giản:
$$\text{Accuracy} = \frac{\text{Số dự đoán ĐÚNG}}{\text{Tổng số quan sát}} = \frac{TP + TN}{TP + TN + FP + FN}$$

> [!CAUTION]
> **Trong dự án này, nếu bạn dùng Accuracy, bạn sẽ bị điểm kém và doanh nghiệp sẽ bị phá sản! Tại sao?**

Hãy nhìn vào phân phối dữ liệu thực tế của tập `train.csv` (gồm 668.665 khách hàng):
- Khách hàng chọn **`"No"` (Không mua)**: **551.886 người (chiếm 82,54%)**
- Khách hàng chọn **`"Yes"` (Sẽ mua)**: **116.779 người (chiếm 17,46%)**

Tỷ lệ này bị lệch khoảng **4,7 : 1** (cứ gần 5 người thì chỉ có 1 người mua xe). Đây là hiện tượng **Mất cân bằng dữ liệu (Imbalanced Data)**.

#### Kịch bản "Mô hình lười biếng" (Dummy Lazy Classifier)
Bây giờ, giả sử lập trình viên tạo ra một con bot cực kỳ lười biếng: **Nó chẳng cần học thuật toán gì cả, bất kể ai bước vào cửa, nó đều phán bừa: `"KHÔNG MUA"` (`"No"`)**.

Hãy tính Accuracy của con bot này:
- Toàn bộ 551.886 người không mua: Nó đoán đúng 100%!
- Toàn bộ 116.779 người muốn mua: Nó đoán sai 100%!
- **Accuracy của nó là:**
  $$\text{Accuracy} = \frac{551.886}{668.665} \approx \mathbf{82,54\%}!$$

**Hậu quả thực tế:**
- Con bot đạt độ chính xác "khủng" hơn 82%!
- Nhưng đối với đại lý ô tô, **con bot này vô dụng 100%**: Nó không chỉ ra được **dù chỉ 1 khách hàng** trong số 116.779 người thực sự muốn mua xe! Đại lý không bán được chiếc xe nào và đóng cửa!
- Do đó: **Accuracy tạo ra ảo tưởng về hiệu năng cao, che giấu hoàn toàn sự thất bại trong việc phát hiện lớp thiểu số quan trọng (`"Yes"`).**

---

## 4. HIỂU TƯỜNG TẬN VỀ PRECISION, RECALL & F1-SCORE

Để đánh giá đúng thực chất, người ta dùng **Ma trận nhầm lẫn (Confusion Matrix)** tập trung vào lớp quan trọng nhất: **Lớp `"Yes"`**.

| Thực tế \ Dự đoán | Dự đoán là `"No"` | Dự đoán là `"Yes"` |
| :---: | :---: | :---: |
| **Thực tế là `"No"`** | **True Negative (TN):** Đoán không mua, thực tế đúng là không mua | **False Positive (FP):** Báo động giả! Đoán sẽ mua nhưng thực tế họ không mua |
| **Thực tế là `"Yes"`** | **False Negative (FN):** Bỏ sót khách! Thực tế họ muốn mua nhưng mô hình bảo không mua | **True Positive (TP):** Bắt trúng đích! Đoán sẽ mua và họ thực sự mua |

---

### 4.1. Precision (Độ chuẩn xác khi dự đoán Yes)
$$\text{Precision} = \frac{TP}{TP + FP} = \frac{\text{Số người mua thật trong nhóm được dự đoán Yes}}{\text{Tổng số người mà mô hình bảo Yes}}$$

- **Ý nghĩa đời thực:** Khi mô hình nói: *"100 người này sẽ mua xe đấy!"*, thì có bao nhiêu người thực sự rút tiền mua?
- **Nếu Precision thấp:** Đội Telesale gọi 100 người mà 90 người mắng xối xả bảo không có nhu cầu -> Lãng phí thời gian và tiền bạc của nhân viên.
- **Khi nào cần Precision cao?** Khi chi phí để tiếp cận một người rất đắt đỏ (ví dụ: tặng xe chạy thử tận nhà, tặng quà VIP).

---

### 4.2. Recall (Độ bao phủ / Độ nhạy - Bắt trúng khách mua)
$$\text{Recall} = \frac{TP}{TP + FN} = \frac{\text{Số người mua mà mô hình tìm ra}}{\text{Toàn bộ 116.779 người thực sự muốn mua ngoài đời}}$$

- **Ý nghĩa đời thực:** Ngoài kia có 116.779 người đang có ý định mua xe điện. Mô hình của bạn "lùng sục" và tóm được bao nhiêu % trong số họ?
- **Nếu Recall thấp:** Có 100 khách hàng tiềm năng muốn mua xe nhưng mô hình chỉ nhận ra 20 người, bỏ sót 80 người rơi vào tay đối thủ cạnh tranh!
- **Tại sao Recall trong dự án này lại cực kỳ quan trọng?**
  - Bán 1 chiếc xe điện, đại lý lãi hàng nghìn USD. Bỏ sót 1 khách mua (**False Negative**) là mất đứt khoản tiền lớn.
  - Trong khi đó, gọi nhầm cho 1 người không mua (**False Positive**) chỉ mất 1 cuộc điện thoại 2.000 VNĐ.
  - Do đó, doanh nghiệp luôn muốn **Recall càng cao càng tốt** để không bỏ sót khách sộp!

---

### 4.3. Sự mâu thuẫn (Trade-off) giữa Precision và Recall
Hai chỉ số này luôn "kéo co" với nhau:
- Nếu bạn muốn **Precision cực cao (99%)**: Bạn chỉ dám chọn những người siêu giàu, nhà có 5 trạm sạc. Lúc này đoán ai là trúng người đó, nhưng bạn sẽ **bỏ sót 90% khách hàng khác** -> Recall rớt xuống đáy!
- Nếu bạn muốn **Recall cực cao (99%)**: Bạn dự đoán `"Yes"` cho hầu hết tất cả mọi người để không sót ai. Nhưng khi đó gọi 10 người thì 8 người từ chối -> Precision rớt thê thảm!

---

### 4.4. F1-Score: Trọng tài công tâm
Để giải quyết mâu thuẫn trên, người ta sinh ra **F1-Score**. F1-Score là **Trung bình điều hòa (Harmonic Mean)** giữa Precision và Recall:
$$F_1 = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$

> [!TIP]
> **Tại sao phải dùng Trung bình điều hòa mà không dùng trung bình cộng thông thường?**
> - Giả sử mô hình có: $\text{Precision} = 1.0$ (rất cao) nhưng $\text{Recall} = 0.0$ (bỏ sót toàn bộ).
> - Nếu lấy trung bình cộng: $\frac{1.0 + 0.0}{2} = 0.5$ (Nhìn con số 0.5 tưởng là tàm tạm, nhưng thực tế mô hình vô dụng vì Recall bằng 0!).
> - Nhưng nếu lấy F1-Score: $2 \times \frac{1.0 \times 0.0}{1.0 + 0.0} = \mathbf{0.0}$!
> - **F1-Score sẽ trừng phạt rất nặng nếu một trong hai chỉ số bị thấp.** Mô hình chỉ có F1-Score cao khi và chỉ khi **vừa tìm được nhiều khách (Recall tốt) vừa không đoán bừa bãi (Precision tốt)**.

---

## 5. ROC-AUC TRONG BÀI TOÁN NÀY CHO BIẾT ĐIỀU GÌ?

**ROC-AUC** là viết tắt của: **Receiver Operating Characteristic — Area Under the Curve** (Diện tích dưới đường cong ROC).

### 5.1. Khái niệm đơn giản nhất về ROC-AUC
Khi mô hình phân loại dự đoán, nó không xuất ra chữ `"Yes"` hay `"No"` ngay, mà nó trả về một con số: **Xác suất mua xe (từ 0.0 đến 1.0)**.
- Người A: 0.85
- Người B: 0.62
- Người C: 0.15

Bình thường ta chọn ngưỡng mặc định là **0.5**: ai trên 0.5 là `"Yes"`, dưới 0.5 là `"No"`. Nhưng ai bắt ta phải chọn 0.5? Ta hoàn toàn có thể hạ ngưỡng xuống 0.3 (để bắt thêm nhiều khách) hoặc nâng lên 0.7.

**ROC** là đường cong vẽ ra khi ta dịch chuyển ngưỡng từ 0.0 lên 1.0, thể hiện sự đánh đổi giữa:
- **Trục Y: True Positive Rate (chính là Recall)** — Bắt trúng khách mua.
- **Trục X: False Positive Rate** — Báo động nhầm người không mua.

**AUC** là diện tích nằm bên dưới đường cong đó (chạy từ 0.5 đến 1.0):

```text
  1.0 ┌───────────────────┐  AUC = 1.0 : Hoàn hảo tuyệt đối (Tiên tri)
      │               .---│
  T   │            .-'    │  AUC = 0.8 - 0.9 : Rất tốt (Mô hình xịn)
  P   │         .-'       │
  R   │       .'          │  AUC = 0.5 : Đường chéo ngẫu nhiên (Tung đồng xu)
      │     .'            │
  0.0 └────┴──────────────┘
     0.0                 1.0
             FPR
```

---

### 5.2. Ý nghĩa kinh doanh trực giác của ROC-AUC
Hãy tưởng tượng bạn bước vào phòng chờ của đại lý và **bốc ngẫu nhiên 2 người**:
- **Người thứ nhất:** Thực tế là người **SẼ MUA XE** (`"Yes"`).
- **Người thứ hai:** Thực tế là người **KHÔNG MUA XE** (`"No"`).

Bạn đưa thông tin của cả 2 người cho mô hình chấm điểm xác suất:
> **ROC-AUC chính là xác suất mà mô hình sẽ chấm điểm cho người SẼ MUA cao hơn người KHÔNG MUA!**

- Nếu **ROC-AUC = 0.5**: Mô hình giống như một người tung đồng xu hên xui, hoàn toàn không phân biệt được ai muốn mua ai không.
- Nếu **ROC-AUC = 0.85**: Có **85% khả năng** mô hình sẽ chấm điểm xác suất của người mua xe cao hơn người không mua xe!
- **Lợi ích thực tế:** Khi Telesale sắp xếp danh sách 10.000 khách hàng từ điểm cao xuống điểm thấp, ROC-AUC cao đảm bảo rằng những người thực sự muốn mua sẽ **nằm ở đầu danh sách**, giúp nhân viên gọi điện đến đâu là chốt đơn đến đó!

---

## 6. GIẢI THÍCH CHI TIẾT TỪNG CODE CELL & BIỂU ĐỒ
### Theo đúng luồng thực thi trong [ADY201m_On_Going_1_Working.ipynb](file:///d:/Ongoing%201%20rework/ADY201m_On_Going_1_Working.ipynb)

Toàn bộ notebook gồm đúng **10 Code cells** được tổ chức theo quy trình chuẩn khoa học dữ liệu:
`Part 2 (3 cells) → Part 3 (6 cells) → Part 4 (1 cell)`.

---

### PART 2 — KHỞI TẠO VÀ KHÁM PHÁ BAN ĐẦU

#### Cell [1] — Data Loading & Structure Inspection
```python
# Đọc file train và test từ thư mục dataset/
df_train = pd.read_csv('dataset/train.csv')
df_test = pd.read_csv('dataset/test.csv')
```
- **Ý nghĩa code:**
  - `pd.read_csv(...)`: Lệnh của thư viện Pandas đọc dữ liệu bảng từ đĩa cứng vào bộ nhớ RAM dưới dạng DataFrame.
  - Sử dụng đường dẫn tương đối `'dataset/...'` giúp notebook chạy mượt trên mọi máy tính mà không bị lỗi cứng ổ đĩa (như `C:\` hay `D:\`).
- **Biểu đồ Cell [1] sinh ra:** Không vẽ biểu đồ, in ra bảng `.head()` và cấu trúc `.info()`.
- **Kết quả thu được:** Train có 668.665 dòng, 15 cột; Test có 286.571 dòng, 14 cột. Test không có cột `Will_Buy_EV`.

#### Cell [2] — Missing Values & Duplicates Check
```python
train_missing = df_train.isnull().sum()
train_duplicates = df_train.duplicated().sum()
```
- **Ý nghĩa code:**
  - `.isnull().sum()`: Đếm xem trên mỗi cột có bao nhiêu ô bị bỏ trống (NaN).
  - `.duplicated().sum()`: Kiểm tra xem có 2 dòng nào giống hệt nhau từ đầu đến cuối không.
- **Biểu đồ sinh ra:** Bảng đối soát thống kê giá trị rỗng.
- **Kết quả thu được:** Phát hiện **0 missing values** và **0 duplicate rows** trên cả hai tập. Dữ liệu gốc sạch sẽ hoàn hảo.

#### Cell [3] — Descriptive Statistics & Target Imbalance
```python
df_train[num_cols].describe()
df_train['Will_Buy_EV'].value_counts(normalize=True)
```
- **Ý nghĩa code:**
  - `.describe()`: Tính toán các thông số thống kê cơ bản: giá trị nhỏ nhất (`min`), lớn nhất (`max`), trung bình (`mean`), độ lệch chuẩn (`std`), các mốc tứ phân vị (25%, 50%, 75%).
  - `.value_counts(normalize=True)`: Đếm tỷ lệ phần trăm của từng nhóm trong biến mục tiêu `Will_Buy_EV`.
- **Biểu đồ sinh ra:** Biểu đồ Bar chart thể hiện phân phối biến mục tiêu (No: 82,54%, Yes: 17,46%).
- **Ý nghĩa biểu đồ:** Nhìn vào biểu đồ là thấy ngay cột `"No"` cao gấp gần 5 lần cột `"Yes"`. Đây là bằng chứng thép kết luận dữ liệu bị **mất cân bằng nhãn**, cấm sử dụng Accuracy!

---

### PART 3 — TIỀN XỬ LÝ & CHUẨN BỊ DỮ LIỆU (6 CELLS CHÍNH)

Part 3 được cấu trúc lại tinh gọn thành 6 code cells theo đúng quy trình **Data Preparation Flow**:

```
[Cell 1: Ingestion] ➔ [Cell 2: Cleaning] ➔ [Cell 3: Architecture]
        ➔ [Cell 4: Distribution] ➔ [Cell 5: Engineering] ➔ [Cell 6: Export & Verify]
```

---

#### Cell [4] (Part 3 — Cell 1): Importing Datasets & Initial Validation
*(Tương ứng Rubric Bước 1: Importing Datasets)*

```python
# 1. Khai báo đường dẫn tương đối an toàn
DATA_DIR = Path('dataset')
train_path = DATA_DIR / 'train.csv'
test_path = DATA_DIR / 'test.csv'

# 2. Nạp dữ liệu vào DataFrame làm việc độc lập
df_train_prep = pd.read_csv(train_path)
df_test_prep = pd.read_csv(test_path)
```
- **Giải thích từng dòng code:**
  - `Path('dataset')`: Sử dụng thư viện `pathlib` hiện đại của Python để quản lý đường dẫn file một cách chuyên nghiệp, tự động tương thích trên cả Windows (dấu `\`) và Linux/Mac (dấu `/`).
  - `df_train_prep = pd.read_csv(...)`: Nạp dữ liệu vào các biến mới có đuôi `_prep` (Prepared).
  - **Quy tắc an toàn sống còn:** Chúng ta không thao tác đè lên dữ liệu thô ban đầu, đảm bảo tính bất biến của tệp gốc.
  - `set(df_train_prep.columns) - set(df_test_prep.columns)`: Lấy tập hợp cột train trừ tập hợp cột test để kiểm tra phần bù. Kết quả trả về đúng `{'Will_Buy_EV'}`. Điều này khẳng định 100% tập test không bị rò rỉ nhãn (No Label Leakage).
- **Biểu đồ trong Cell [4]: Bar chart So sánh quy mô mẫu Train vs. Test:**
  - Trục hoành (X): Hai cột đại diện cho `Train Set` (xanh đậm) và `Test Set` (xanh nhạt).
  - Trục tung (Y): Số lượng dòng dữ liệu (từ 0 đến 800.000).
  - **Ý nghĩa biểu đồ:** Hiển thị trực quan tỷ lệ phân chia tập dữ liệu đúng chuẩn tỉ lệ vàng **70% (Train: 668.665 dòng)** và **30% (Test: 286.571 dòng)**. Giúp người đọc nắm bắt ngay quy mô mẫu đồ sộ của dự án (gần 1 triệu bản ghi) mà không cần đọc bảng số liệu khô khan.

---

#### Cell [5] (Part 3 — Cell 2): Missing Values & Data Formatting
*(Tương ứng Rubric Bước 2: Handling Missing Values & Bước 3: Data Formatting; tích hợp Task 3.1, 3.2, 3.3)*

```python
# 1. Rà soát trùng lặp kể cả khi bỏ cột id
train_dup_no_id = df_train_prep.drop(columns=['id']).duplicated().sum()

# 2. Chuẩn hóa khoảng trắng văn bản bằng .str.strip()
cat_cols = ['Gender', 'City_Type', 'Current_Car_Type', 'Home_Charging_Possible', 
            'Subsidy_Available', 'Range_Anxiety_Level']

for col in cat_cols:
    df_train_prep[col] = df_train_prep[col].str.strip()
    df_test_prep[col] = df_test_prep[col].str.strip()

df_train_prep['Will_Buy_EV'] = df_train_prep['Will_Buy_EV'].str.strip()
```
- **Giải thích từng dòng code:**
  - `.drop(columns=['id']).duplicated().sum()`: Kiểm tra xem có trường hợp nào 2 khách hàng có thông tin giống hệt nhau nhưng chỉ khác mã ID hay không. Kết quả là 0 -> Dữ liệu hoàn toàn độc lập, không bị clone mẫu.
  - `.str.strip()`: Cắt bỏ mọi khoảng trắng vô tình bị gõ thừa ở đầu hoặc cuối chuỗi (ví dụ `" Male "` thành `"Male"`). Nếu không làm sạch, máy tính sẽ tưởng `"Male "` và `"Male"` là hai giới tính khác nhau!
  - **Lưu ý chuyên môn cực kỳ quan trọng:** Tuyệt đối không dùng `.astype(str)` trước khi strip, vì nếu cột có giá trị rỗng (NaN), lệnh đó sẽ biến `NaN` thành chuỗi ký tự `"nan"`, khiến giá trị khuyết bị "tàng hình" và không thể xử lý được!
- **Biểu đồ trong Cell [5]: Horizontal Bar Chart số lượng nhóm phân loại duy nhất (Unique Categories):**
  - Trục tung (Y): 6 biến phân loại (`Current_Car_Type`, `Gender`, `City_Type`, `Range_Anxiety_Level`, `Home_Charging_Possible`, `Subsidy_Available`).
  - Trục hoành (X): Số lượng nhóm phân loại duy nhất sau khi đã làm sạch khoảng trắng.
  - **Ý nghĩa biểu đồ:** Cho thấy các biến phân loại đều có không gian giá trị rất gọn gàng: `Current_Car_Type` có 4 nhóm, các biến khác có 3 hoặc 2 nhóm. Không có hiện tượng giá trị rác hay chính tả sai lệch (ví dụ xuất hiện nhóm thứ 5 kỳ lạ). Điều này báo hiệu bước mã hóa tiếp theo sẽ rất thuận lợi.

---

#### Cell [6] (Part 3 — Cell 3): Normalization & Categorical Encoding Architecture
*(Tương ứng Rubric Bước 4: Data Normalization & Bước 6: Categorical Encoding)*

```python
# 1. Thống kê biên độ dao động thang đo
scale_table = df_train_prep[num_features].agg(['min', 'max', 'mean', 'std']).T
scale_table['Range (Max - Min)'] = scale_table['max'] - scale_table['min']

# 2. Cấu hình Pipeline (Chỉ định nghĩa cấu hình, KHÔNG fit ở Ongoing 1)
ordinal_mapping = [['Low', 'Medium', 'High']]

preprocessor_pipeline = ColumnTransformer(
    transformers=[
        ('num_scaler', RobustScaler(), num_features),
        ('ord_encoder', OrdinalEncoder(categories=ordinal_mapping), ordinal_features),
        ('nom_encoder', OneHotEncoder(handle_unknown='ignore', sparse_output=False), nominal_features)
    ],
    remainder='drop'
)
```
- **Giải thích từng dòng code:**
  - `scale_table['Range'] = max - min`: Tính biên độ chênh lệch giữa giá trị lớn nhất và nhỏ nhất của từng thuộc tính số.
  - `ColumnTransformer`: Công cụ mạnh mẽ của `scikit-learn` cho phép áp dụng các phép biến đổi khác nhau lên các nhóm cột khác nhau trong cùng một luồng duy nhất.
  - `RobustScaler()`: Chuẩn hóa dữ liệu dựa trên Trung vị (Median) và Khoảng tứ phân vị (IQR). Nó tốt hơn `StandardScaler` ở chỗ **không bị méo mó khi gặp các giá trị thu nhập cực cao**.
  - `OrdinalEncoder(categories=[['Low', 'Medium', 'High']])`: Mã hóa biến thứ bậc `Range_Anxiety_Level`. Vì tâm lý lo lắng có thứ tự rõ ràng (`Low: 0` < `Medium: 1` < `High: 2`), nên dùng Ordinal để máy hiểu mức độ nghiêm trọng tăng dần.
  - `OneHotEncoder(handle_unknown='ignore')`: Tách các biến danh nghĩa (như giới tính, loại xe) thành các cột nhị phân (0 và 1). Tham số `handle_unknown='ignore'` là một tấm lá chắn an toàn: nếu tập test sau này xuất hiện một loại xe lạ chưa từng thấy, mô hình sẽ tự động gán toàn số 0 chứ không bị báo lỗi crash chương trình.
  - **QUY TẮC SỐNG CÒN — TẠI SAO KHÔNG GỌI `.fit()` Ở ĐÂY?**
    - Nếu bạn gọi `preprocessor_pipeline.fit(df_train_prep)` ngay lúc này, mô hình sẽ tính toán trung bình/độ lệch chuẩn trên toàn bộ 668.665 dòng.
    - Sau này ở Ongoing 2, khi bạn chia dữ liệu thành Train và Validation để thi cử, tập Validation đã vô tình bị "lộ" thông số từ trước. Đây gọi là **Rò rỉ dữ liệu (Data Leakage)**.
    - Do đó, ở Ongoing 1 ta **chỉ thiết kế bản vẽ kiến trúc Pipeline**. Việc thi công (`.fit()`) sẽ được trì hoãn sang Ongoing 2 bên trong từng fold huấn luyện!
- **Biểu đồ trong Cell [6]: Horizontal Bar Chart thang đo Logarithmic (Log-scale Range):**
  - Trục hoành (X): Biên độ giá trị (Max - Min) được vẽ trên **thang đo Logarit (10^0, 10^1, 10^2, 10^5)**.
  - **Ý nghĩa biểu đồ:** Biến `Annual_Income_USD` có Range lên tới $158.549, trong khi `Number_of_Cars_Owned` chỉ có Range = 3, `Charging_Stations` có Range = 14. Sự chênh lệch hàng nghìn lần này được hiển thị rõ mồn một. Biểu đồ này là câu trả lời đanh thép giải thích tại sao bắt buộc phải dùng Scaler cho các mô hình nhạy cảm khoảng cách như Logistic Regression hay KNN.

---

#### Cell [7] (Part 3 — Cell 4): Data Binning & Outliers/Noise Handling
*(Tương ứng Rubric Bước 5: Data Binning & Bước 7: Outliers & Noise Handling; tích hợp Task 3.4)*

```python
# 1. Tính toán ngưỡng IQR để phát hiện ngoại lệ thống kê
q1 = df_train_prep['Annual_Income_USD'].quantile(0.25)
q3 = df_train_prep['Annual_Income_USD'].quantile(0.75)
iqr = q3 - q1
upper_bound = q3 + 1.5 * iqr

# 2. Khảo sát thử nghiệm gom nhóm tuổi
age_bins = [0, 35, 50, 100]
age_labels = ['Young (<35)', 'Middle (35-50)', 'Senior (>50)']
age_binned = pd.cut(df_train_prep['Age'], bins=age_bins, labels=age_labels)
```
- **Giải thích từng dòng code:**
  - `quantile(0.25)` và `quantile(0.75)`: Tìm mốc phân vị 25% ($Q_1$) và 75% ($Q_3$). Khoảng nằm giữa là $IQR = Q_3 - Q_1$.
  - `upper_bound = q3 + 1.5 * iqr`: Công thức Tukey kinh điển trong thống kê để xác định ranh giới ngoại lệ trên. Bất kỳ ai có thu nhập vượt mốc $155.818,50 sẽ bị tính là điểm vượt ngưỡng thống kê (có 3.678 người như vậy).
  - `pd.cut(...)`: Lệnh chia một biến số liên tục thành các khoảng rời rạc (Binning).
- **Quyết định chuyên môn & Lập luận học thuật:**
  1. **Tại sao KHÔNG XÓA 3.678 người có thu nhập cao?**
     - Trong thống kê, vượt ngưỡng $1.5 \times IQR$ chỉ là cảnh báo phân phối lệch, **không đồng nghĩa là dữ liệu sai**!
     - Thu nhập $150.000 - $188.000 USD là hoàn toàn thực tế ngoài xã hội.
     - Quan trọng nhất về mặt kinh doanh: **Người có thu nhập cao chính là khách hàng tiềm năng số 1 mua xe điện!** Nếu ta dại dột xóa họ đi (Trimming) hoặc ép hạ thu nhập của họ xuống (Winsorizing/Capping), ta sẽ làm mất đi tín hiệu quan trọng nhất của bài toán kinh doanh.
  2. **Tại sao KHÔNG THỰC HIỆN Data Binning?**
     - Khi ta gom tuổi thành 3 nhóm (Trẻ, Trung niên, Già), một người 34 tuổi và một người 20 tuổi bị coi là giống hệt nhau, trong khi người 34 tuổi và 36 tuổi lại bị tách thành hai nhóm khác nhau.
     - Thao tác này gây ra hiện tượng **Mất mát thông tin (Information Loss)**. Các thuật toán hiện đại dạng cây (LightGBM, XGBoost) tự động tìm điểm cắt thông minh hơn con người rất nhiều, nên việc giữ nguyên biến liên tục là tối ưu nhất.
- **Biểu đồ trong Cell [7]: Bộ đôi Boxplot & Histogram/KDE của Thu nhập:**
  - **Nửa bên trái (Boxplot):** Cho thấy chiếc hộp thể hiện 50% dữ liệu ở giữa, một đường kẻ đứt màu đỏ thể hiện cận trên $155.818, và các chấm tròn màu đỏ kéo dài về phía bên phải thể hiện 3.678 khách hàng có thu nhập cao.
  - **Nửa bên phải (Histogram & KDE):** Thể hiện hình dạng đường cong phân phối thu nhập. Đường cong thoai thoải kéo dài về phía tay phải (Right-skewed Distribution).
  - **Ý nghĩa biểu đồ:** Chứng minh cho người đọc thấy rằng các điểm thu nhập cao tạo thành một dải phân phối liên tục, mượt mà tự nhiên, chứ không phải là những điểm dị biệt do lỗi đánh máy bấm thừa số 0.

---

#### Cell [8] (Part 3 — Cell 5): Data Integration & Feature Engineering
*(Tương ứng Rubric Bước 8: Data Integration & Bước 9: Feature Engineering)*

```python
# Tạo 3 đặc trưng phái sinh mới nhất quán trên cả train và test
for df in [df_train_prep, df_test_prep]:
    # Đặc trưng 1: Tổng số trạm sạc tiếp cận được quanh nhà và nơi làm việc
    df['Total_Charging_Access'] = df['Charging_Stations_Near_Home'] + df['Charging_Stations_Near_Work']
    
    # Đặc trưng 2: Tỷ lệ trạm sạc trên cự ly đi lại (+1 ổn định mẫu số)
    df['Charging_Commute_Ratio'] = (df['Total_Charging_Access'] / (df['Daily_Commute_km'] + 1)).round(4)
    
    # Đặc trưng 3: Thu nhập bình quân trên mỗi xe đang sở hữu
    df['Income_Per_Car'] = (df['Annual_Income_USD'] / df['Number_of_Cars_Owned']).round(2)
```
- **Giải thích từng dòng code & Ý nghĩa 3 đặc trưng:**
  1. `Total_Charging_Access`: Cộng trạm sạc gần nhà và gần cơ quan. Đây là hai nơi xe đỗ lâu nhất trong ngày. Tổng số này thể hiện **tổng mức độ an tâm về hạ tầng sạc** của khách hàng.
  2. `Charging_Commute_Ratio`: Lấy tổng trạm sạc chia cho `Daily_Commute_km + 1`. Người đi làm 50 km mà chỉ có 1 trạm sạc sẽ rất sợ hết pin; người đi 5 km mà có 10 trạm sạc thì tâm lý cực kỳ thoải mái.
     - **Hằng số `+ 1` để làm gì?** Đây là kỹ thuật toán học gọi là **Hằng số ổn định số học (Numerical Stability Constant)**. Nó phòng ngừa trường hợp ai đó có cự ly đi lại bằng 0 km (hoặc rất nhỏ gần 0), giúp mẫu số không bao giờ bị lỗi chia cho 0 (`ZeroDivisionError` hoặc sinh ra giá trị vô cực `Inf`).
  3. `Income_Per_Car`: Thu nhập chia cho số lượng xe sở hữu. Một gia đình kiếm được $100.000 nhưng đang nuôi 4 chiếc xe hơi thì gánh nặng tài chính sẽ lớn hơn rất nhiều so với người kiếm $100.000 mà chỉ có 1 xe. Đặc trưng này phản ánh **sức mua thực tế cho một phương tiện mới**.
  - `np.isinf(...).sum()` và `.isnull().sum()`: Kiểm tra lại toàn bộ dữ liệu xem có ô nào bị sinh ra giá trị NaN hoặc Inf không. Kết quả: **0 NaN, 0 Inf** trên cả Train và Test!
- **Biểu đồ trong Cell [8]: Bộ ba biểu đồ phân phối cho 3 đặc trưng mới:**
  - **Biểu đồ 1 (Histogram):** `Total_Charging_Access` có hình chuông cân đối tuyệt đẹp, đỉnh nằm ở mức 11–12 trạm sạc.
  - **Biểu đồ 2 (KDE Plot):** `Charging_Commute_Ratio` có phân phối lệch phải, thể hiện phần lớn mọi người có tỷ số thuận tiện sạc ở mức vừa phải, chỉ một số ít có tỷ lệ trạm sạc cực kỳ dồi dào.
  - **Biểu đồ 3 (Boxplot):** `Income_Per_Car` phân hóa mạnh mẽ từ $7.500 đến $188.549/xe, trung bình đạt gần $59.000/xe, tạo ra một đặc trưng tài chính có tính phân loại cực cao.

---

#### Cell [9] (Part 3 — Cell 6): Before/After Comparison & Export Verification
*(Tương ứng Rubric Bước 10: Exporting Prepared Data & tích hợp Task 3.5)*

```python
# 1. Xuất file ra thư mục riêng prepared_data/
PREPARED_DIR = Path('prepared_data')
PREPARED_DIR.mkdir(exist_ok=True)

df_train_prep.to_csv(PREPARED_DIR / 'train_prepared.csv', index=False)
df_test_prep.to_csv(PREPARED_DIR / 'test_prepared.csv', index=False)

# 2. Đọc lại ngay lập tức từ đĩa để kiểm định độc lập
df_train_reloaded = pd.read_csv(PREPARED_DIR / 'train_prepared.csv')
df_test_reloaded = pd.read_csv(PREPARED_DIR / 'test_prepared.csv')
```
- **Giải thích từng dòng code:**
  - `PREPARED_DIR.mkdir(exist_ok=True)`: Tự động tạo thư mục `prepared_data/` nếu chưa có.
  - `to_csv(..., index=False)`: Xuất bảng dữ liệu ra file CSV. Tham số `index=False` là bắt buộc để Pandas không tự ý chèn thêm một cột số thứ tự vô nghĩa (`Unnamed: 0`).
  - `pd.read_csv(...)`: Đọc ngược trở lại hai file vừa ghi xuống ổ cứng. Đây là bước kiểm định "nghiệm thu thực tế" (Sanity Check) để đảm bảo file lưu trên đĩa hoàn toàn lành lặn, không bị lỗi font hay mất mát dữ liệu.
- **Biểu đồ trong Cell [9]: Grouped Bar Chart So sánh số lượng cột Trước vs. Sau:**
  - Cột màu xám: Số lượng cột ban đầu (Train: 15 cột, Test: 14 cột).
  - Cột màu xanh lá: Số lượng cột sau khi xử lý (Train: 18 cột, Test: 17 cột).
  - **Ý nghĩa biểu đồ:** Minh chứng rõ ràng rằng dữ liệu đã được làm giàu thêm **đúng 3 đặc trưng mới (+3 features)** một cách nhất quán trên cả hai tập, trong khi bảo toàn 100% số lượng dòng quan sát.

---

### PART 4 — ĐÁNH GIÁ MỨC ĐỘ SẴN SÀNG & KIỂM ĐỊNH CUỐI CÙNG

#### Cell [10] — Export Quality Verification
- Thực hiện kiểm định lần cuối cùng về dung lượng file, số dòng, số cột, kiểm tra tỷ lệ nhãn mục tiêu và sự hiện diện của 3 đặc trưng mới.
- Khẳng định bộ dữ liệu đã đạt chuẩn **Prepared Data** và sẵn sàng 100% để bước sang Giai đoạn Ongoing 2 (EDA chuyên sâu, phân tầng Cross-Validation và huấn luyện các mô hình Machine Learning).

---

## 7. TÓM TẮT BỘ CÂU HỎI VẤN ĐÁP NHANH (CHEAT SHEET)

Dưới đây là các câu hỏi mà Giảng viên thường hỏi nhất khi chấm bài, cùng câu trả lời ngắn gọn, chuẩn xác:

| Câu hỏi của Giảng viên | Câu trả lời chuẩn xác & Thuyết phục |
| :--- | :--- |
| **1. Tại sao em không dùng Accuracy để đánh giá bài này?** | Dạ vì dữ liệu bị mất cân bằng nhãn nghiêm trọng (82,54% No và chỉ 17,46% Yes). Một mô hình phán bừa toàn bộ là "No" vẫn đạt Accuracy 82,54% nhưng vô dụng hoàn toàn vì không tìm ra được khách hàng mua xe nào. |
| **2. F1-Score khác gì với Accuracy?** | Dạ F1-Score là trung bình điều hòa giữa Precision và Recall trên lớp khách hàng mua xe ("Yes"). F1 chỉ cao khi mô hình vừa bắt trúng nhiều khách mua (Recall cao) vừa không phán bừa bãi gây lãng phí chi phí gọi điện (Precision cao). |
| **3. Trong bài này, giữa Precision và Recall em ưu tiên chỉ số nào hơn?** | Dạ em ưu tiên **Recall** hơn. Vì bỏ sót một khách mua xe điện (False Negative) làm đại lý mất hàng nghìn USD lợi nhuận; trong khi gọi nhầm cho một người không mua (False Positive) chỉ tốn một cuộc gọi vài nghìn đồng. |
| **4. ROC-AUC = 0.85 nghĩa là gì?** | Dạ nghĩa là nếu bốc ngẫu nhiên 1 người thực sự muốn mua và 1 người không mua, thì có 85% xác suất mô hình sẽ chấm điểm xác suất của người muốn mua cao hơn người không mua. Nó đo lường khả năng xếp hạng ưu tiên khách hàng của mô hình. |
| **5. Tại sao trong Ongoing 1 nhóm không fit Scaler và Encoder lên dữ liệu?** | Dạ để tuân thủ nguyên tắc chống rò rỉ dữ liệu (Anti-Data Leakage). Nếu fit trên toàn bộ dữ liệu bây giờ, thông số tập kiểm định sẽ bị lộ trước. Nhóm chỉ cấu hình Pipeline và sẽ fit cục bộ bên trong từng training fold của Cross-Validation ở Ongoing 2. |
| **6. Có 3.678 khách hàng thu nhập cao vượt ngưỡng IQR, tại sao nhóm không xóa?** | Dạ vì ngưỡng 1.5 * IQR chỉ là công cụ cảnh báo thống kê do phân phối thu nhập bị lệch phải tự nhiên. Thu nhập $150.000–$188.000 USD là hoàn toàn thực tế. Nhóm khách hàng giàu này chính là đối tượng tiềm năng số 1 mua xe điện, xóa đi sẽ gây sai lệch nghiêm trọng bài toán kinh doanh. |
| **7. Hằng số `+ 1` trong công thức `Charging_Commute_Ratio` để làm gì?** | Dạ đó là hằng số ổn định số học (numerical stability constant) nhằm ngăn ngừa mẫu số tiến tới 0 gây ra lỗi chia cho 0 (`ZeroDivisionError`) hoặc sinh ra giá trị vô cực (`Inf`). |
| **8. Tệp xuất ra đã sẵn sàng đưa vào mô hình hóa (Model-ready) chưa?** | Dạ chưa, tệp xuất ra là **Prepared Data** (đã làm sạch, chuẩn hóa định dạng và tạo đặc trưng). Dữ liệu sẽ trở thành **Model-ready Data** sau khi đi qua Pipeline biến đổi (Scaling & Encoding) bên trong từng fold huấn luyện của Ongoing 2. |

---
<h4 align="center"> Báo cáo được biên soạn độc quyền phục vụ dự án ADY201m — FPT University </h4>
