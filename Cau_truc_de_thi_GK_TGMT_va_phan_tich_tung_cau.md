# Cấu trúc đề thi giữa kỳ Thị giác máy tính và phân tích từng câu

Tài liệu ôn tập tổng hợp từ hai file Word, ba notebook và dữ liệu trong hai file ZIP được cung cấp. Phạm vi là dạng bài **phân lớp ảnh bằng CNN trên CIFAR-10**. Phần chung gồm 5 câu: xây dựng kiến trúc, nạp mô hình và dự đoán, đánh giá bằng chỉ số, trực quan hóa, viết báo cáo.

**Điểm cần nhớ:** học quy trình xử lý và cách đọc kết quả; khi nhận đề phải kiểm tra lại kiến trúc, thang điểm, số ảnh cần hiển thị và loại biểu đồ. Các tài liệu hiện có chứa những biến thể khác nhau, không phải một đề duy nhất với mọi thông số cố định.

## 1 Nguồn tài liệu và những khác biệt cần biết

### 1.1 Quy ước tên nguồn

| Ký hiệu | Tài liệu | Vai trò trong bản tổng hợp |
|---|---|---|
| T1 | `HuongDanThiGK-Final-HD (1).docx` | Hướng dẫn chuẩn bị, khung code và yêu cầu báo cáo Word |
| T2 | `ĐỀ THI GIỮA KỲ SỐ 1-THAM KHẢO (1).docx` | Đề tham khảo 60 phút, 10 điểm và phần lời giải mẫu |
| T3 | `ThiGK-TGMT-MSSV-SM-20250919T112636Z-1-001.zip` | Bộ test 20 ảnh; có hai notebook `ThiGK-TGMT-MSSV-SM.ipynb` và `TGK-TGMT-MSSV2-04.ipynb` |
| T4 | `ThiGK-TGMT-MSSV-SM (1).zip` | Bộ test 50 ảnh; có notebook `ThiGK-TGMT-MSSV-SM.ipynb` chứa biến thể kiến trúc và trực quan hóa |

Số ảnh, kiểu dữ liệu và phân bố nhãn dưới đây được kiểm tra trực tiếp từ các file `.npy`. Kiến trúc mô hình lưu được đối chiếu từ cấu hình trong `.h5`. Những accuracy và bảng chỉ số thực nghiệm được lấy từ output đã lưu trong notebook; bản tổng hợp này không huấn luyện lại hoặc chạy lại suy luận mô hình.

### 1.2 Đối chiếu các biến thể

| Nội dung | Đề Word T2 và hướng dẫn T1 | Notebook trong T4 |
|---|---|---|
| Kiến trúc ở Câu 1 | 2 khối; mỗi khối có 2 Conv2D và 1 MaxPooling2D | 3 khối; mỗi khối có 1 Conv2D, BatchNormalization và MaxPooling2D |
| Số filters | 32, 64 | 32, 64, 128 |
| Phần phân loại | Flatten, Dense 128, Dropout 0.5, Dense 10 softmax | Flatten, Dense 256, Dropout 0.3, Dense 10 softmax |
| Điểm Câu 1 đến Câu 5 | T2: **2 – 1 – 3 – 2 – 2** | **2 – 2 – 2 – 2 – 2** |
| Câu 3 | T2 yêu cầu confusion matrix, classification report và bộ chỉ số từng lớp | Confusion matrix và bộ chỉ số từng lớp; có macro và weighted average |
| Câu 4a | 8 ảnh, nhãn thật, nhãn dự đoán và dấu đúng/sai | 6 ảnh đầu, bố cục 2 × 3, thêm confidence |
| Câu 4b | Accuracy theo từng lớp | F1-score theo từng lớp, thêm đường macro-F1 |
| Câu 5 | T2 yêu cầu báo cáo 200–300 từ; T1 yêu cầu file Word | Báo cáo thêm macro-F1, lớp tốt/xấu theo F1 và top-2 cặp nhầm |

**Các điểm chưa thống nhất trong nguồn:**

- T1 vẫn ghi Câu 3 là 3 điểm nhưng khung chỉ thể hiện hai ý, mỗi ý 1 điểm; phần `classification_report` không được tách ra như T2. Khi ôn nên chuẩn bị đủ ba phần của T2; khi làm bài dùng phân điểm trên đề được phát.
- T1 ghi chuẩn bị 20 phút; T2 ghi chuẩn bị ở nhà 30 phút. Đây là thời lượng hướng dẫn, không phải cam kết thời gian huấn luyện trên mọi máy.
- Phần đề trong T2 nêu báo cáo 200–300 từ; phần lời giải cho phép cell văn bản hoặc Word, trong khi T1 yêu cầu Word. Nên chuẩn bị cách xuất báo cáo Word và làm theo hình thức nộp thực tế.
- T2 có nhắc Flowers-17 trong phần hướng dẫn giảng viên, nhưng không có một đề Flowers-17 hoàn chỉnh trong các file đã gửi. Vì vậy không tự suy ra kiến trúc hoặc thang điểm của đề đó.

### 1.3 Dữ liệu thực tế trong hai file ZIP

| Thuộc tính | Bộ 20 ảnh trong T3 | Bộ 50 ảnh trong T4 |
|---|---|---|
| Kích thước ảnh | `(20, 32, 32, 3)` | `(50, 32, 32, 3)` |
| Kiểu dữ liệu ảnh | `float32`, giá trị từ 0 đến 1 | `float32`, giá trị từ 0 đến 1 |
| Kích thước nhãn | `(20,)`, nhãn nguyên 0–9 | `(50,)`, nhãn nguyên 0–9 |
| Số ảnh theo nhãn 0–9 | `[1, 3, 2, 3, 0, 3, 0, 0, 5, 3]` | `[5, 5, 5, 5, 5, 5, 5, 5, 5, 5]` |
| Lớp không có ảnh thật | `deer`, `frog`, `horse` | Không có |
| Accuracy lưu trong notebook | 16/20 = 80% | 36/50 = 72% |

