# Code kiến trúc CNN P01–P30 — TensorFlow/Keras

Mã triển khai theo đúng P01–P30 trong **Du_doan_100_kien_truc_Cau_1_CNN.md**. Mỗi mục có một hàm độc lập; các lớp được viết trực tiếp theo thứ tự để dễ chép và sửa theo đề.

Đã kiểm tra cả 30 hàm với TensorFlow 2.20.0 / Keras 3.15.1: dựng mô hình, chạy dữ liệu giả qua mạng và đối chiếu số tham số. Đầu ra của mỗi mẫu là `(batch_size, 10)` với cấu hình mặc định.

## Import dùng chung

Chạy phần này một lần trước khi chạy hàm xây dựng bất kỳ mô hình nào.

```python
from tensorflow import keras
from tensorflow.keras import layers, regularizers

keras.backend.set_image_data_format("channels_last")
```

Mặc định: input **32×32×3**, output **10 lớp softmax**. Mọi Conv dùng kernel 3×3, stride 1, padding same; MaxPool dùng 2×2, stride 2, valid. Conv và Dense giữ bias mặc định để khớp số tham số của bản dự đoán.

Mỗi hàm trả về mô hình đã dựng kiến trúc. Chạy phần import và hàm cần dùng; ví dụ gọi hàm đặt ở cuối tài liệu. Số tham số ghi dưới đây tính cho input và số lớp mặc định, gồm cả thống kê không huấn luyện của BN.

**P15 có 1.310.154 tham số**, giữ đúng cấu hình của bản dự đoán. Các mẫu còn lại trong P01–P30 đều dưới 1 triệu tham số.

## Mục lục nhanh

| Mã | Kiến trúc | Hàm |
|---|---|---|
| P01 | Mẫu Câu 1: 2 block, mỗi block 2 Conv | `build_p01` |
| P02 | Mẫu Câu 1: 3 block, mỗi block 1 Conv + BN | `build_p02` |
| P03 | CNN bản demo | `build_p03` |
| P04 | CNN chi tiết có augmentation 0.1 và BN | `build_p04` |
| P05 | CNN Flatten trong lab so sánh | `build_p05` |
| P06 | CNN GAP trong lab so sánh | `build_p06` |
| P07 | CNN FullCIFAR: 6 Conv, 3 block | `build_p07` |
| P08 | ShallowNet 32 filters | `build_p08` |
| P09 | ShallowNet 16 filters | `build_p09` |
| P10 | ShallowNet 64 filters | `build_p10` |
| P11 | 2 block, mỗi block chỉ 1 Conv | `build_p11` |
| P12 | 3 block đôi 16–32–64 | `build_p12` |
| P13 | 3 block đơn 16–32–64 | `build_p13` |
| P14 | CNN nhỏ 16–32 | `build_p14` |
| P15 | CNN rộng 64–128 | `build_p15` |
| P16 | 2 block, mỗi block 3 Conv | `build_p16` |
| P17 | Số Conv không đều: 2–1–1 | `build_p17` |
| P18 | Số Conv không đều: 1–2–2 | `build_p18` |
| P19 | 4 block đơn 16–32–64–128 | `build_p19` |
| P20 | 4 block đôi và GAP | `build_p20` |
| P21 | 3 block đơn với BN trước ReLU | `build_p21` |
| P22 | 2 block đôi với BN sau ReLU | `build_p22` |
| P23 | 3 block đôi với BN trước ReLU | `build_p23` |
| P24 | 3 block đôi với BN sau ReLU | `build_p24` |
| P25 | Mỗi block chỉ có một BN ở cuối phần Conv | `build_p25` |
| P26 | BN đặt sau pooling | `build_p26` |
| P27 | Chỉ block 1 có BN | `build_p27` |
| P28 | Chỉ block 2 có BN | `build_p28` |
| P29 | BN sau Dense có ReLU | `build_p29` |
| P30 | Dense tuyến tính → BN → ReLU | `build_p30` |


## P01 — Mẫu Câu 1: 2 block, mỗi block 2 Conv

2 block 32–64; mỗi block 2 Conv và 1 MaxPool; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 591.274.

```python
def build_p01(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P01")
    return model
```

## P02 — Mẫu Câu 1: 3 block, mỗi block 1 Conv + BN

3 block 32–64–128; mỗi block Conv có ReLU → BN → MaxPool; Dense 256, Dropout 0.3.

**Tổng tham số mặc định:** 621.258.

```python
def build_p02(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 32 filters; Conv-ReLU → BN
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 64 filters; Conv-ReLU → BN
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 3: 1 Conv, 128 filters; Conv-ReLU → BN
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P02")
    return model
```

## P03 — CNN bản demo

2 block 32–64, mỗi block 2 Conv; Dense 128, Dropout 0.3.

**Tổng tham số mặc định:** 591.274.

```python
def build_p03(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P03")
    return model
```

## P04 — CNN chi tiết có augmentation 0.1 và BN