**Hai file `cifar10_model.h5` giống nhau hoàn toàn theo SHA-256.** Mô hình lưu trong cả hai ZIP có kiến trúc **2 khối**, Dense 128, Dropout 0.5, với 591.274 tham số.

Trong T4, mô hình mới xây ở Câu 1 có 3 khối và 621.258 tham số, nhưng Câu 2 lại nạp mô hình 2 khối từ `.h5`. Vì thế, **72% là kết quả của mô hình được nạp từ file, không phải kết quả đã huấn luyện của kiến trúc 3 khối ở Câu 1**. Khi viết báo cáo cần gọi đúng mô hình đang được đánh giá.

## 2 Cấu trúc đề thi chung

### 2.1 Khung năm câu

| Câu | Năng lực được kiểm tra | Sản phẩm cần có | Điểm theo T2 | Điểm theo T4 |
|---|---|---|---:|---:|
| 1 | Chuyển mô tả kiến trúc thành mạng CNN | Hàm xây dựng mô hình và `summary()` | 2 | 2 |
| 2 | Nạp đúng mô hình, dữ liệu và tạo dự đoán | `y_prob`, `y_pred`, accuracy và số ảnh đúng/tổng số ảnh | 1 | 2 |
| 3 | Đánh giá tổng thể và từng lớp | Confusion matrix, bảng precision/recall/F1/support; report nếu đề yêu cầu | 3 | 2 |
| 4 | Trình bày kết quả để người xem kiểm chứng | Lưới ảnh dự đoán và biểu đồ chỉ số từng lớp | 2 | 2 |
| 5 | Dùng số liệu để nhận xét và đề xuất | Báo cáo có dẫn chứng, giới hạn và hướng cải thiện | 2 | 2 |
| **Tổng** | | | **10** | **10** |

### 2.2 Mối liên hệ giữa các câu

Câu 1 cho thấy mình biết thiết kế mạng. Câu 2 đưa dữ liệu qua mô hình đã huấn luyện để tạo kết quả dự đoán. Câu 3 dùng kết quả đó để đo mô hình đúng, sai ở đâu. Câu 4 đưa các con số về hình ảnh và biểu đồ dễ quan sát. Câu 5 kết hợp số liệu với hình ảnh để giải thích kết quả và đề xuất cải thiện.

**Câu 2 là đầu mối dữ liệu của Câu 3–5.** Nếu nạp nhầm bộ test, đổi thứ tự ảnh và nhãn hoặc dự đoán bằng mô hình chưa huấn luyện thì các câu phía sau vẫn có thể chạy nhưng kết luận sẽ sai.

Trong các bài mẫu, `test_model = build_cnn_model()` ở Câu 1 và `model = load_model(...)` ở Câu 2 là hai đối tượng riêng. Xây dựng thành công `test_model` không có nghĩa mô hình đó đã học được cách phân loại ảnh.

### 2.3 Các thông số phải kiểm tra khi nhận đề

1. Số khối CNN, số Conv trong từng khối, filters và BatchNormalization.
2. Số units Dense, Dropout, số lớp đầu ra.
3. Tên file, đường dẫn, số ảnh và cách chuẩn hóa dữ liệu.
4. Có yêu cầu `classification_report` hay không; macro tính trên những lớp nào.
5. Số ảnh hiển thị, bố cục, yêu cầu confidence và màu heatmap.
6. Biểu đồ dùng accuracy theo lớp hay F1-score; có đường trung bình hay không.
7. Tiêu chí chọn lớp tốt/xấu, số cặp nhầm cần nêu và hình thức nộp báo cáo.

## 3 Chuẩn bị trước khi thi

Theo T1, tạo thư mục `ThiGK-TGMT-MSSV-SM` trên Google Drive, thay `MSSV` và `SM` bằng thông tin của mình. Ba file cần chuẩn bị là:

| File | Công dụng |
|---|---|
| `cifar10_model.h5` | Mô hình đã huấn luyện, gồm kiến trúc và trọng số |
| `new_test_samples.npy` | Ảnh đầu vào để đánh giá |
| `new_test_labels.npy` | Nhãn thật để đối chiếu dự đoán |

Lưu notebook làm bài cùng thư mục để dễ quản lý. Chuẩn bị báo cáo theo yêu cầu nộp của giảng viên. T1 hướng dẫn lấy hai file test từ LMS; không tự thay bằng bộ ảnh dễ hơn hoặc lấy ảnh huấn luyện để đánh giá.

**Các bước chuẩn bị mô hình:** tải CIFAR-10, tiền xử lý ảnh, xây đúng mạng, chọn loss phù hợp với nhãn, huấn luyện, lưu mô hình và thử nạp lại. Ví dụ trong T2 dùng Adam, learning rate 0.001, batch size 32 và 10 epochs; đây là cấu hình mẫu chuẩn bị, không phải thông số bắt buộc của mọi biến thể.

| Dạng nhãn khi huấn luyện | Loss tương ứng |
|---|---|
| Nhãn nguyên, ví dụ `3`, `5`, `8` | `sparse_categorical_crossentropy` |
| Nhãn one-hot dài 10 phần tử | `categorical_crossentropy` |

Để đánh giá bằng scikit-learn trong bài này, nhãn thật và nhãn dự đoán nên là hai mảng số nguyên một chiều. Nếu một bộ dữ liệu khác dùng nhãn one-hot thì đổi bằng `argmax(axis=1)`; không dùng `reshape(-1)` để biến ma trận one-hot thành nhãn.

**Lưu ý về đánh giá:** ví dụ T2 dùng tập test làm `validation_data`, rồi lấy mẫu từ chính tập đó để kiểm tra. Nếu đã dùng kết quả validation để chọn mô hình, tập này không còn là tập kiểm tra cuối cùng hoàn toàn độc lập. Khi đề xuất cải thiện quy trình, nên tách validation từ dữ liệu huấn luyện và giữ test cho đánh giá cuối.

## 4 Phân tích Câu 1 Xây dựng kiến trúc CNN

### 4.1 Đề thực sự muốn kiểm tra điều gì

Sinh viên phải đọc mô tả và dựng đúng thứ tự các lớp. Câu này chủ yếu chấm cấu trúc mạng, không yêu cầu chứng minh mạng vừa tạo đã đạt accuracy cao nếu đề chỉ yêu cầu xây kiến trúc.

Ảnh có kích thước `32 × 32 × 3`: chiều cao, chiều rộng và ba kênh màu. Đầu ra có 10 giá trị softmax, tương ứng 10 lớp. Thứ tự lớp trong toàn bài là:

```python
class_names = [
    'airplane', 'automobile', 'bird', 'cat', 'deer',
    'dog', 'frog', 'horse', 'ship', 'truck'
]
```

### 4.2 Ý nghĩa các lớp

| Thành phần | Hiểu ngắn gọn | Điểm cần nhớ |
|---|---|---|
| Conv2D | Học các bộ lọc nhận ra đặc trưng từ ảnh | Filters là số bản đồ đặc trưng đầu ra |
| ReLU | Tạo tính phi tuyến để mạng học quan hệ phức tạp | Được dùng trong các Conv và Dense ẩn ở bài mẫu |
| `padding='same'` | Với stride 1 trong bài, giữ kích thước không gian sau Conv | Không có nghĩa số kênh luôn giữ nguyên |
| MaxPooling2D 2 × 2 | Giảm kích thước không gian, giữ giá trị nổi bật trong vùng nhỏ | Với cấu hình mẫu: 32 → 16 → 8, hoặc thêm 4 ở mạng 3 khối |
| BatchNormalization | Chuẩn hóa activation theo cơ chế của lớp khi huấn luyện/suy luận | Chỉ thêm vào bài làm khi kiến trúc yêu cầu |
| Flatten | Trải khối đặc trưng thành vector | Không có tham số học |
| Dense | Kết hợp đặc trưng để phân loại | Số units phải đúng yêu cầu |
| Dropout | Bỏ ngẫu nhiên một phần activation trong lúc huấn luyện | Không có nghĩa xóa vĩnh viễn neuron; không bật ngẫu nhiên khi `predict()` thông thường |
| Dense 10 softmax | Tạo phân bố đầu ra trên 10 lớp | Lấy chỉ số giá trị lớn nhất để chọn một nhãn |

### 4.3 Hai kiến trúc cần phân biệt

| Giai đoạn | Biến thể 2 khối trong T2 | Biến thể 3 khối trong T4 |
|---|---|---|
| Đầu vào | 32 × 32 × 3 | 32 × 32 × 3 |
| Khối 1 | Conv32, Conv32, Pool → 16 × 16 × 32 | Conv32, BN, Pool → 16 × 16 × 32 |
| Khối 2 | Conv64, Conv64, Pool → 8 × 8 × 64 | Conv64, BN, Pool → 8 × 8 × 64 |
| Khối 3 | Không có | Conv128, BN, Pool → 4 × 4 × 128 |
| Flatten | 4.096 phần tử | 2.048 phần tử |
| Dense ẩn | 128, ReLU | 256, ReLU |
| Dropout | 0.5 | 0.3 |
| Đầu ra | 10, softmax | 10, softmax |

Có thể nhớ bằng câu: **đọc số khối trước, đọc cấu trúc trong mỗi khối sau, cuối cùng kiểm tra phần Dense và đầu ra**. Hai Conv trong một khối không đồng nghĩa với hai khối.

### 4.4 Khung code kiến trúc 2 khối

```python
from tensorflow import keras
from tensorflow.keras import layers

def build_cnn_model(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([keras.Input(shape=input_shape)])
    for filters in [32, 64]:
        model.add(layers.Conv2D(filters, 3, padding='same', activation='relu'))
        model.add(layers.Conv2D(filters, 3, padding='same', activation='relu'))
        model.add(layers.MaxPooling2D(2))
    model.add(layers.Flatten())
    model.add(layers.Dense(128, activation='relu'))
    model.add(layers.Dropout(0.5))
    model.add(layers.Dense(num_classes, activation='softmax'))
    return model

test_model = build_cnn_model()
test_model.summary()
```

Nếu đề dùng biến thể T4, thay phần xây khối bằng đoạn sau và đổi Dense thành 256, Dropout thành 0.3:

```python
for filters in [32, 64, 128]:
    model.add(layers.Conv2D(filters, 3, padding='same', activation='relu'))
    model.add(layers.BatchNormalization())
    model.add(layers.MaxPooling2D(2))
```

**Lỗi dễ mất điểm:** thiếu một Conv trong mỗi khối của T2; thêm BN vào sai biến thể; thiếu Flatten; nhầm Dropout; dùng số đầu ra khác 10; chỉ khai báo hàm mà không gọi hàm và in cấu trúc. Không tự đổi sang mạng mạnh hơn nếu câu hỏi yêu cầu một kiến trúc cụ thể.

**Cách kiểm tra:** đếm đúng số lớp, xem kích thước sau Pool, kiểm tra đầu ra `(None, 10)`. `None` là kích thước batch có thể thay đổi. Mạng 2 khối theo đúng cấu hình trên có 591.274 tham số; biến thể T4 có tổng 621.258 tham số, bao gồm tham số không huấn luyện của BN.

## 5 Phân tích Câu 2 Nạp mô hình và đánh giá

### 5.1 Yêu cầu và thứ tự xử lý

Đầu tiên xác định đúng thư mục và đủ ba file. Sau đó nạp mô hình đã huấn luyện, nạp ảnh và nhãn thật, kiểm tra dữ liệu, dự đoán xác suất, chuyển sang nhãn và tính accuracy.

| Biến | Nội dung | Kích thước trong bài |
|---|---|---|
| `X` | Ảnh test | `(N, 32, 32, 3)` |
| `y_true` | Nhãn thật | `(N,)` |
| `y_prob` | Đầu ra softmax của mô hình | `(N, 10)` |
| `y_pred` | Nhãn mô hình chọn | `(N,)` |
| `acc` | Tỉ lệ dự đoán đúng trên toàn bộ tập test | Một số từ 0 đến 1 |