Augmentation A10; 2 block 32–64, mỗi Conv theo thứ tự Conv → BN → ReLU; Flatten, Dense 128, Dropout 0.3.

**Tổng tham số mặc định:** 592.042.

```python
def build_p04(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Augmentation nằm trong model
        layers.RandomFlip("horizontal"),
        layers.RandomRotation(0.1),
        layers.RandomZoom(0.1),

        # Block 1: 2 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P04")
    return model
```

## P05 — CNN Flatten trong lab so sánh

Augmentation A15; 2 block 32–64 có BN trước ReLU; Flatten, Dense 128, Dropout 0.3.

**Tổng tham số mặc định:** 592.042.

```python
def build_p05(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Augmentation nằm trong model
        layers.RandomFlip("horizontal"),
        layers.RandomRotation(0.15),
        layers.RandomZoom(0.15),

        # Block 1: 2 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P05")
    return model
```

## P06 — CNN GAP trong lab so sánh

Cùng backbone P05; thay bằng GAP, Dense 128 có L2=1e-4, Dropout 0.4.

**Tổng tham số mặc định:** 75.946.

```python
def build_p06(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Augmentation nằm trong model
        layers.RandomFlip("horizontal"),
        layers.RandomRotation(0.15),
        layers.RandomZoom(0.15),

        # Block 1: 2 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.GlobalAveragePooling2D(),
        layers.Dense(128, activation="relu",
                     kernel_regularizer=regularizers.l2(1e-4)),
        layers.Dropout(0.4),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P06")
    return model
```

## P07 — CNN FullCIFAR: 6 Conv, 3 block

3 block 32–64–128, mỗi block 2 Conv; Flatten, Dense 256, Dropout 0.5.

**Tổng tham số mặc định:** 814.122.

```python
def build_p07(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P07")
    return model
```

## P08 — ShallowNet 32 filters

Một Conv 32 filters, ReLU, Flatten rồi phân loại trực tiếp 10 lớp.

**Tổng tham số mặc định:** 328.586.

```python
def build_p08(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Một lớp Conv của ShallowNet
        layers.Conv2D(32, 3, padding="same", activation='relu'),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P08")
    return model
```

## P09 — ShallowNet 16 filters

Thay Conv duy nhất của ShallowNet bằng 16 filters; giữ nguyên phần còn lại.

**Tổng tham số mặc định:** 164.298.

```python
def build_p09(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Một lớp Conv của ShallowNet
        layers.Conv2D(16, 3, padding="same", activation='relu'),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P09")
    return model
```

## P10 — ShallowNet 64 filters

Thay Conv duy nhất của ShallowNet bằng 64 filters; giữ nguyên phần còn lại.

**Tổng tham số mặc định:** 657.162.

```python
def build_p10(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Một lớp Conv của ShallowNet
        layers.Conv2D(64, 3, padding="same", activation='relu'),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P10")
    return model
```

## P11 — 2 block, mỗi block chỉ 1 Conv

2 block 32–64, mỗi block 1 Conv + MaxPool; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 545.098.

```python
def build_p11(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P11")
    return model
```

## P12 — 3 block đôi 16–32–64

3 block, mỗi block 2 Conv; filters 16–32–64; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 204.570.

```python
def build_p12(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 16 filters; Conv-ReLU
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P12")
    return model
```

## P13 — 3 block đơn 16–32–64

3 block, mỗi block 1 Conv; filters 16–32–64; không BN; Dense 128, Dropout 0.3.

**Tổng tham số mặc định:** 156.074.

```python
def build_p13(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 16 filters; Conv-ReLU
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 1 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P13")
    return model
```

## P14 — CNN nhỏ 16–32

2 block đôi 16–32; Dense 64, Dropout 0.3.

**Tổng tham số mặc định:** 148.442.

```python
def build_p14(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 16 filters; Conv-ReLU
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(64, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P14")
    return model
```

## P15 — CNN rộng 64–128

2 block đôi 64–128; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 1.310.154.

```python
def build_p15(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P15")
    return model
```

## P16 — 2 block, mỗi block 3 Conv

Block 32 và 64, mỗi block 3 Conv rồi MaxPool; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 637.450.

```python
def build_p16(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 3 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 3 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P16")
    return model
```

## P17 — Số Conv không đều: 2–1–1

3 block 32–64–128 với số Conv lần lượt 2–1–1; Dense 256, Dropout 0.3.

**Tổng tham số mặc định:** 629.610.

```python
def build_p17(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 1 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P17")
    return model
```

## P18 — Số Conv không đều: 1–2–2

3 block 32–64–128 với số Conv lần lượt 1–2–2; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 541.322.

```python
def build_p18(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P18")
    return model
```

## P19 — 4 block đơn 16–32–64–128

4 block, mỗi block 1 Conv + MaxPool; Flatten, Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 164.394.

```python
def build_p19(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 16 filters; Conv-ReLU
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 1 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 4: 1 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P19")
    return model
```

## P20 — 4 block đôi và GAP