`model.predict(X)` chưa trả về tên lớp. Mỗi ảnh có 10 giá trị đầu ra. `argmax(axis=1)` chọn vị trí lớn nhất trên từng hàng, từ đó thu được một nhãn cho mỗi ảnh.

### 5.2 Khung code cốt lõi

Đoạn dưới dùng sau khi đã kết nối Google Drive trong Colab và thay đường dẫn đúng. Các ví dụ Câu 3–5 sử dụng tiếp những biến tạo ở đây.

```python
from pathlib import Path
import numpy as np
from sklearn.metrics import accuracy_score

BASE_DIR = Path('/content/drive/MyDrive/ThiGK-TGMT-MSSV-SM')
model_path = BASE_DIR / 'cifar10_model.h5'
x_path = BASE_DIR / 'new_test_samples.npy'
y_path = BASE_DIR / 'new_test_labels.npy'

for path in [model_path, x_path, y_path]:
    assert path.exists(), f'Không thấy file: {path}'

model = keras.models.load_model(model_path, compile=False)
X = np.load(x_path, allow_pickle=False)
y_true = np.load(y_path, allow_pickle=False).astype(int).reshape(-1)

assert X.shape[1:] == (32, 32, 3)
assert len(X) == len(y_true) and len(X) > 0
assert np.all((0 <= y_true) & (y_true < len(class_names)))
print('Shape:', X.shape, y_true.shape)
print('Min/max:', X.min(), X.max())
print('Support:', np.bincount(y_true, minlength=len(class_names)))

# Hai bộ .npy đã cung cấp đều có ảnh float32 trong [0, 1].
# Không chia 255 thêm lần nữa.
y_prob = model.predict(X, verbose=0)
y_pred = y_prob.argmax(axis=1)
acc = accuracy_score(y_true, y_pred)
print(f'Accuracy = {acc:.4f} ({acc:.2%})')
print(f'Đúng {np.sum(y_true == y_pred)}/{len(y_true)} ảnh')
```

`compile=False` phù hợp ở đây vì chỉ nạp mô hình để dự đoán; các chỉ số được tính bên ngoài bằng scikit-learn. Nếu muốn gọi `fit()` hoặc `evaluate()` thì cần cấu hình compile phù hợp. Nạp mô hình đầy đủ từ `.h5` không yêu cầu xây lại kiến trúc Câu 1 trước đó. [K1]

### 5.3 Hiểu accuracy và tránh nhầm confidence

**Accuracy = số ảnh dự đoán đúng / tổng số ảnh test.** Với output mẫu: 16/20 = 80%, còn 36/50 = 72%.

Confidence của một ảnh là giá trị softmax ứng với lớp mô hình chọn, ví dụ `y_prob[i, y_pred[i]]`. Đây không phải accuracy của cả mô hình và không bảo đảm ảnh đó được nhận đúng. Một dự đoán sai vẫn có thể có confidence cao.

**Lỗi dễ mất điểm:** dùng `test_model` chưa huấn luyện để dự đoán; chia 255 lần hai; dùng `argmax(axis=0)`; đưa cả ma trận xác suất vào `accuracy_score`; tráo cặp file ảnh/nhãn giữa hai ZIP; xáo trộn ảnh nhưng không xáo trộn nhãn cùng thứ tự.

Không suy ra mô hình suy giảm từ 80% xuống 72% chỉ qua hai bộ mẫu khác nhau. Hai kết quả này đánh giá cùng file mô hình trên hai tập test có số lượng và thành phần khác nhau.

## 6 Phân tích Câu 3 Các chỉ số đánh giá

### 6.1 Confusion matrix cho biết sai ở đâu

Accuracy chỉ cho biết tổng số đúng. Confusion matrix cho biết ảnh thuộc lớp thật nào đã bị dự đoán thành lớp nào.

Trong cách gọi `confusion_matrix(y_true, y_pred)`, **hàng là lớp thật, cột là lớp dự đoán**. Ô đường chéo là số dự đoán đúng; ô ngoài đường chéo là số nhầm theo một hướng cụ thể. Nếu bỏ `labels`, scikit-learn chọn các nhãn xuất hiện trong nhãn thật hoặc nhãn dự đoán. Vì vậy cần cố định thứ tự đủ 10 nhãn khi trục hiển thị đủ 10 tên lớp. [K2]

```python
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.metrics import confusion_matrix

NUM_CLASSES = len(class_names)
labels = np.arange(NUM_CLASSES)
cm = confusion_matrix(y_true, y_pred, labels=labels)

plt.figure(figsize=(9, 7))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues',
            xticklabels=class_names, yticklabels=class_names)
plt.xlabel('Nhãn dự đoán')
plt.ylabel('Nhãn thật')
plt.title('Confusion matrix')
plt.xticks(rotation=45, ha='right')
plt.yticks(rotation=0)
plt.tight_layout()
plt.show()
```

Ví dụ `cm[6, 3] = 2` nghĩa là 2 ảnh thật thuộc lớp `frog` bị nhận thành `cat`. Điều này khác với `cm[3, 6]`, là chiều nhầm ngược lại.

**Tự kiểm tra:** `cm.shape == (10, 10)`; tổng tất cả ô bằng số ảnh; tổng đường chéo bằng số ảnh đúng; tổng mỗi hàng bằng support của lớp đó. Nếu không khớp thì chưa nên dùng biểu đồ để viết báo cáo.

### 6.2 Precision Recall F1 và support

Xét một lớp đang quan tâm: TP là số ảnh của lớp đó được nhận đúng; FP là ảnh lớp khác bị nhận thành lớp đó; FN là ảnh lớp đó bị nhận thành lớp khác.

| Chỉ số | Công thức | Câu hỏi giúp dễ nhớ |
|---|---|---|
| Precision | TP / (TP + FP) | Trong các ảnh mô hình gọi là lớp này, bao nhiêu ảnh thật sự đúng? |
| Recall | TP / (TP + FN) | Trong các ảnh thật của lớp này, mô hình tìm đúng bao nhiêu? |
| F1 | 2TP / (2TP + FP + FN) | Precision và recall có đồng thời tốt không? |
| Support | Số ảnh thật của lớp | Có bao nhiêu mẫu để đánh giá lớp này? |

Các định nghĩa và quy tắc trung bình được đối chiếu với tài liệu scikit-learn. [K3]

**Ví dụ trực tiếp từ bộ 50 ảnh:** lớp `cat` có 5 ảnh thật, nhận đúng 4 ảnh nhưng mô hình gọi tổng cộng 7 ảnh là `cat`. Vì vậy precision = 4/7 ≈ 0,5714, recall = 4/5 = 0,8, F1 ≈ 0,6667. Mô hình nhận được phần lớn ảnh mèo nhưng còn gọi nhầm một số ảnh khác thành mèo.

```python
from sklearn.metrics import classification_report, precision_recall_fscore_support

# T2 yêu cầu in report; giữ đủ 10 nhãn để tên lớp không lệch.
print(classification_report(
    y_true, y_pred, labels=labels, target_names=class_names,
    digits=4, zero_division=0
))

precision, recall, f1, support = precision_recall_fscore_support(
    y_true, y_pred, labels=labels, average=None, zero_division=0
)

present = support > 0
for i in labels[present]:
    print(class_names[i], precision[i], recall[i], f1[i], support[i])

macro_f1_all = f1.mean()
macro_f1_present = f1[present].mean()
weighted_f1 = np.average(f1, weights=support)
print('Macro-F1 đủ 10 lớp:', macro_f1_all)
print('Macro-F1 chỉ lớp có support > 0:', macro_f1_present)
print('Weighted-F1:', weighted_f1)
```

Đoạn code in bảng chi tiết cho lớp có mẫu theo hướng dẫn T1. Có thể in thêm các lớp còn lại, nhưng phải ghi `support = 0` và giải thích giới hạn đánh giá. `zero_division=0` đặt giá trị quy ước cho trường hợp phép chia không xác định; nó không chứng minh mô hình nhận sai mọi ảnh của một lớp không có mẫu.

### 6.3 Phân biệt cách lấy trung bình

| Cách tính | Ý nghĩa trong bài | Khi dùng |
|---|---|---|
| Macro đủ 10 lớp | Trung bình cộng chỉ số của 10 nhãn đã chỉ định | Khi đề yêu cầu đánh giá đủ 10 lớp; nêu rõ quy ước với lớp thiếu mẫu |
| Macro chỉ lớp có mẫu | Trung bình cộng sau khi lọc `support > 0` | Khi đề/hướng dẫn yêu cầu như T1 |
| Weighted | Trung bình theo trọng số support | Giúp phản ánh cơ cấu số ảnh trong tập test |

**Không xóa ảnh khỏi tập test để tính macro chỉ lớp có mẫu.** Vẫn tạo confusion matrix và chỉ số trên toàn bộ dự đoán; chỉ lọc các phần tử chỉ số khi lấy trung bình. Các lỗi dự đoán vào lớp không có ảnh thật vẫn là lỗi và vẫn xuất hiện trên confusion matrix.

Với bộ 20 ảnh, ba lớp `deer`, `frog`, `horse` có support bằng 0. Từ các chỉ số trong notebook:

| Chỉ số | Giá trị |
|---|---:|
| Accuracy | 0,8000 |
| Macro-F1 đủ 10 lớp với quy ước trên | 0,5790 |
| Macro-F1 chỉ 7 lớp có mẫu | 0,8272 |
| Weighted-F1 | 0,8352 |

Hai giá trị macro khác nhau vì tập lớp được lấy trung bình khác nhau. Không ghi một giá trị là “macro-F1” mà bỏ qua quy ước đang dùng. Macro-F1 được tính bằng trung bình F1 từng lớp, không phải lấy công thức F1 áp vào macro-precision và macro-recall.

Với bộ 50 ảnh, mỗi lớp đều có 5 mẫu nên macro và weighted của cùng một chỉ số bằng nhau. Macro-F1 trong output là 0,7171.

### 6.4 Lỗi cụ thể trong notebook mẫu 20 ảnh

Có ba vấn đề nên hiểu trước khi dùng notebook để ôn:

1. **Ma trận không đủ 10 × 10 nhưng trục lại gắn 10 tên lớp.** Code bỏ `labels=range(10)`, nên ma trận đã lưu chỉ có 9 × 9. Lớp `horse` không xuất hiện cả trong nhãn thật lẫn dự đoán; hai lớp thiếu ảnh thật khác là `deer` và `frog` vẫn xuất hiện ở dự đoán. Danh sách nhãn thực sự của ma trận là `[0, 1, 2, 3, 4, 5, 6, 8, 9]`, khiến tên các trục phía sau bị lệch.
2. **Phần tìm cặp nhầm tự ánh xạ bằng các lớp có support > 0.** Tập này chỉ có 7 lớp, khác với 9 nhãn thực sự của ma trận. Vì thế tên cặp nhầm được in trong báo cáo không đáng tin. Cách sửa là luôn tạo ma trận đủ 10 lớp rồi dùng trực tiếp chỉ số `cm[i, j]`.
3. **Output báo cáo của `TGK-TGMT-MSSV2-04.ipynb` chưa đồng bộ.** Cell tính macro trên lớp có mẫu in F1 = 0,8272, nhưng output cell báo cáo phía sau vẫn ghi 0,5790. Cần chạy lại tuần tự sau khi sửa code, không chép số từ hai lần chạy khác nhau.

Đọc các ô số trên ma trận đã lưu theo đúng thứ tự nhãn, bốn lỗi của bộ 20 ảnh là **automobile → truck, bird → deer, cat → frog và dog → cat**, mỗi cặp 1 ảnh. Đây là cách sửa cách đọc output cũ, không phải kết quả của một lần chạy dự đoán mới.

## 7 Phân tích Câu 4 Trực quan hóa

### 7.1 Hiển thị ảnh và dự đoán