4 block đôi 16–32–64–128; GAP, Dense 128, Dropout 0.4.

**Tổng tham số mặc định:** 311.322.

```python
def build_p20(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 16 filters; Conv-ReLU
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.Conv2D(16, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 4: 2 Conv, 128 filters; Conv-ReLU
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.GlobalAveragePooling2D(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.4),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P20")
    return model
```

## P21 — 3 block đơn với BN trước ReLU

Giữ filters và head của P02, nhưng mỗi block là Conv → BN → ReLU → MaxPool.

**Tổng tham số mặc định:** 621.258.

```python
def build_p21(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 1 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 3: 1 Conv, 128 filters; Conv → BN → ReLU
        layers.Conv2D(128, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P21")
    return model
```

## P22 — 2 block đôi với BN sau ReLU

Mỗi Conv có ReLU rồi BN; 2 block 32–64; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 592.042.

```python
def build_p22(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU → BN
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU → BN
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P22")
    return model
```

## P23 — 3 block đôi với BN trước ReLU

3 block đôi 32–64–128, Conv → BN → ReLU; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 552.362.

```python
def build_p23(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 128 filters; Conv → BN → ReLU
        layers.Conv2D(128, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(128, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P23")
    return model
```

## P24 — 3 block đôi với BN sau ReLU

3 block đôi 32–64–128, Conv có ReLU → BN; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 552.362.

```python
def build_p24(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU → BN
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU → BN
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 3: 2 Conv, 128 filters; Conv-ReLU → BN
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P24")
    return model
```

## P25 — Mỗi block chỉ có một BN ở cuối phần Conv

Mỗi block: Conv-ReLU → Conv-ReLU → BN → MaxPool; filters 32–64.

**Tổng tham số mặc định:** 591.658.

```python
def build_p25(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU; BN sau Conv cuối
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU; BN sau Conv cuối
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.BatchNormalization(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P25")
    return model
```

## P26 — BN đặt sau pooling

Mỗi block: Conv-ReLU → MaxPool → BN; filters 32–64–128; Dense 256, Dropout 0.3.

**Tổng tham số mặc định:** 621.258.

```python
def build_p26(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 1 Conv, 32 filters; Conv-ReLU; BN sau MaxPool
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),
        layers.BatchNormalization(),

        # Block 2: 1 Conv, 64 filters; Conv-ReLU; BN sau MaxPool
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),
        layers.BatchNormalization(),

        # Block 3: 1 Conv, 128 filters; Conv-ReLU; BN sau MaxPool
        layers.Conv2D(128, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),
        layers.BatchNormalization(),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.3),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P26")
    return model
```

## P27 — Chỉ block 1 có BN

Block 32 dùng Conv → BN → ReLU hai lần; block 64 không BN; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 591.530.

```python
def build_p27(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv → BN → ReLU
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(32, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P27")
    return model
```

## P28 — Chỉ block 2 có BN

Block 32 không BN; block 64 dùng Conv → BN → ReLU hai lần; Dense 128, Dropout 0.5.

**Tổng tham số mặc định:** 591.786.

```python
def build_p28(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv → BN → ReLU
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Conv2D(64, 3, padding="same", activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P28")
    return model
```

## P29 — BN sau Dense có ReLU

Backbone của P01; head là Flatten → Dense 128 ReLU → BN → Dropout 0.5 → output.

**Tổng tham số mặc định:** 591.786.

```python
def build_p29(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation="relu"),
        layers.BatchNormalization(),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P29")
    return model
```

## P30 — Dense tuyến tính → BN → ReLU

Backbone của P01; Dense 128 chưa kích hoạt, sau đó BN, ReLU, Dropout 0.5.

**Tổng tham số mặc định:** 591.786.

```python
def build_p30(input_shape=(32, 32, 3), num_classes=10):
    model = keras.Sequential([
        keras.Input(shape=input_shape),

        # Block 1: 2 Conv, 32 filters; Conv-ReLU
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.Conv2D(32, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Block 2: 2 Conv, 64 filters; Conv-ReLU
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.Conv2D(64, 3, padding="same", activation='relu'),
        layers.MaxPooling2D(2),

        # Phần phân loại
        layers.Flatten(),
        layers.Dense(128, activation=None),
        layers.BatchNormalization(),
        layers.ReLU(),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ], name="P30")
    return model
```

## Gọi mô hình và xem kiến trúc

Chạy sau phần import và hàm cần dùng. Thay build_p01 bằng build_p02, …, build_p30 để chọn mẫu khác.

```python
model = build_p01()
model.summary()
print("Output:", model.output_shape)  # (None, 10)
print("Tổng tham số:", model.count_params())
```

## Tài liệu API đối chiếu

- [Keras Sequential](https://www.tensorflow.org/api_docs/python/tf/keras/Sequential)
- [Conv2D](https://www.tensorflow.org/api_docs/python/tf/keras/layers/Conv2D)
- [BatchNormalization](https://www.tensorflow.org/api_docs/python/tf/keras/layers/BatchNormalization)