Mục tiêu là cho người xem đối chiếu ảnh thật với quyết định của mô hình. Mỗi ô cần có ảnh, nhãn thật, nhãn dự đoán và dấu đúng/sai. Với biến thể T4, thêm confidence cho nhãn dự đoán.

| Yêu cầu | T2 | T4 |
|---|---|---|
| Số ảnh | 8 | 6 ảnh đầu |
| Bố cục trong bài mẫu | 2 × 4 | 2 × 3 |
| Đánh dấu | Đúng/sai, thường xanh/đỏ | Đúng/sai, xanh/đỏ, kèm confidence |

```python
n_rows, n_cols = 2, 4   # T4: đổi thành 2, 3
show_confidence = False  # T4: đổi thành True
n_show = min(n_rows * n_cols, len(X))
fig, axes = plt.subplots(n_rows, n_cols, figsize=(3.5*n_cols, 3.5*n_rows))

for i, ax in enumerate(np.asarray(axes).ravel()):
    ax.axis('off')
    if i >= n_show:
        continue
    correct = y_true[i] == y_pred[i]
    pred_text = class_names[y_pred[i]]
    if show_confidence:
        pred_text += f' ({y_prob[i, y_pred[i]]:.1%})'
    ax.imshow(X[i])
    ax.set_title(
        f'Thật: {class_names[y_true[i]]}\nDự đoán: {pred_text}\n'
        f'{"Đúng" if correct else "Sai"}',
        color='green' if correct else 'red'
    )
plt.tight_layout()
plt.show()
```

**Lỗi dễ gặp:** ảnh và nhãn lấy từ hai thứ tự khác nhau; hard-code 8 ảnh khi đề yêu cầu 6; quên lấy tên lớp; đưa giá trị confidence của lớp thật thay vì lớp dự đoán; coi confidence cao là bằng chứng chắc chắn dự đoán đúng.

Trong T4 có một ảnh `dog` bị dự đoán thành `deer` với confidence lưu là khoảng 99,6%. Ví dụ này cho thấy cần kiểm tra nhãn thật để xác định đúng/sai.

### 7.2 Biểu đồ chỉ số từng lớp

**Dạng A — accuracy theo lớp trong T2:** lọc những ảnh có nhãn thật bằng lớp đang xét, rồi tính tỉ lệ dự đoán đúng trong nhóm đó. Theo định nghĩa này, giá trị chính là **recall của lớp**, bằng số đúng trên hàng confusion matrix chia tổng hàng. Nó không phải accuracy nhị phân one-vs-rest có cộng cả true negative.

**Dạng B — F1-score theo lớp trong T4:** dùng mảng F1 của Câu 3, vẽ đủ tên lớp và thêm đường macro-F1 nếu đề yêu cầu. F1 phản ánh cả việc bỏ sót ảnh của lớp và việc nhận nhầm ảnh khác thành lớp đó.

```python
metric = 'class_accuracy'  # T4: đổi thành 'f1'
values = recall.copy() if metric == 'class_accuracy' else f1.copy()
values[~present] = np.nan  # Không diễn giải lớp thiếu mẫu như một lớp yếu

fig, ax = plt.subplots(figsize=(11, 5))
heights = np.nan_to_num(values, nan=0.0)
bars = ax.bar(class_names, heights)

for i, bar in enumerate(bars):
    text = f'{values[i]:.2f}\n(n={support[i]})' if present[i] else 'N/A\n(n=0)'
    ax.text(bar.get_x()+bar.get_width()/2, bar.get_height()+0.02,
            text, ha='center', fontsize=9)

if metric == 'f1':
    # Quy ước ở đây: trung bình trên các lớp có mẫu.
    # Với T4 đủ 10 lớp, giá trị bằng macro-F1 đủ 10 lớp.
    mean_f1 = f1[present].mean()
    ax.axhline(mean_f1, color='red', linestyle='--',
               label=f'Macro-F1 lớp có mẫu = {mean_f1:.4f}')
    ax.legend()

ax.set_ylabel('Accuracy theo lớp' if metric == 'class_accuracy' else 'F1-score')
ax.set_title('Kết quả theo từng lớp')
ax.set_ylim(0, 1.2)
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()
```

Nhãn `N/A` trong đoạn minh họa thể hiện không có ảnh thật để so sánh hiệu quả lớp đó. Nếu đề buộc vẽ giá trị số theo quy ước `zero_division=0`, vẫn có thể hiện 0 nhưng phải kèm `n=0`, không kết luận đây là lớp dự đoán kém nhất. Không thay đổi mảng F1 gốc khi làm biểu đồ; vẫn giữ nguyên để tính các chỉ số theo đúng quy ước của đề.

**Điều cần giải thích:** tại sao một lớp cao hoặc thấp, dựa vào bao nhiêu mẫu, và biểu đồ đang dùng chỉ số gì. Trong bộ 50 ảnh, recall của `ship` và `truck` đều bằng 1, nhưng F1 của `truck` thấp hơn vì có ảnh lớp khác bị dự đoán thành `truck`.

## 8 Phân tích Câu 5 Báo cáo và đề xuất

### 8.1 Một báo cáo đầy đủ cần những gì

| Phần | Nội dung cần nêu | Dữ liệu lấy từ |
|---|---|---|
| Kết quả tổng thể | Số ảnh đúng/tổng số ảnh, accuracy; macro-F1 nếu yêu cầu | Câu 2 và Câu 3 |
| Lớp tốt và yếu | Tên lớp, chỉ số cụ thể, số mẫu; xử lý đồng hạng | Bảng metrics và biểu đồ |
| Nhầm lẫn chính | Lớp thật → lớp dự đoán, số ảnh nhầm | Ô ngoài đường chéo của confusion matrix |
| Giải thích và cải thiện | Nguyên nhân có thể, cách kiểm tra và biện pháp phù hợp | Ảnh sai, chỉ số, kiến thức mô hình |
| Giới hạn | Tập test nhỏ, lớp thiếu mẫu, mức độ khái quát kết luận | Phân bố dữ liệu |

Không chỉ liệt kê con số. Mỗi nhận xét nên có cấu trúc: **kết quả quan sát được → ý nghĩa → hướng xử lý**.

Ví dụ: “Lớp dog có recall 0,4, nghĩa là chỉ nhận đúng 2/5 ảnh. Ba ảnh còn lại bị nhầm sang cat, deer và frog. Cần xem các ảnh sai để kiểm tra ảnh hưởng của hình dáng, nền và độ phân giải trước khi lựa chọn tăng cường dữ liệu phù hợp.”

### 8.2 Chọn lớp tốt nhất và yếu nhất cho đúng

Trước hết xác định tiêu chí: accuracy theo lớp/recall hay F1. Sau đó chỉ xếp hạng những lớp có mẫu, báo cáo các lớp đồng hạng và nêu support. `argmax()` hoặc `argmin()` đơn lẻ chỉ trả về một vị trí đầu tiên, nên có thể bỏ sót đồng hạng.

| Bộ mẫu | Theo recall trên các lớp có mẫu | Theo F1 trên các lớp có mẫu |
|---|---|---|
| 20 ảnh | Cao nhất: airplane, ship, truck cùng 1,0; thấp nhất: bird 0,5 | Cao nhất: airplane và ship cùng 1,0; thấp nhất: bird và cat cùng khoảng 0,6667 |
| 50 ảnh | Cao nhất: ship và truck cùng 1,0; thấp nhất: dog 0,4 | Cao nhất: ship 1,0; thấp nhất: dog 0,5 |

Lớp airplane trong bộ 20 ảnh chỉ có 1 ảnh. Tỉ lệ đúng 100% ở 1/1 ảnh chưa đủ để khẳng định lớp đó dễ nhất nói chung.

### 8.3 Tìm cặp nhầm nhiều nhất

Lấy các ô ngoài đường chéo có giá trị lớn hơn 0, sắp xếp giảm dần, rồi chọn top-K theo đề. Có thể tính trực tiếp từ ma trận đủ 10 nhãn:

```python
errors = [
    (int(cm[i, j]), class_names[i], class_names[j])
    for i in labels for j in labels
    if i != j and cm[i, j] > 0
]
errors.sort(key=lambda item: (-item[0], item[1], item[2]))

top_k = 2
# Giữ các cặp đồng hạng ở ngưỡng top-K, như cách trình bày trong T4.
if errors:
    threshold = errors[min(top_k, len(errors)) - 1][0]
    for count, true_name, pred_name in errors:
        if count < threshold:
            break
        print(f'{true_name} → {pred_name}: {count} ảnh')
else:
    print('Không có ảnh dự đoán sai.')
```

Nếu đề yêu cầu đúng K cặp, lấy `errors[:top_k]` và nêu có đồng hạng khi cần. Không lấy ô đường chéo; không gộp hai hướng A → B và B → A nếu chưa nói rõ đang đổi cách đếm.

Với T4, hai cặp đứng đầu là `automobile → airplane` và `frog → cat`, mỗi cặp 2 ảnh. Với T3, bốn cặp nhầm đều có 1 ảnh, vì vậy top-2 không có hai cặp vượt trội duy nhất.

### 8.4 Đề xuất phải gắn với vấn đề quan sát được

| Quan sát hoặc giới hạn | Hướng cải thiện hợp lý | Cách kiểm chứng |
|---|---|---|
| Một số lớp ít hoặc không có ảnh test | Đánh giá trên tập lớn hơn, đủ lớp | Kiểm tra support và độ ổn định chỉ số qua nhiều mẫu |
| Nhiều ảnh động vật bị nhầm | Xem ảnh sai; thử augmentation phù hợp với ảnh và nhãn | So sánh confusion matrix và F1 từng lớp trên validation |
| Nghi ngờ overfitting | Xem đường train/validation; cân nhắc regularization, augmentation, early stopping | Khoảng cách kết quả train và validation |
| Mô hình học chưa đủ | Điều chỉnh lịch learning rate hoặc số epoch | Theo dõi validation thay vì chỉ nhìn training accuracy |
| Cần thử biểu diễn đặc trưng tốt hơn | Thử kiến trúc khác hoặc transfer learning nếu phạm vi bài cho phép | Dùng cùng quy trình đánh giá và tập test cuối độc lập |

Không thể kết luận overfitting chỉ từ một accuracy trên 20 hoặc 50 ảnh. Cũng không nên khẳng định ảnh bị nhầm vì “màu lông giống nhau”, “nền giống nhau” nếu chưa xem ảnh đó. Đây có thể là giả thuyết để kiểm tra, không phải điều confusion matrix tự chứng minh.

### 8.5 Khung viết báo cáo 200 đến 300 từ

Triển khai thành 4 đoạn ngắn, thay bằng kết quả thực tế:

1. **Đoạn 1:** mô hình được đánh giá, bộ test, số ảnh đúng, accuracy và macro-F1 nếu yêu cầu; nêu quy ước macro.
2. **Đoạn 2:** lớp tốt/yếu theo đúng chỉ số được chọn, có số liệu và support; giải thích ngắn sự khác nhau giữa precision và recall nếu cần.
3. **Đoạn 3:** 2–3 cặp nhầm chính hoặc số cặp đề yêu cầu; nêu chiều nhầm, số lần và giả thuyết nguyên nhân dựa trên ảnh đã xem.
4. **Đoạn 4:** 2–3 đề xuất sát lỗi quan sát, cách kiểm chứng và giới hạn tập test nhỏ.

Trước khi viết, chạy lại các cell tính toán cần thiết để thống nhất số liệu. Không dùng output 80% của bộ 20 ảnh cùng biểu đồ F1 của bộ 50 ảnh trong một báo cáo.

## 9 Những lỗi cần sửa hoặc tránh khi học từ bài mẫu

| Vấn đề | Hậu quả | Cách xử lý |
|---|---|---|
| Chép kiến trúc 3 khối vào đề yêu cầu 2 khối | Chạy được nhưng sai yêu cầu Câu 1 | Đối chiếu từng lớp với đề |
| Gọi 72% là kết quả của mạng 3 khối | Gán kết quả cho mô hình chưa được đánh giá | Nêu đúng mô hình nạp từ `.h5` |
| Chia 255 lần nữa cho hai bộ `.npy` đã chuẩn hóa | Đầu vào khác thang đo lúc huấn luyện | Kiểm tra dtype, min/max và quy trình tiền xử lý |
| Ma trận tự suy ra nhãn nhưng trục gắn đủ 10 tên | Tên lớp bị lệch và nhận xét sai | Truyền `labels=range(10)` |
| Tự ánh xạ ma trận bằng các lớp có support > 0 | Bỏ sót nhãn chỉ có trong dự đoán | Giữ ma trận đầy đủ, dùng chỉ số lớp trực tiếp |
| Xem support 0 là lớp dự đoán kém nhất | Kết luận khi không có mẫu thật để đánh giá | Đánh dấu thiếu mẫu và loại khỏi xếp hạng lớp |
| Trộn macro đủ lớp với macro lớp có mẫu | Bảng và báo cáo mâu thuẫn | Ghi rõ quy ước và tính nhất quán |
| Output còn lưu từ lần chạy trước | Code hiện tại và báo cáo không khớp | Khởi động lại rồi chạy tuần tự khi kiểm tra bài |
| Chọn lớp tốt/xấu bằng một `argmax`/`argmin` | Bỏ sót đồng hạng | So sánh tất cả giá trị bằng cực trị |
| Kết luận nguyên nhân chỉ từ confusion matrix | Suy diễn vượt dữ liệu | Xem ảnh sai và phân biệt quan sát với giả thuyết |

Notebook `ThiGK-TGMT-MSSV-SM.ipynb` trong T3 còn có một cell kết nối Drive trống phần code nhưng giữ output cũ, và không có phần dựng kiến trúc Câu 1 trong nguồn hiện tại. Vì vậy không nên coi notebook này là bài hoàn chỉnh có thể chạy lại từ đầu; dùng T1, T2 và notebook `TGK-TGMT-MSSV2-04.ipynb` để đối chiếu phần còn thiếu.

## 10 Phân bổ thời gian và tự kiểm tra trước khi nộp

### 10.1 Mốc thời gian tham khảo

T2 gợi ý Câu 1–5 lần lượt 15, 8, 20, 12 và 5 phút. Có thể điều chỉnh để dành thời gian kiểm tra cuối như sau; đây là đề xuất ôn tập, không phải quy định thi:

| Công việc | Thời lượng đề xuất |
|---|---:|
| Đọc đề, xác định biến thể và file | 3 phút |
| Câu 1 | 10 phút |
| Câu 2 | 7 phút |
| Câu 3 | 15 phút |
| Câu 4 | 10 phút |
| Câu 5 | 10 phút |
| Kiểm tra và lưu bài | 5 phút |
| **Tổng** | **60 phút** |

T2 còn nêu tiêu chí tổng quát: code hoạt động 60%, kết quả chính xác 25%, phân tích và báo cáo 15%. Đây là định hướng đánh giá được ghi trong tài liệu tham khảo; thang điểm từng ý vẫn theo đề phát khi thi.

### 10.2 Checklist

- [ ] Họ tên, MSSV, số máy và đường dẫn đã thay đúng.
- [ ] Kiến trúc khớp số khối, số Conv, filters, Dense, Dropout và đầu ra.
- [ ] Dùng mô hình đã huấn luyện cho dự đoán; phân biệt với mô hình mới dựng ở Câu 1.
- [ ] Ảnh và nhãn thuộc cùng bộ test, cùng số lượng và đúng thứ tự.
- [ ] Không chuẩn hóa lặp lại; kích thước và thang giá trị phù hợp mô hình.
- [ ] `y_prob` là `(N, 10)`, `y_pred` và `y_true` là `(N,)`.
- [ ] Accuracy khớp số đúng/tổng số ảnh và đường chéo confusion matrix.
- [ ] Confusion matrix có nhãn đúng, đủ 10 × 10 khi hiển thị đủ 10 lớp.
- [ ] Có precision, recall, F1, support và report nếu đề yêu cầu.
- [ ] Quy ước macro được ghi rõ; lớp không có mẫu không bị gọi là lớp yếu nhất.
- [ ] Số ảnh, bố cục và loại biểu đồ đúng biến thể.
- [ ] Báo cáo dùng cùng kết quả tính toán, xử lý đồng hạng và nêu giới hạn dữ liệu.
- [ ] Notebook chạy lại từ đầu, hình và bảng còn hiển thị sau khi lưu; báo cáo được lưu đúng hình thức yêu cầu.

### 10.3 Năm câu tự hỏi để nhớ toàn bộ bài

| Câu thi | Câu hỏi cần trả lời |
|---|---|
| Câu 1 | Mạng được xây như thế nào? |
| Câu 2 | Mô hình đã học dự đoán đúng bao nhiêu ảnh? |
| Câu 3 | Mô hình đúng, sai ở những lớp nào và theo chỉ số nào? |
| Câu 4 | Có thể nhìn thấy các kết quả đó qua ảnh và biểu đồ không? |
| Câu 5 | Từ bằng chứng trên, rút ra nhận xét và đề xuất gì? |

## 11 Tài liệu đối chiếu kỹ thuật

Các yêu cầu đề thi và kết quả mẫu lấy từ T1–T4 ở mục 1. Tài liệu chính thức dưới đây chỉ dùng đối chiếu cách dùng API và định nghĩa chỉ số, không bổ sung yêu cầu thi mới:

- **[K1]** [Keras — Whole model saving and loading](https://keras.io/2/api/models/model_saving_apis/model_saving_and_loading/)
- **[K2]** [scikit-learn — Confusion matrix](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.confusion_matrix.html)
- **[K3]** [scikit-learn — Precision recall F-score support](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_recall_fscore_support.html)

Ngày tổng hợp: 02/10/2026. Các đoạn code là khung minh họa để ôn theo từng câu; thông số và hình thức nộp phải đối chiếu với đề thi cụ thể.
