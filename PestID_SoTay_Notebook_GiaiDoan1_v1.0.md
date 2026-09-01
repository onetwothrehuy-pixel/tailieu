# PestID — Sổ tay 12 Notebook Giai đoạn 1

**Tài liệu bổ trợ · Phiên bản v1.0 · 01/9/2026**
**Tài liệu mẹ:** `docs/phuong_phap/PestID_KeHoach_DuLieu_GiaiDoan1_v1.2_hopnhat.md` — Mục XII.
**Phạm vi:** 12 notebook trong `notebooks/quy_trinh_xu_ly_data/`, từ `data/raw/IP102` tới `clean_v1.0` đã khóa.
**Đối tượng đọc:** người trực tiếp ngồi viết và chạy notebook; và người mở lại repo ở tuần thứ ba muốn biết một notebook để lại cái gì.

> **Ký hiệu.** Kế thừa nguyên bộ từ tài liệu mẹ: `[ĐÃ ĐO]`, `[ƯỚC LƯỢNG]`, `[GIẢ ĐỊNH]`, `[CẦN XÁC NHẬN]`, `P-xx`, `HC-xx`, `RQ-x`/`Ex`, `CRIT/MAJ/WARN/REP-xx`. Mọi thuật ngữ tra ở Mục 0.7 của tài liệu mẹ.

---

## 0. Tài liệu này là gì và không là gì

**Kết luận chính: đây là "mặt trước" của mỗi notebook — đọc một trang là biết notebook đó làm gì, để lại cái gì, và sau khi chạy xong thì nhóm biết thêm điều gì mà trước đó không biết; nó không lặp lại thuật toán, vì thuật toán đã nằm ở Mục III–X của tài liệu mẹ.**

| Tài liệu này CÓ | Tài liệu này KHÔNG có |
|---|---|
| Mỗi notebook một trang: mục đích, đọc gì, ghi gì, **ý nghĩa của từng file đầu ra**, thu hoạch, cạm bẫy, tiêu chí DONE | Mã nguồn, snippet, chữ ký hàm — logic nặng nằm ở `src/pestid/`, đặc tả nằm ở Mục III–X |
| Bảng truy vết notebook → test cổng chất lượng → RQ/E | Thuật toán chi tiết (hiệu chuẩn ngưỡng ở VI.6, split ở VIII.2, cây quyết định H ở III.6) |
| Ba kịch bản chạy và checklist trước khi commit một notebook | Lịch nhân sự chi tiết — đã có ở Mục XIV |

**Ba câu hỏi tài liệu này tồn tại để trả lời**, và chúng là ba câu hay được hỏi nhất khi một người mở repo lần đầu:

1. *Notebook này có phải cái tôi đang cần không?* → đọc dòng "Một câu" và bảng ở Mục 1.
2. *Nó ghi ra file gì, và file đó nói lên điều gì?* → đọc mục "Ý nghĩa từng đầu ra".
3. *Chạy xong rồi thì sao — cái gì mở khóa, cái gì vẫn còn kẹt?* → đọc mục "Thu hoạch" và "DONE khi".

**Quy tắc dùng chung với tài liệu mẹ.** Khi hai tài liệu mâu thuẫn, **tài liệu mẹ thắng**; sổ tay này phải được sửa theo. Mọi con số trong sổ tay này đều là con số đã có nhãn ở tài liệu mẹ, không có con số mới.

---

## 1. Bản đồ toàn tuyến

### 1.1. Chuỗi phụ thuộc

Tuyến build chính (điều kiện của REP-02) là tám notebook chạy tuần tự:

`01_scan` → `03_exact_duplicates` → `04a_embed` → `04b_nd_candidates` → ⟨người: gán nhãn 600 cặp⟩ → `04c_nd_cluster` → `05_label_audit` → `06_build_clean_pool` → `07_build_split`

Bốn notebook còn lại là **nhánh phân tích**, treo vào tuyến chính nhưng không quay ngược lại nó:

- `08_validate_dataset` ← `07` (chấm điểm, không sửa dữ liệu)
- `09_eda` ← `08` (chỉ chạy sau khi cổng đã PASS)
- `10_crop_study` ← `01` (độc lập với toàn bộ nhánh gần trùng, chạy được ngay Tuần 1)
- `11_leakage_report` ← `04c` **và** `cleaning_config.yaml` đã commit (hàng rào P-03)

Điểm kiến trúc đáng nhớ: **`11_leakage_report` là một nhánh cụt có chủ ý**. Nó sinh bằng chứng cho RQ1 nhưng không được phép quay lại chỉnh bất kỳ tham số nào ở `04b`/`04c`. Nếu ai đó thấy con số rò rỉ "chưa đẹp" và mở lại `04c`, hàng rào MAJ-06 sẽ bắt được — và đó chính là lý do hàng rào tồn tại.

Điểm thứ hai: **không có `02`**. Tài liệu mẹ gộp `01_inventory` và `02_integrity_check` thành `01_scan` vì cả hai đều cần một lượt decode toàn bộ 75.222 ảnh; tách ra là ép đọc đĩa hai lần (III.11). Đánh số giữ nguyên khoảng trống ở `02` để không phải đánh số lại các notebook phía sau.

### 1.2. Bảng tổng quan

| # | Notebook | Một câu | Giờ máy | Giờ người | Tuần | Ưu tiên |
|---|---|---|---|---|---|---|
| 1 | `01_scan.ipynb` | Một lượt đọc duy nhất: kiểm kê, toàn vẹn, mọi loại băm, chất lượng, cache 256 | 35–55 ph (12–18 ph, 4 tiến trình) | 1,75 | T1 | **P0** |
| 2 | `03_exact_duplicates.ipynb` | Nhóm trùng byte và trùng pixel, chọn ảnh đại diện | < 1 ph | 0,5 | T1 | **P0** |
| 3 | `04a_embed.ipynb` | Trích embedding DINOv2 ViT-S/14 cho ảnh đại diện (Kaggle GPU) | ~5 ph GPU | 1,0 | T1 | **P0** |
| 4 | `04b_nd_candidates.ipynb` | Sinh + xác minh cặp ứng viên 4 kênh; sinh mẫu hiệu chuẩn và ảnh biến đổi tổng hợp | 40–70 ph | 1,5 | T1 | **P0** |
| — | ⟨người⟩ Label Studio | Gán nhãn 600 cặp hiệu chuẩn, chấm kép | — | 2,7 | T2 | **P0** |
| 5 | `04c_nd_cluster.ipynb` | Cross-fit scorer, **khóa hai ngưỡng**, dựng và rà đồ thị, sinh cờ rò rỉ | 15–25 ph | 2,0 + 1,7 | T2 | **P0** |
| 6 | `05_label_audit.ipynb` | Năm hàng đợi rà nhãn; nhập kết quả người; áp taxonomy quyết định | 10–20 ph | 17,5 (T12–T16) | T2–T3 | **P0**/P1 |
| 7 | `06_build_clean_pool.ipynb` | Áp cây quyết định H; registry / pool / quarantine / removed; cache phát hành | < 2 ph | 0,75 | T3 | **P0** |
| 8 | `07_build_split.ipynb` | Group-aware split; sinh mọi tập K, OOD, cặp E1; khóa manifest | 1–3 ph | 1,5 | T4 | **P0** |
| 9 | `08_validate_dataset.ipynb` | Chạy 40 test cổng chất lượng; REP-01 + REP-02 | < 2 ph (+ ~1 h REP-02) | 1,5 | T4 | **P0** |
| 10 | `09_eda.ipynb` | 20 biểu đồ bốn tầng + phân tích mất cân bằng, chỉ train + val | 15–25 ph | 4,0 | T4 (được trượt T5) | P1 |
| 11 | `10_crop_study.ipynb` | Kiểm kê nhánh detection; đo mức mất đối tượng do center crop | ~10 ph | 1,0 | T1 | P1 |
| 12 | `11_leakage_report.ipynb` | Đo rò rỉ trên phép chia gốc; bảng B-L1..L4, hình H-L1..H-L6 | 5 ph | 2,0 | T2–T4 | **P0** |

Ba notebook chiếm gần hết giờ máy (`01`, `04b`, và lượt REP-02 của `08`); một notebook chiếm gần hết giờ người (`05`). Biết điều này trước giúp không lên lịch nhầm: `01` chạy được qua đêm, còn `05` thì không — nó cần người ngồi trước màn hình.

---

## 2. Cách đọc một trang notebook

Mỗi mục ở phần 3 đều có đúng chín khối sau, luôn theo thứ tự này:

| Khối | Nó trả lời câu hỏi gì |
|---|---|
| **Một câu** | Notebook này làm gì, nói được trong một hơi |
| **Vì sao tồn tại** | Bỏ nó đi thì cái gì hỏng — và hỏng ở chỗ khó chẩn đoán tới mức nào |
| **Đọc / Ghi** | File vào, file ra, tham số ở ô 1 |
| **Ý nghĩa từng đầu ra** | Mỗi file nói lên điều gì và ai tiêu thụ nó ở bước sau |
| **Ô 5 phải hiện** | Bảng, biểu đồ, lưới ảnh mẫu — theo đặc tả XII.2 |
| **Tự kiểm ở ô 6** | Mã test của cổng chất lượng mà chính notebook này phải chạy trước |
| **Thu hoạch** | Sau khi chạy xong, nhóm biết thêm gì và mở khóa được việc gì |
| **Cạm bẫy** | Lỗi đã được dự đoán trước, gần như chắc chắn sẽ có người mắc |
| **DONE khi** | Điều kiện nhị phân để nói "xong", không phải cảm giác |

Nhắc lại khuôn mẫu bảy ô (Mục XII.1 của tài liệu mẹ), vì mọi trang dưới đây đều giả định nó: **ô 0** tiêu đề markdown · **ô 1** chỉ tham số, có thẻ `parameters` · **ô 2** setup và in phiên bản + commit · **ô 3** nạp dữ liệu và kiểm hash đầu vào · **ô 4** gọi hàm từ `src/pestid/` · **ô 5** hiển thị kết quả · **ô 6** ghi ra đĩa và tự kiểm · **ô 7** ghi sổ chạy `{notebook}_run.json`.

Hai ô hay bị bỏ nhất là **ô 6** và **ô 7**, và cả hai đều là ô làm nên khác biệt giữa "notebook chạy được" và "notebook bảo vệ được trước hội đồng". Ô 6 bắt lỗi ở tuần 1 thay vì ngày 24/9; ô 7 là bằng chứng duy nhất cho REP-02.

---

## 3. Mười hai notebook

### 3.1. `01_scan.ipynb`

**Một câu.** Mở mỗi file trong `data/raw/IP102` **đúng một lần** và rút ra tất cả những gì cần rút: kiểm kê, toàn vẹn, bốn loại băm, bốn chỉ số chất lượng, và bản cache cạnh ngắn 256.

**Vì sao tồn tại.** Đây là notebook duy nhất chạm vào ảnh gốc ở quy mô toàn bộ. Sau khi nó chạy xong, **mọi bước còn lại của Giai đoạn 1 chỉ làm việc trên bảng** — không notebook nào phải đọc lại 75.222 file nhỏ nữa. Nếu tách A, B, C-hash, G và cache ra thành năm notebook, chi phí máy nhân lên bốn đến năm lần mà không thêm thông tin nào (III.11). Bỏ notebook này thì không có gì để chạy tiếp; chạy nó sai thì mọi con số phía sau sai theo mà không ai biết.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `data/raw/IP102/classification/{train,val,test}/{0..101}/`, `classes.txt`, `train/val/test.txt`, `Annotations/`, `JPEGImages/` |
| Ghi | `A_inventory.parquet`, `A_source_checksum.txt`, `B_integrity.parquet`, `B_removed.csv`, `D_hashes.npz`, `G_quality.parquet`, `cache256_scan/`, `N_cache_scan_manifest.parquet` |
| Tham số ô 1 | `force_rescan=False`, `workers=4`, `cache_quality=95` |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `A_inventory.parquet` | Sổ cái gốc: mỗi ảnh một dòng, có `image_id`, `orig_split`, `orig_class_id`, `sha256`. Đây là mẫu số của mọi phép đếm về sau | 03, 06, và mọi bảng phần trăm trong báo cáo |
| `A_source_checksum.txt` | Dấu vân tay của toàn bộ nguồn — một chuỗi duy nhất chứng minh "chúng tôi đã chạy trên đúng bản IP102 này" | `build_info.json`, test REP-02 |
| `B_integrity.parquet` | Ảnh nào mở được, kích thước bao nhiêu, mode gì, có alpha không, EXIF xoay bao nhiêu | 06 (nhánh 1–2 của cây H), EDA tầng 1, `10_crop_study` |
| `B_removed.csv` | Danh sách ảnh bị loại **tự động** kèm lý do kỹ thuật (`decode_fail`, `too_small`, `empty_file`) | 06, MAJ-03, datasheet |
| `D_hashes.npz` | dHash / pHash / wHash 64-bit và thumbnail xám 32×32 cho từng ảnh | 04b — bốn kênh sinh ứng viên |
| `G_quality.parquet` | `blur_lapvar`, `blur_lapvar_norm`, `brightness`, `clipping_ratio` tính **trên cache 256** để loại nhiễu do độ phân giải | 05 (hàng đợi blur), EDA tầng 2–3, phân tích `class_size × blur` |
| `cache256_scan/` + `N_cache_scan_manifest.parquet` | Bản ảnh cạnh ngắn đúng 256 px mà **mọi bộ nạp dữ liệu sẽ đọc** (HC-07). Ở giai đoạn này còn là cache *ứng viên*: nó chứa cả ảnh sẽ bị loại về sau | 04a, 05, 06 (lọc thành cache phát hành) |

**Ô 5 phải hiện.** Bảng số ảnh theo lớp và theo split gốc, cộng số ảnh dính từng cờ chất lượng (kèm cột %). Histogram cạnh ngắn, tỷ lệ khung hình, độ mờ — ba đường dọc đánh dấu 112, 200, 256 px. Lưới ảnh: **12 ảnh mờ nhất, 12 ảnh nhỏ nhất, và cả 7 ảnh có kênh trong suốt sau khi ghép nền trắng**.

**Tự kiểm ở ô 6.** `len(inventory) == 75_222` · `relative_path` duy nhất · `class_nunique == 102` · split gốc đúng 45.095 / 7.508 / 22.619 · CRIT-02 (`image_id` duy nhất và bằng đúng `"IP102__" + file_name`) · MAJ-07 (mọi ảnh cache có `min(w,h) == 256`, hệ số phóng ≤ 2,286, PSNR trung vị ≥ 40 dB).

**Thu hoạch.**

- **Con số quan trọng nhất chưa từng đo:** bao nhiêu ảnh có cạnh ngắn dưới 112 px. Trước notebook này nhóm mới chỉ biết mức dưới 64 px là 5 ảnh `[ĐÃ ĐO]`. Con số 112 px ảnh hưởng trực tiếp tới `n_c` của lớp Tail, và qua đó tới HC-01 và HC-06 — tức tới việc K45 có tồn tại hay phải hạ xuống K40.
- **`sha256_pixel` lần đầu có mặt**, mở đường cho việc phát hiện trùng pixel ở `03` mà băm file không thấy được.
- **Phân bố thật của cạnh ngắn và aspect ratio**, thay cho suy đoán — đây là đầu vào để quyết định có giữ center crop hay không (`10_crop_study`).
- **Toàn bộ chi phí đọc đĩa của Giai đoạn 1 được trả một lần ở đây.** Sau notebook này, không bước nào còn đụng ảnh gốc.

**Cạm bẫy.**

- `exif_transpose` phải áp **đúng một lần** trong canonical decoded view, và cả pixel-hash lẫn cache đều tiêu thụ chính view đó. Áp hai lần, hoặc áp lại trong `DataLoader`, làm ảnh xoay sai và làm pixel-hash không khớp giữa hai lượt chạy.
- Nếu ba con số kiểm kê không khớp, **dừng và tải lại IP102**. Tuyệt đối không "sửa cho khớp" — đó là cách nhanh nhất để có một dataset không ai dựng lại được.
- `blur_lapvar` phải tính trên cache 256, không phải trên ảnh gốc; tính trên ảnh gốc là đo độ phân giải chứ không đo độ nét.
- Notebook này **không xóa ảnh vì mờ**. Nó chỉ đo và gắn cờ (P-08). Ngưỡng blur chưa tồn tại ở thời điểm này — nó được hiệu chuẩn ở `05`.

**DONE khi.** Chạy hết không lỗi ở chế độ Restart & Run All · `A_inventory` có đúng 75.222 dòng · file thực tế trong `cache256_scan/` khớp `N_cache_scan_manifest` · mọi ảnh cache có cạnh ngắn 256 · `docs/data/A_inventory_discrepancies.md` đã ghi mọi chênh lệch với annotation gốc (kể cả khi tập chênh lệch rỗng — ghi "rỗng" cũng là một kết quả).

*Phục vụ:* nền của toàn bộ RQ1–RQ7 · *Công việc:* T01, T02, T03 · *Chặn:* mọi thứ.

---

### 3.2. `03_exact_duplicates.ipynb`

**Một câu.** Gom các ảnh giống hệt nhau ở hai mức — trùng byte và trùng pixel đã giải mã — rồi chọn một ảnh đại diện cho mỗi nhóm theo quy tắc xác định.

**Vì sao tồn tại.** Hai bản sao trong cùng một lớp làm `n_c` sai, kéo theo trọng số square-root sampling và effective-number ở E3 sai. Hai bản sao ở hai split là **rò rỉ tuyệt đối**, không cần bàn về ngưỡng. Hai bản sao ở hai lớp là **mâu thuẫn nhãn không thể chối cãi** — tín hiệu mạnh nhất mà rà nhãn tự động có thể cho.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `A_inventory.parquet` (cột `sha256`), `B_integrity.parquet` (cột `sha256_pixel`) |
| Ghi | `C_duplicate_clusters.csv` |
| Tham số ô 1 | `hash="sha256"` |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `C_duplicate_clusters.csv` | Mỗi nhóm trùng một cụm, kèm `group_context` (`intra` / `cross_split` / `cross_class`) và cờ `is_exact_representative`. `group_context` mới là phần có ý nghĩa: cùng một hiện tượng kỹ thuật nhưng ba hệ quả hoàn toàn khác nhau | 04a (chỉ trích embedding cho đại diện), 05 (hàng đợi `exact_cross_class`), 06 (nhánh 3 của cây H), CRIT-05 |

**Ô 5 phải hiện.** Bảng số nhóm và số ảnh dư, **tách theo ba bối cảnh** — cùng lớp / khác split / khác lớp. Phân bố kích thước nhóm. Lưới ảnh: **mọi nhóm khác lớp, hiện cả hai nhãn cạnh nhau** — không lấy mẫu, vì loại nhóm này ít và mỗi nhóm đều phải đi qua tay người.

**Tự kiểm ở ô 6.** CRIT-05 (mỗi nhóm trùng góp tối đa một ảnh vào pool sạch) · CRIT-06 (một nội dung pixel chỉ mang một nhãn) · quy tắc chọn đại diện là toàn phần và xác định: độ phân giải lớn hơn → `file_size` lớn hơn → `image_id` nhỏ hơn theo thứ tự chuỗi.

**Thu hoạch.**

- **Số nhóm trùng pixel — con số hoàn toàn mới.** `[ĐÃ ĐO]` ở mức băm file là 5 nhóm / 5 ảnh dư, trong đó 2 nhóm rò rỉ xuyên split và 0 nhóm mâu thuẫn nhãn; mức pixel chưa ai đo. Notebook này trả lời "IP102 sạch ở mức file, nhưng ở mức pixel thì sao?".
- **Hàng đợi rà nhãn đầu tiên** (`exact_cross_class`) được sinh ra, với tín hiệu tuyệt đối: hai file có cùng ma trận điểm ảnh mà mang hai nhãn thì chắc chắn một trong hai nhãn sai.
- **Tập ảnh đại diện** — đầu vào chính xác của `04a`, giúp không lãng phí GPU trích embedding cho các bản sao.

**Cạm bẫy.**

- Hai JPEG lưu ở mức nén khác nhau **không** cho pixel giống hệt. Chúng thuộc bài toán gần trùng ở `04b`, và gọi chúng là "exact duplicate" trong báo cáo là một tuyên bố sai mà hội đồng bắt được ngay.
- Nhóm `cross_class` **không được xóa tự động**. Cây quyết định H đưa chúng sang nhánh REVIEW (III.6, nhánh 3b) — xóa lặng lẽ ở đây là vi phạm P-05 và P-07 cùng lúc.
- Ảnh đã bị loại vì trùng chính xác **không bắt buộc phải có `near_duplicate_group`**; đừng viết assertion đòi điều đó (xem ghi chú "Khi FAIL" của CRIT-05).

**DONE khi.** Mỗi nhóm còn đúng một đại diện · mọi ảnh không phải đại diện đều vắng khỏi pool nhưng vẫn có mặt trong registry kèm lý do · số nhóm ba bối cảnh đã được ghi vào báo cáo.

*Phục vụ:* RQ1, RQ3 (`n_c` chính xác) · *Công việc:* T04 · *Phụ thuộc:* `01`.

---

### 3.3. `04a_embed.ipynb`

**Một câu.** Trích embedding DINOv2 ViT-S/14 (384 chiều, fp16) cho tập ảnh đại diện sau bước C, chạy trên Kaggle GPU.

**Vì sao tồn tại.** Ba kênh băm (dHash, pHash, wHash) **mù hoàn toàn** trước lật ngang, và mất tín hiệu từ mức cắt cúp khoảng 15% trở đi. Embedding là kênh duy nhất bắt được họ biến đổi hình học. Ngoài ra nó còn là đầu vào của bốn việc khác nữa: cleanlab, phát hiện outlier "ảnh không chứa đối tượng", UMAP tầng 4, và bảng khoảng cách nội lớp/liên lớp.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `N_cache_scan_manifest.parquet` + quyết định đại diện từ `C_duplicate_clusters.csv` |
| Ghi | `D_embeddings.npy` (float16, `(N_after_C, 384)`), `D_embedding_index.parquet` |
| Tham số ô 1 | `model="dinov2_vits14"`, `batch=128`, `fp16=True` |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `D_embeddings.npy` | Vị trí của mỗi ảnh trong không gian biểu diễn của một mô hình nền không được huấn luyện trên IP102 — nên nó là ý kiến độc lập về "hai ảnh này có giống nhau không" | 04b (kênh top-k cosine), 05 (cleanlab, outlier), 09 (UMAP, heatmap chồng lấn) |
| `D_embedding_index.parquet` | Ánh xạ `row_index ↔ image_id`. Tồn tại **để không ai phải nối bảng bằng thứ tự ngầm** | mọi nơi đọc `.npy` |

**Ô 5 phải hiện.** Bảng số dòng embedding so với số ảnh đủ điều kiện (hai số này phải bằng nhau, và nếu lệch thì lệch ở đâu). Histogram chuẩn L2 trước khi chuẩn hóa — một đỉnh bất thường ở 0 nghĩa là có ảnh trắng hoặc ảnh hỏng lọt qua.

**Tự kiểm ở ô 6.** `rows(embeddings) == rows(index)` · không có NaN · mọi `image_id` trong index thuộc đúng tập sau bước C · chuẩn L2 > 0 với mọi dòng.

**Thu hoạch.**

- **Kênh phát hiện thứ tư** — thứ khiến `04b` có thể tuyên bố phủ được họ crop/xoay/lật, thay vì chỉ phủ họ nén/đổi kích thước.
- **Một biểu diễn dùng chung cho bốn việc khác nhau**, trích một lần thay vì bốn lần. Đây là lý do `04a` tách riêng thành một notebook dù chỉ chạy 5 phút: nó chạy trên máy khác (Kaggle GPU) so với phần còn lại.
- 0,4 h GPU — **toàn bộ nhu cầu GPU của Giai đoạn 1**. Hạn mức Kaggle không bị đụng tới.

**Cạm bẫy.**

- **Không bao giờ nối embedding với manifest bằng thứ tự dòng ngầm định.** Đây là lỗi kinh điển, không báo lỗi, và làm mọi kết quả gần trùng sai một cách vô hình. File index tồn tại chính vì lý do này.
- Nếu Kaggle không tải được trọng số DINOv2, fallback đã định sẵn là MobileNetV3-Small ImageNet trên CPU — chậm hơn nhưng vẫn dùng được cho việc sinh ứng viên. **Ghi fallback vào `build_info.json`**, vì nó đổi ý nghĩa của bảng recall theo họ biến đổi ở `04b`.
- fp16 là cố ý (dung lượng), nhưng mọi phép tính cosine phải nâng lên fp32 trước khi so ngưỡng.

**DONE khi.** Số dòng khớp, 384 cột, không NaN, index tồn tại và ánh xạ 1–1.

*Phục vụ:* RQ1 (kênh phát hiện), RQ2 (E4-1..E4-3) · *Công việc:* T05 · *Phụ thuộc:* `01`, `03`.

---

### 3.4. `04b_nd_candidates.ipynb`

**Một câu.** Sinh tập cặp ứng viên từ bốn kênh, xác minh từng cặp bằng MSE/SSIM, rồi chuẩn bị **hai nguồn sự thật** cho việc hiệu chuẩn ngưỡng ở bước sau.

**Vì sao tồn tại.** 75.222 ảnh cho 2,83 tỷ cặp — không thể chấm hết, cũng không thể lấy mẫu ngẫu nhiên (tỷ lệ cặp gần trùng thật `[ƯỚC LƯỢNG]` dưới `10⁻⁵`, nên mẫu 10.000 cặp ngẫu nhiên gần như chắc chắn không chứa cặp dương tính nào). Notebook này giải bài toán đó bằng cách thu hẹp về tập ứng viên, rồi chuẩn bị hai nguồn sự thật khác nhau cho hai đại lượng khác nhau: **recall đo bằng biến đổi tổng hợp** (ground truth dựng được, chính xác 100%, chi phí người bằng 0) và **precision đo bằng nhãn người** trên mẫu phân tầng theo điểm.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `D_hashes.npz`, `D_embeddings.npy` + index |
| Ghi | `D_pairs_verified.parquet`, `D_calib_sample.csv` (600 cặp), `D_synth_pairs.csv`, `D_synthetic_recall.csv` |
| Tham số ô 1 | `hamming_radius=8`, `mih_blocks=(21,21,22)`, `topk=20`, `calib_n=600` |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `D_pairs_verified.parquet` | Mọi cặp ứng viên kèm đặc trưng từng kênh và kết quả xác minh MSE/SSIM — nguyên liệu thô của scorer | 04c |
| `D_calib_sample.csv` | 600 cặp chia 6 tầng × 100 theo điểm sơ bộ, ép tối thiểu 20 cặp liên lớp mỗi tầng. Kèm `N_h`, `n_h` và xác suất chọn của từng cặp — **thiếu ba trường này thì không hậu phân tầng được và bootstrap sẽ cho CI sai** | ⟨người⟩ → 04c |
| `D_synth_pairs.csv` | 300 ảnh gốc × 12 biến thể = 3.600 ảnh, nhãn đúng theo xây dựng | 04c (đo recall) |
| `D_synthetic_recall.csv` | Recall **tách riêng theo 7 họ biến đổi**: nén, đổi kích thước, cắt cúp, xoay, lật, chỉnh sáng, watermark | 04c, và trực tiếp vào báo cáo |

**Ô 5 phải hiện.** Bảng số cặp ứng viên theo từng kênh, phần giao giữa các kênh, và tổng sau khử trùng — bảng này cho biết kênh nào thực sự đóng góp và kênh nào chỉ lặp lại kênh khác. Histogram khoảng cách Hamming của cặp gần nhất. Lưới **12 cặp ở mỗi tầng điểm**, để người chấm hình dung được thang điểm trước khi bước vào Label Studio.

**Tự kiểm ở ô 6.** Phép thử tổng hợp không bỏ sót cặp `H ≤ 8` · không cặp nào thiếu điểm ở bất kỳ kênh nào · mẫu hiệu chuẩn đủ 6 tầng × 100 và mỗi tầng ≥ 20 cặp liên lớp · seed lấy mẫu đã ghi.

**Thu hoạch.**

- **Bảng recall theo họ biến đổi là một kết quả có giá trị công bố.** Nó là câu trả lời chuẩn bị sẵn cho câu hỏi "làm sao các em biết mình không bỏ sót?" — câu hỏi mà mọi hội đồng đều hỏi và hầu hết đồ án không trả lời được.
- **Biết chính xác kênh nào phủ họ biến đổi nào**, thay vì tin vào bảng dự đoán. Nếu embedding không bắt được họ lật ngang như dự kiến, notebook này là nơi phát hiện — sớm hơn một tuần so với khi cụm đã dựng xong.
- **600 cặp đã sẵn sàng cho người chấm**, phân tầng đúng chỗ ngưỡng sẽ được đặt.

**Cạm bẫy.**

- **Không lấy mẫu hiệu chuẩn ngẫu nhiên.** Gần như toàn bộ cặp ứng viên có điểm cao; mẫu ngẫu nhiên sẽ không phủ vùng biên — mà vùng biên mới là nơi ngưỡng được đặt.
- Quota ép 20 cặp liên lớp mỗi tầng là **một tầng con riêng**, không được trộn vào như lấy mẫu đều khi tính trọng số.
- Đây là notebook tốn giờ máy thứ hai (40–70 ph). Đừng chạy nó ở chế độ thử nghiệm với `topk` nhỏ rồi quên đổi lại trước lượt chính thức — ô 7 ghi tham số, nên lỗi này truy được, nhưng chỉ khi có người đọc sổ chạy.

**DONE khi.** `D_pairs_verified` đầy đủ điểm · `D_calib_sample.csv` đúng 600 dòng với đủ metadata lấy mẫu · bảng recall có đủ 7 họ · lưới 12 cặp mỗi tầng đã xuất ra HTML để người chấm xem trước.

*Phục vụ:* RQ1 · *Công việc:* T06, T07 · *Phụ thuộc:* `01`, `04a`.

---

### 3.5. ⟨người⟩ — Gán nhãn 600 cặp hiệu chuẩn

**Đây không phải notebook**, nhưng nó nằm giữa `04b` và `04c` nên phải có mặt trong sổ tay này, nếu không sẽ có người tưởng `04c` chạy được ngay sau `04b`.

| | Nội dung |
|---|---|
| Công cụ | Label Studio |
| Đầu vào | `D_calib_sample.csv` (600 cặp) |
| Đầu ra | `D_calib_labeled.csv` |
| Cách làm | Hai thành viên chấm **độc lập**, ba mức `same_scene` / `different` / `unsure`. Cặp `unsure` của cả hai người tính là `different` khi đo precision (bảo thủ) và được báo cáo riêng |
| Chỉ số | Cohen's kappa ≥ 0,70. Dưới ngưỡng: **dừng, làm rõ tiêu chí, chấm lại** — bộ nhãn mà hai người không đồng thuận thì không hiệu chuẩn được gì |
| Chi phí | 2,7 h người, chia hai đợt 300 cặp (Tuần 2, T2 và T3) |

**Thu hoạch.** Nguồn sự thật duy nhất cho precision. Và một thứ nữa ít ai nghĩ tới: quá trình chấm buộc hai người phải thống nhất *định nghĩa* gần trùng — "cùng một cảnh chụp hoặc cùng một ảnh gốc đã bị biến đổi", chứ không phải "trông giống nhau". Nếu định nghĩa này chưa rõ trong đầu hai người thì kappa sẽ nói ra ngay.

**Cạm bẫy.** Chấm không mù (nhìn thấy điểm số của cặp) làm nhãn bị neo theo máy và phá luôn ý nghĩa của phép hiệu chuẩn.

---

### 3.6. `04c_nd_cluster.ipynb`

**Một câu.** Huấn luyện scorer bằng cross-fit, **khóa hai ngưỡng** `τ_strict` và `τ_recall`, dựng đồ thị gần trùng và rà cụm lớn, rồi — chỉ sau khi khóa — sinh cờ rò rỉ trên phép chia gốc.

**Vì sao tồn tại.** Đây là **cổng cứng nhất của cả Giai đoạn 1**. Tài liệu mẹ nói thẳng: mọi thứ ở Tuần 3 và Tuần 4 đều giả định các cụm đã đúng, và sửa ngưỡng sau Tuần 2 nghĩa là làm lại từ T10. Notebook này cũng là nơi thực thi quyết định thiết kế quan trọng nhất của Mục V: **một scorer, hai điểm hoạt động** — vì tầng ĐO cần precision (con số đưa vào báo cáo phải chịu được việc bị chiếu lên màn hình) còn tầng CHẶN cần recall (bỏ sót một cặp là để lọt rò rỉ vào chính benchmark).

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `D_pairs_verified`, `D_calib_labeled.csv`, `D_synthetic_recall.csv`, kích thước tầng lấy mẫu |
| Ghi | scorer đã fit, `near_duplicate_clusters.csv`, `docs/data/D_threshold_calibration.md`, `D_oversized_clusters.csv`, `E_orig_leakage_flags`, và **`configs/cleaning_config.yaml` được commit** |
| Tham số ô 1 | `precision_floor=0.90`, `recall_target=0.98`, `bootstrap=5000` |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `near_duplicate_clusters.csv` | `near_duplicate_group` cho mọi ảnh — thành phần liên thông của đồ thị. Đây là **đơn vị không chia được** của bước split | 07 (CRIT-04), 05, 09, 11 |
| `D_threshold_calibration.md` | Hồ sơ có chữ ký: bảng precision theo tầng, bảng recall theo họ, Cohen's kappa, 12 cặp ảnh quanh mỗi ngưỡng. **Phải commit trước** khi bất kỳ script nào đọc `orig_split == "test"` | hàng rào P-03, MAJ-06, báo cáo |
| `D_oversized_clusters.csv` | Sổ các cụm vượt `S_alert`: cạnh cầu nào đã rà, kết luận gì. Cụm lớn **được rà chứ không bị cắt** (HC-04) | MAJ-05, 06 |
| `E_orig_leakage_flags` | Hai cờ `near_dup_with_train_orig_strict` và `_recall` cho mọi ảnh `orig_split ∈ {val,test}` | 11, và E1 ở Giai đoạn 2 |

**Ô 5 phải hiện.** Bảng precision và recall tại từng ngưỡng kèm cỡ mẫu hiệu dụng `n_eff`. **Hai panel hiệu chuẩn (H-L2)** — panel A precision OOF có trọng số + CI, panel B recall tổng hợp theo từng họ; hai panel chung trục ngưỡng, **không ghép thành PR curve giả** vì precision và recall đến từ hai quần thể khác nhau. Phân bố kích thước cụm. Lưới **12 cặp ngay dưới và 12 cặp ngay trên mỗi ngưỡng — đây là bằng chứng chính đưa vào báo cáo**.

**Tự kiểm ở ô 6.** Cổng hiệu chuẩn: tồn tại `τ_strict` (cận dưới bootstrap 95% của precision có trọng số ≥ 0,90 với `n_eff ≥ 30`) · tồn tại `τ_recall` (recall toàn pipeline ≥ 0,98) · **`τ_recall ≤ τ_strict`** (kiểm chiều ngưỡng) · tỷ lệ cụm lớn chưa giải quyết ≤ 2% · MAJ-12 (`nd_confidence ∈ {strict, recall, manual}`, mọi cạnh recall có `edge_status`) · MAJ-09 (kappa ≥ 0,70).

**Thu hoạch.**

- **Hai con số được khóa và ký** — sau thời điểm này chúng là hằng số của cả đồ án. Đây là thu hoạch lớn nhất của Tuần 2.
- **`near_duplicate_group` cho mọi ảnh**, tức đơn vị không chia được của bước split đã tồn tại. Trước notebook này, chưa thể chia dữ liệu đúng cách.
- **Biết bộ phát hiện của nhóm mạnh yếu ở đâu**, bằng số, theo từng họ biến đổi — không phải bằng cảm giác.
- **Cờ rò rỉ trên phép chia gốc**, nguyên liệu của RQ1.

**Cạm bẫy.**

- **Tuyệt đối không hạ `precision_floor` hay `recall_target` để cổng chịu mở.** Hai con số ấy là điều kiện của các test Critical; hạ chúng sau khi đã nhìn thấy dữ liệu là đúng thứ P-03 cấm. Khi cổng FAIL, thang leo ba bước đã định sẵn ở Mục XIV.2 (bổ sung 150 cặp đúng tầng thiếu → bật SIFT/RANSAC cho họ hụt → bật SIFT và nâng `k` lên 50), và ngày 11/9 đã được để trống làm ngày dự phòng.
- **Không cắt cụm lớn.** HC-04 đã được sửa từ "trần cắt cụm" thành "ngưỡng audit không phá cụm". Cụm lớn đã được hai người xác nhận là thật thì không tính là lỗi.
- **Không sinh cờ rò rỉ trước khi `cleaning_config.yaml` được commit.** Thứ tự này là hàng rào P-03 và được MAJ-06 kiểm bằng hash config + timestamp — không dựa vào `mtime` vì `mtime` sửa được.
- Bắc cầu (A~B, B~C ⇒ cả ba cùng split) là **ngữ nghĩa đúng**, không phải tác dụng phụ cần khắc phục. Vấn đề của bắc cầu là một cạnh sai nhập hai cụm lớn, và nó được xử lý bằng rà cạnh cầu chứ không bằng cách bỏ tính bắc cầu.

**DONE khi.** `cleaning_config.yaml` đã commit và có hash trong `build_info` · `D_threshold_calibration.md` có chữ ký nhóm trưởng và ngày · kappa ≥ 0,70 · recall ≥ 0,98 tại `τ_recall` · cận dưới precision ≥ 0,90 tại `τ_strict` · mọi cụm vượt `S_alert` có dòng trong sổ.

*Phục vụ:* RQ1 · *Công việc:* T09, T10, T11 · *Phụ thuộc:* `04b` + nhãn người · *Chặn:* `05`, `06`, `07`, `11`.

---

### 3.7. `05_label_audit.ipynb`

**Một câu.** Sinh năm hàng đợi rà nhãn, nhập kết quả chấm của người, và áp taxonomy quyết định để mọi ảnh khả nghi có một trạng thái cuối.

**Vì sao tồn tại.** Rà toàn bộ 75.222 ảnh ở tốc độ lạc quan 5 giây/ảnh là 104 h cho một người, 209 h nếu chấm kép — vượt ngân sách `[GIẢ ĐỊNH: 60 h]` hơn ba lần. Rà đại trà **không phải một lựa chọn**, và bất kỳ kế hoạch nào ngầm giả định điều đó đều sai ngay từ đầu. Chiến lược thay thế là phân tầng: để công cụ chỉ chỗ, để người quyết định (P-10), và chi giờ công theo tỷ lệ nghịch với độ chắc chắn của tín hiệu tự động.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `near_duplicate_clusters.csv`, `D_embeddings.npy`, `G_quality.parquet`, `C_duplicate_clusters.csv`, kết quả Label Studio |
| Ghi | `label_audit.csv` (**append-only**), `quarantine_images.csv`, `class_manifest.csv` (cột `taxonomy_level`), `docs/data/G_blur_calibration.md` |
| Tham số ô 1 | `queue="all"`, `cleanlab_topk=400` |

**Năm hàng đợi và chiến lược của mỗi hàng đợi.**

| Hàng đợi | Tín hiệu | Số ứng viên | Rà bao nhiêu | Giờ người |
|---|---|---|---|---|
| `exact_cross_class` | Tuyệt đối | `[ĐÃ ĐO]` 0 ở mức MD5; mức pixel chưa đo | **100%** | 0,3 |
| `nd_cross_class` | Mạnh nhất còn lại | `[ƯỚC LƯỢNG]` 200–350 nhóm tại `τ_recall` | **100%** | **6,3** |
| `cleanlab` | Yếu, chỉ gợi ý | top 400 theo `self_confidence` | Mẫu 400 | **4,4** |
| `outlier` | Rất yếu | top 150 xa tâm lớp | Mẫu 150 | 1,3 |
| `blur` | Cần hiệu chuẩn riêng | 300 ảnh phân tầng | Mẫu 300, chấm mù | **2,5** |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `label_audit.csv` | Một dòng cho mỗi **hành động**, không phải mỗi ảnh. Có `evidence` (vì sao ảnh vào hàng đợi), `vote_1`/`vote_2` thô, `decided_at`. Ba tính chất khiến nó truy vết ngược được: chỉ ghi thêm, giữ phiếu thô nên tính lại kappa được bất cứ lúc nào, và có bằng chứng nên câu hỏi "vì sao các em sửa ảnh này" có sẵn câu trả lời | 06, P-07, hội đồng |
| `quarantine_images.csv` | Ảnh `uncertain` — vẫn ở registry để đếm và truy vết, nhưng **không** ở clean pool. Đây là cách báo cáo trung thực mà không gọi nhầm ảnh chưa xác minh là "sạch" | 06, WARN-01 |
| `class_manifest.csv` cột `taxonomy_level` | Mỗi lớp ở cấp species / genus / family. IP102 trộn lẫn ba cấp, và điều đó ảnh hưởng tới cách diễn giải mọi kết quả fine-grained | 07, RQ4, datasheet |
| `G_blur_calibration.md` | `p*` đã chọn, hoặc kết luận **"không có ngưỡng blur nào dùng được"** — cả hai đều là kết quả hợp lệ và đều được báo cáo | 06 (nhánh 5 của cây H), MAJ-04 |

**Ô 5 phải hiện.** Bảng số ảnh theo từng hàng đợi và từng phán quyết (`keep` / `relabel` / `remove` / `uncertain`). Phân bố `self_confidence` của cleanlab. Lưới **20 ảnh khả nghi nhất kèm nhãn hiện tại và nhãn đề xuất** đặt cạnh nhau.

**Tự kiểm ở ô 6.** `needs_review == 0` ở thời điểm khóa (CRIT-08) · mọi dòng `uncertain`/`removed` có đủ `reason`, `decided_by`, `decided_at` (MAJ-03) · WARN-01 (`uncertain` ≤ 1%) · MAJ-04 (xóa vì blur ≤ 1% pool và ≤ 5% mỗi lớp) · MAJ-09 (kappa/alpha đã báo cáo) · WARN-08 (số cụm liên lớp ≤ 400).

**Thu hoạch.**

- **`needs_review = 0`** — điều kiện đi tiếp của Tuần 3. Không được chia dữ liệu khi nhãn còn treo, vì mọi tập K sinh từ nhãn treo đều phải làm lại.
- **Số cụm liên lớp thật.** `[ƯỚC LƯỢNG]` 200–350, nhưng đây là khoản duy nhất trong ngân sách người có thể phình 2–3 lần. Notebook này là nơi con số thật xuất hiện, và là nơi quyết định có kích hoạt chiến lược cắt giảm VII.6 hay không.
- **Kết luận về ngưỡng blur** — có dùng được hay không. Nếu ngay cả `p = 1` cũng không đạt precision 0,90, `blur_lapvar_norm` chuyển hẳn sang vai trò metadata thuần túy, và đó là một kết luận đúng chứ không phải một thất bại.
- **Tên lớp và cấp phân loại đã chuẩn hóa** — `[ĐÃ ĐO]` ít nhất 2 lỗi chính tả thấy được (`Polyphagotars onemus`, `Brevipoalpus`).

**Cạm bẫy.**

- **cleanlab chỉ sinh candidate, không quyết định** (P-10). Nó chạy k-fold ngẫu nhiên **group-aware trên toàn pool**, không phải trên split cuối — vì mục đích ở đây là sinh ứng viên chứ không phải ước lượng hiệu năng. Ghi rõ điều này để không ai nhầm con số cleanlab với con số hiệu năng.
- **Bất đồng đi thẳng vào `uncertain`.** Không có "người thứ ba phá hòa" — với 3 người rà thì người thứ ba luôn là cùng một người, và như vậy thực chất là quyết định đơn phương.
- **Cụm đa nhãn chỉ có hai lối ra:** relabel cho toàn cụm nhất quán, hoặc chuyển **toàn cụm** vào quarantine. Không giữ cụm đa nhãn để thuật toán split "tự xử", và không rà 30% rồi để 70% chưa giải quyết quay lại pool.
- Người chấm blur **không được biết ảnh thuộc dải phân vị nào** — có nhóm đối chứng trên `q_c(50)` trộn lẫn chính vì lý do đó.

**DONE khi.** `label_audit.csv` không còn dòng `needs_review` · mọi cụm đa nhãn đã relabel nhất quán hoặc quarantine toàn cụm · `G_blur_calibration.md` có chữ ký · Krippendorff alpha đã báo cáo.

*Phục vụ:* mọi RQ (chất lượng nhãn là nền) · *Công việc:* T12–T16 · *Phụ thuộc:* `04c`.

---

### 3.8. `06_build_clean_pool.ipynb`

**Một câu.** Áp cây quyết định H lên mọi phán quyết đã có, tách 75.222 ảnh thành ba tập không giao nhau, và dựng bản cache phát hành.

**Vì sao tồn tại.** Đây là nơi mọi phán quyết cleaning và label auditing hội tụ thành một tập ảnh duy nhất, và là **ranh giới mà sau nó không được xóa ảnh nữa**. Nếu định nghĩa "được vào pool" không phát biểu được bằng một biểu thức boolean thì `master_manifest` không tái lập được và REP-01 fail.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `A_inventory`, `B_integrity`, `C_duplicate_clusters`, `near_duplicate_clusters`, `G_quality`, `label_audit.csv`, `cache256_scan/` |
| Ghi | `image_registry.parquet`, `master_pool.parquet`, `quarantine_images.csv`, `removed_images.csv`, `cache256/` phát hành + `N_cache_manifest.parquet`, `class_manifest.csv`, `CHANGELOG_dataset.md` |
| Tham số ô 1 | `version="clean-v1.0"`, `link_mode="hardlink"` |

**Ý nghĩa từng đầu ra — và ba khái niệm phải tách bạch.**

| File | Nó là gì | Vì sao tách riêng |
|---|---|---|
| `image_registry.parquet` | **Sổ cái đủ 75.222 ảnh** và mọi phán quyết trên chúng | Để báo cáo được đầy đủ: "chúng tôi bắt đầu với 75.222 và đây là số phận của từng ảnh" |
| `master_pool.parquet` | **Chỉ `keep` và `relabeled`** | Đây mới là "dữ liệu sạch". Gọi registry là clean pool là một lỗi diễn đạt có hậu quả |
| `quarantine_images.csv` | `uncertain` | Không phải rác, cũng không phải sạch. Vẫn được đếm, không được dùng |
| `removed_images.csv` | `removed`, mỗi dòng có lý do, người quyết, thời điểm | P-07: không xóa lặng lẽ |
| `cache256/` + `N_cache_manifest.parquet` | Cache **phát hành**, bằng đúng tập manifest — không có orphan | CRIT-01. Cache scan ở `interim` được phép chứa ảnh đã loại; cache phát hành thì không |
| `class_manifest.csv` | 102 dòng: `n_clean`, `eligible`, `taxonomy_level`, điểm P/A/D | Đầu vào để sinh mọi tập K ở `07` |

**Ô 5 phải hiện.** Bảng phân hoạch registry / pool / quarantine / removed **có tổng kiểm** — bốn số phải cộng lại đúng 75.222. Biểu đồ số ảnh mất theo lớp, sắp giảm dần (lớp nào chịu thiệt nhất). Lưới **12 ảnh bị loại của mỗi lý do** — nếu 12 ảnh của một lý do trông hoàn toàn bình thường thì lý do đó đang loại quá tay.

**Tự kiểm ở ô 6.** CRIT-01 (file cache tồn tại, `cm.image_id` duy nhất, tập cache phát hành = tập manifest, ba tập là phân hoạch của registry) · CRIT-05 · MAJ-03 · MAJ-04 · MAJ-07 (cạnh ngắn = 256, hệ số phóng ≤ 2,286, PSNR trung vị ≥ 40 dB) · và đẳng thức `|pool| + |quarantine| + |removed| == 75.222`.

**Thu hoạch.**

- **Một phân hoạch có tổng kiểm** — mọi ảnh gốc đều có đúng một số phận, và số phận đó truy được về một dòng log.
- **Con số quyết định của HC-06: bao nhiêu lớp đạt sàn `n_c ≥ 334`.** Nếu ≥ 45 thì K45 tồn tại; nếu không, hạ xuống K40 rồi K35 và công bố phạm vi. Đây là lúc biết chắc, sau khi đã đếm sơ bộ ở Tuần 1 và Tuần 2.
- **Cache phát hành** — thứ mà mọi `DataLoader` của Giai đoạn 2 sẽ đọc, và thứ được đóng gói lên Kaggle.
- **Số ảnh mất theo lớp**, đi thẳng vào datasheet như một hạn chế đã biết.

**Cạm bẫy.**

- **Cache phát hành phải dựng bằng hardlink hoặc copy, không nén lại.** Nén lại lần hai làm PSNR tụt và MAJ-07 sẽ FAIL — mà lúc đó đã mất công dựng cả cache.
- Sau bước này, `S_alert` và `eligible` phải được **tính lại pass 2**, vì tập ảnh đã đổi. Cụm mới vượt ngưỡng mà bị bỏ qua là lỗ hổng của MAJ-05.
- Cây quyết định dừng ở **nhánh khớp đầu tiên**. Viết nó thành một chuỗi `if` độc lập thay vì `elif` là cách phổ biến nhất để hai nhánh cùng ăn một ảnh.
- **Không bao giờ ghi đè một `clean_vX` đã khóa** (P-09). Sửa gì thì tạo `clean_v1.1` và ghi một dòng vào `CHANGELOG_dataset.md`: cái gì đổi, bao nhiêu ảnh bị ảnh hưởng, ai quyết định, tại sao.

**DONE khi.** Phân hoạch cộng đủ 75.222 và ba tập đôi một không giao · cache phát hành khớp manifest, không orphan · số lớp `eligible` đã biết và HC-06 đã có kết luận · `CHANGELOG_dataset.md` có dòng đầu tiên.

*Phục vụ:* mọi RQ · *Công việc:* T19, T20 · *Phụ thuộc:* `05`.

---

### 3.9. `07_build_split.ipynb`

**Một câu.** Chia dữ liệu **một lần** trên toàn bộ 102 lớp theo cụm, rồi sinh mọi tập nghiên cứu bằng cách lọc từ một manifest duy nhất.

**Vì sao tồn tại.** P-04 nói mọi tập K sinh bằng lọc từ một manifest; notebook này biến nguyên tắc đó thành code. Bất biến duy nhất phải bảo vệ, phát biểu chính xác: *với mọi cặp ảnh `(i, j)` mà `near_duplicate_group(i) == near_duplicate_group(j)`, ta có `split(i) == split(j)`.* Vì cụm là thành phần liên thông, bất biến này bao hàm cả các cặp bắc cầu — và đó là điều đúng, vì nếu A ở train và C ở test trong khi B ở train thì C vẫn rò rỉ qua B.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `master_pool.parquet`, `class_manifest.csv`, `PAD_ranking_locked.csv`, `configs/split_config.yaml` |
| Ghi | `master_manifest.parquet` + `.csv`, `sets/*` (11 file), `split_statistics.csv`, `build_info.json` |
| Tham số ô 1 | `config="configs/split_config.yaml"`, `out="runs/A"` |

**Bốn pha của thuật toán** (chi tiết ở VIII.2): **pha 0** fail-fast chặn cụm đa nhãn — gán split không giải quyết được mâu thuẫn nhãn nên không được che nó bằng heuristic; **pha 1** tham lam LPT theo từng lớp, tie-break bằng `cluster_id` chứ không bằng RNG; **pha 2** sửa chữa cục bộ bằng move và swap; **pha 3** quy hoạch động thưa, chỉ chạy cho lớp mà greedy+repair chưa đạt — nhờ vậy phân biệt được "heuristic thất bại" với "bài toán thật sự vô nghiệm".

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `master_manifest.parquet` | **Nguồn sự thật duy nhất.** Mọi tập K về sau chỉ là view của nó | mọi thứ |
| `master_manifest.csv` | Bản đọc được bằng mắt, `image_id` ép kiểu string | người |
| `sets/K15, K20_VN, K35, {K45\|K40\|K35}` | Các tập quy mô lồng nhau **và đúng thứ hạng** P/A/D | E5, E6, E7 |
| `sets/K20_Count` | 20 lớp nhiều ảnh sạch nhất — **tập tham chiếu, không phải đối chứng nhân quả** | E2 |
| `sets/K20_FG`, `K20_FG_matched` | 20 lớp tương đồng hình thái; bản matched chỉ tạo khi bản gốc trượt ràng buộc ±20% IR / ±15% tổng ảnh train | E2, RQ4 |
| `sets/test_balanced_K20_VN` | 50 ảnh/lớp từ Natural Test, **tối đa hóa đa dạng cụm** | E9 |
| `sets/ood_heldout_calib`, `ood_heldout_eval` | 82 lớp ngoài K20-VN, chia 30/70 **theo cụm** | E9 |
| `sets/E1_orig_split_matched`, `E1_group_split_matched` | Hai nhánh khớp số lượng ảnh train từng lớp, hạ mẫu **theo cụm nguyên vẹn** | E1 — bằng chứng chính của RQ1 |
| `split_statistics.csv` | 102 dòng: mục tiêu và thực tế 70/15/15, cột lệch, cờ `hc02_violated` | MAJ-01, datasheet |
| `build_info.json` | seed, checksum config, commit git, phiên bản, `accepted_risks[]` | REP-02, đóng gói |

**Ô 5 phải hiện.** Bảng 102 dòng: mục tiêu và thực tế của 70/15/15 từng lớp, kèm cột lệch. Histogram độ lệch từng lớp có đánh dấu ngưỡng ±3 điểm phần trăm. Danh sách các lớp bị đặt `eligible = False` kèm lý do — **nêu tên, không chỉ đếm**.

**Tự kiểm ở ô 6.** CRIT-03 (không ảnh nào ở hai split) · CRIT-04 (cụm không bị chia) · CRIT-07 (tập K là view, không phải bản sao) · CRIT-09 và CRIT-09b (OOD hợp lệ và chia theo cụm) · CRIT-10 (`test_balanced`) · CRIT-11 (PTQ lấy từ train) · CRIT-12 (một nhãn cuối mỗi cụm) · CRIT-13 (sàn HC-01) · CRIT-14 (lồng nhau **và đúng thứ hạng**) · CRIT-15 (song ánh lớp ↔ chỉ số, hai chiều) · MAJ-01 · MAJ-11 · MAJ-13.

**Thu hoạch.**

- **Một phép chia duy nhất, xác định hoàn toàn.** Tie-break bằng `cluster_id` chứ không bằng RNG nghĩa là chạy hai lần cho cùng một kết quả bit-for-bit — điều kiện của REP-01.
- **Mười một file trong `sets/`** — toàn bộ "hợp đồng dữ liệu" giao cho Giai đoạn 2. Sau notebook này, E1–E10 không cần chia lại gì nữa.
- **Biết chính xác lớp nào không đo được** và vì sao. Thà có 18 lớp đo được còn hơn 20 lớp trong đó 2 lớp cho con số không diễn giải nổi.
- **`build_info.json`** — dấu vân tay của cả dataset, thứ mà REP-02 sẽ đối chiếu.

**Cạm bẫy.**

- **Không sửa CRIT-04 bằng cách tách cụm.** Nếu một cụm làm phép chia bất khả thi, thứ tự ưu tiên vi phạm là: (1) toàn vẹn cụm — không có ngoại lệ; (2) sàn HC-01 theo thứ tự test ≥ 50 → val ≥ 30 → train ≥ 100; (3) tỷ lệ ±3 điểm; (4) con số 70/15/15 chính xác — thẩm mỹ.
- **`sorted()` ở mọi vòng lặp sinh ra thứ tự gán.** `dict`/`set` không đảm bảo thứ tự lặp và `os.listdir` không sắp xếp; đây là nguyên nhân số một của "chạy hai lần ra hai kết quả", và nó **không bao giờ báo lỗi** — chỉ âm thầm cho hash khác.
- Khi DP ở pha 3 trả về `None`, **báo infeasible chứ không đổi seed mù**. Đổi seed để "may ra được" là cách biến một bài toán vô nghiệm thành một kết quả không tái lập.
- Hạ mẫu K20-FG-matched và cặp E1 phải **theo cụm**, không theo ảnh. Hạ theo ảnh phá vỡ tính nguyên vẹn cụm và làm hỏng chính bất biến chống rò rỉ.
- Tên file của Kmax phải là **kích thước thật** (`K45.csv` hoặc `K40.csv` hoặc `K35.csv`), không giả tên K45 khi thực tế là K40 (MAJ-11).

**DONE khi.** Mọi test Critical liên quan PASS · `split_statistics.csv` đã được đọc và mọi lớp vi phạm MAJ-01 đã có văn bản chấp nhận rủi ro trong `build_info.accepted_risks[]` · chạy hai lượt `runs/A` và `runs/B` cho hash chuẩn tắc giống hệt.

*Phục vụ:* toàn bộ RQ1–RQ7 · *Công việc:* T21, T22 · *Phụ thuộc:* `06`.

---

### 3.10. `08_validate_dataset.ipynb`

**Một câu.** Chạy toàn bộ 40 test của cổng chất lượng, so hash hai lượt chạy, và trả về một câu trả lời nhị phân: dataset có được phép chuyển sang training hay không.

**Vì sao tồn tại.** Vì phần lớn lỗi dữ liệu **không** làm chương trình dừng. Chúng làm con số sai đi một chút, ở chỗ không ai nhìn, cho tới khi hội đồng nhìn. Cổng chất lượng là nơi biến 40 giả định ngầm thành 40 assertion chạy được.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `master_manifest`, `sets/*`, `image_registry`, `N_cache_manifest`, `runs/A` và `runs/B`, `build_info.json` |
| Ghi | `reports/quality_gate_report.json`; mã thoát `0` khi mọi Critical PASS, `1` khi có Critical FAIL |
| Tham số ô 1 | `strict=True`, `compare=("runs/A","runs/B")` |

**Ý nghĩa đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `quality_gate_report.json` | 40 dòng test kèm PASS/FAIL và thời gian chạy; `critical_passed` / `critical_total`; danh sách `accepted_risks` cho các Major được chấp nhận | checklist Mục XV, đóng gói Kaggle, báo cáo 12 acceptance test (deliverable #18) |

**Ba mức và luật cổng.** **Critical** phải PASS 100% — không có cờ dòng lệnh nào bỏ qua được, và đó là chủ ý thiết kế. **Major** được phép FAIL nhưng phải có văn bản chấp nhận rủi ro ghi vào `build_info.json` với `test_id`, `reason`, `accepted_by`, `accepted_at`. **Warning** chỉ ghi log.

**Ô 5 phải hiện.** Bảng 40 dòng test với cột PASS/FAIL và cột thời gian chạy. Với **mỗi Critical FAIL: hiện 12 bản ghi vi phạm đầu tiên** — một test báo FAIL mà không chỉ ra bản ghi nào sai thì người sửa phải đi dò tay, và đó là nửa giờ mất trắng mỗi lần.

**Tự kiểm ở ô 6.** Chính nó là ô tự kiểm của cả tuyến. Thêm hai việc riêng: REP-01 (hash chuẩn tắc của `runs/A` bằng `runs/B`) và REP-02 (dựng lại toàn bộ từ `data/raw` + commit + config, so với `build_info["manifest_sha256"]`). Ô 5 còn phải **in lại tổng giờ máy và giờ người từ chính bảng XII.5**, để bảng đổi thì tổng đổi theo.

**Thu hoạch.**

- **Một câu trả lời nhị phân** thay cho một cuộc thảo luận: được train, hay chưa.
- **Bằng chứng trực tiếp cho acceptance test #12 của đề cương** (REP-02) — thứ mà "chúng em có ghi seed" không thay thế được.
- **Danh sách rủi ro đã chấp nhận, có chữ ký** — khác hẳn với danh sách lỗi bị bỏ quên.
- Hash chuẩn tắc theo định nghĩa cố định (sắp theo `image_id`, thứ tự cột cố định, float 6 chữ số, UTF-8 không BOM, `\n`) — nghĩa là từ nay "giống hệt" có định nghĩa, và câu ACC-12 vốn không kiểm được bằng code đã kiểm được.

**Cạm bẫy.**

- **Chạy thử notebook này ngay từ Tuần 3**, trên `master_pool` chưa split — nhiều test đã kiểm được, và một Critical FAIL lộ ra ngày 24/9 gần như chắc chắn làm trượt mốc 29/9.
- REP-02 tốn `[ƯỚC LƯỢNG]` khoảng 1 h. Chạy nó **một lần trước khi khóa** và một lần nữa sau bất kỳ thay đổi code nào — đừng để nó thành việc của buổi chiều cuối cùng.
- `git_dirty == false` là điều kiện của milestone Tuần 4; một commit dở dang làm `build_info` ghi một commit không dựng lại được.

**DONE khi.** `critical_passed == critical_total` · mọi Major FAIL có dòng trong `accepted_risks[]` · REP-01 và REP-02 đều PASS · `quality_gate_report.json` đã commit.

*Phục vụ:* mọi RQ · *Công việc:* T23, T24 · *Phụ thuộc:* `07`.

---

### 3.11. `09_eda.ipynb`

**Một câu.** Sinh 20 biểu đồ bốn tầng cùng phân tích mất cân bằng, **chỉ trên train và validation**, mỗi biểu đồ gắn với một hành động cụ thể khi kết quả bất thường.

**Vì sao tồn tại.** Nguyên tắc lọc của Mục IX: một biểu đồ được giữ khi trả lời được ba câu — *nó trả lời câu hỏi gì · nếu kết quả bất thường thì làm gì · ai sẽ đọc nó*. Không trả lời được câu thứ hai thì loại. Bảy biểu đồ quen thuộc đã bị loại vì lý do đó (histogram RGB, word cloud, pie chart tỷ lệ split, ma trận tương quan toàn bộ, t-SNE bên cạnh UMAP, lưới ảnh ngẫu nhiên không phân tầng, dung lượng file theo lớp).

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `master_manifest.parquet`, `D_embeddings.npy`, `G_quality.parquet`, `near_duplicate_clusters.csv` |
| Ghi | `reports/EDA_report.md`, `reports/figures/eda/*.png`, `data/interim/M_eda_tables.xlsx`, `reports/L_imbalance_analysis.md` |
| Tham số ô 1 | `tiers=(1,2,3,4)` |

**Bốn tầng và câu hỏi của mỗi tầng.**

| Tầng | Mức | Câu hỏi chủ đạo | Số biểu đồ |
|---|---|---|---|
| 1 | Dataset | Bộ dữ liệu sau làm sạch có đúng như dự kiến không? | 4 |
| 2 | Chất lượng | Cụm có phình không, và blur score có đang đo nhầm độ phân giải không? | 5 |
| 3 | Lớp | Mất cân bằng thật đến mức nào, và **Tail chỉ ít hay còn kém**? | 6 |
| 4 | Biểu diễn | Các lớp có tách được trong không gian embedding không? Cặp nào dễ nhầm? | 5 |

**Ô 5 phải hiện.** 20 bảng của bốn tầng, 20 biểu đồ của Mục IX, và lưới ảnh đại diện **gần tâm và xa tâm** của mỗi lớp — hai đầu của mỗi lớp nói nhiều hơn một mẫu ngẫu nhiên.

**Tự kiểm ở ô 6.** Assertion HC-08: **mọi biểu đồ tầng 4 sinh từ file có `split ∈ {train, val}`** — kiểm bằng code chứ không bằng lời hứa · WARN-02 (IR theo ảnh so IR theo cụm) · WARN-03 · WARN-05 · WARN-07 · định nghĩa Head/Medium/Tail có timestamp sớm hơn mọi file kết quả mô hình.

**Thu hoạch.**

- **IR thực đo thay cho số dự kiến.** HC-10 nói rõ: tỷ lệ mất cân bằng phải báo cáo bằng số đo thực tế; con số `≈ 24,9×` hiện chỉ là dự kiến sau khi lọc về 20 lớp. Notebook này thay nó bằng số thật.
- **Câu trả lời cho câu hỏi quan trọng nhất của E3: Tail chỉ ít dữ liệu, hay còn kém chất lượng hơn?** (E3-4, heatmap Spearman `log n_c` × 6 biến chất lượng). Nếu Tail vừa ít vừa mờ, E3 sẽ kết luận sai về hiệu quả các chiến lược cân bằng — cải thiện có thể đến từ bù dữ liệu, hoặc từ bù nhiễu, và hai thứ đó cần hai cách xử lý khác nhau.
- **Đường cong Lorenz và hệ số Gini** — đi thẳng vào Chương 3, và là căn cứ thiết kế E3.
- **Cặp lớp dễ nhầm** (E4-3 heatmap k-NN) — đầu vào cho việc chọn K20-FG và cho phân tích ma trận nhầm lẫn của E5, ghi lại **trước** khi chạy E5 để so.
- **`scene_diversity_c`** — lớp nào mà train chỉ gồm vài cảnh chụp. Dưới 0,5 kích hoạt WARN-05 và phải ghi vào datasheet.

**Cạm bẫy.**

- **Test không được đưa qua bất kỳ mô hình nào ở giai đoạn này, kể cả chỉ để trích embedding.** Đây là HC-08 và P-03 cùng lúc, và nó phải được bảo vệ bằng assertion trong chính notebook.
- Đừng vẽ lại bảy biểu đồ đã bị loại ở IX.6. Chúng bị loại vì không dẫn tới hành động nào, và vẽ chúng làm loãng báo cáo.
- Notebook này là **P1 và được phép trượt sang Tuần 5**. Nó không phải điều kiện của bất kỳ test Critical nào, và Giai đoạn 2 chạy E1–E4 không cần nó có mặt ngày đầu. Đây là dư địa được thiết kế sẵn cho Tuần 4 — dùng nó khi cần, đừng hy sinh `08` để kịp `09`.

**DONE khi.** 20 biểu đồ đã sinh · assertion HC-08 PASS · `EDA_report.md` có phần diễn giải chứ không chỉ có hình · IR thực đo đã thay số dự kiến trong mọi chỗ nhắc tới HC-10.

*Phục vụ:* RQ2, RQ3, RQ4 · *Công việc:* T27, T29 · *Phụ thuộc:* `08`.

---

### 3.12. `10_crop_study.ipynb`

**Một câu.** Kiểm kê nhánh detection của IP102, rồi đo bằng dữ liệu xem `resize 256 → center crop 224` có cắt mất côn trùng hay không.

**Vì sao tồn tại.** Đề cương chốt resize cạnh ngắn 256 → center crop 224 ở cả train lẫn Flutter, **không kèm bằng chứng nào về mức mất đối tượng**. Với trung vị ảnh IP102 `[ĐÃ ĐO]` 439×325 (aspect ratio 1,35), center crop giữ 64,7% chiều rộng — bỏ 17,7% mỗi bên. Với ảnh `ar = 2,0` chỉ giữ 43,8%. Đó mới là số học; câu hỏi thật là **đối tượng có nằm trong phần bị bỏ không**, và câu đó phải trả lời bằng dữ liệu. May mắn là IP102 có sẵn nhánh detection để trả lời.

Nếu không làm notebook này, rủi ro là **trần hiệu năng bị đặt bởi tiền xử lý chứ không bởi mô hình** — và nhóm sẽ dành cả Giai đoạn 3 tối ưu kiến trúc để bù cho một quyết định crop.

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `Annotations/` (XML), `JPEGImages/`, `master_manifest` hoặc `A_inventory` |
| Ghi | `N_bbox_inventory.csv`, `N_bbox_crop_analysis.csv`, `reports/N_crop_loss_study.md` |
| Tham số ô 1 | — |

**Ý nghĩa từng đầu ra.**

| File | Nó nói lên điều gì | Ai tiêu thụ |
|---|---|---|
| `N_bbox_inventory.csv` | `n_xml`, `n_jpeg`, `n_image_id_mapped`, `n_boxes`, và danh sách lớp không được phủ. **Ba con số đầu không được giả định là bằng nhau**: bài báo IP102 công bố 18.983 ảnh detection, báo cáo cục bộ ghi 18.981 ảnh trong `JPEGImages/` phủ 96/102 lớp | chính notebook này, datasheet |
| `N_bbox_crop_analysis.csv` | Mỗi bbox ánh xạ được một dòng: `retained` (phần diện tích bbox còn lại trong khung crop) và `bbox_area_frac` | quyết định preprocessing |
| `N_crop_loss_study.md` | Bốn chỉ số và kết luận hành động | `preprocess_v1.yaml`, E4, E5, E10, §5.6 của đề cương |

**Bốn con số và ngưỡng hành động của từng con số.**

| Chỉ số | Kết quả | Hành động |
|---|---|---|
| `P(retained < 0,50)` | < 3% | Giữ nguyên center crop; ghi con số vào báo cáo làm bằng chứng |
| | 3–10% | Giữ center crop cho eval (khớp Flutter) nhưng dùng `RandomResizedCrop(scale=(0.65,1.0))` cho train, và báo cáo rủi ro |
| | > 10% | Cân nhắc đổi sang resize cạnh dài 256 + pad về vuông, đổi ở **cả hai phía** train và Flutter |
| `P(bbox_area_frac < 0,05)` | Cao | Bài toán đối tượng nhỏ nghiêm trọng: sau khi về 224, đối tượng dưới 50×50 px. Ghi vào phần hạn chế |
| `P(retained < 0,50 \| ar > 2)` so toàn cục | Chênh lớn | Xác nhận hoặc hạ ngưỡng cờ `extreme_ar = 4,0` xuống mức đo được |
| `retained` trung vị theo lớp | Vài lớp thấp bất thường | Đưa vào phân tích lỗi E5 — có thể là lời giải thích cho lớp có F1 thấp |

**Ô 5 phải hiện.** Bảng kiểm kê nhánh detection và bốn chỉ số. Phân bố phần đối tượng còn lại sau khi cắt giữa. Lưới **12 ảnh bị cắt mất nhiều nhất, vẽ khung đối tượng đè lên** — đây là hình khiến quyết định crop trở nên hiển nhiên với người xem.

**Tự kiểm ở ô 6.** `retained` nằm trong `[0, 1]` với mọi dòng · số bbox ánh xạ được khớp `N_bbox_inventory` · WARN-04 (`P(bbox_retained_224 < 0.5) ≤ 0.03`).

**Thu hoạch.**

- **Một quyết định preprocessing có căn cứ**, thay vì một quyết định kế thừa từ đề cương. Và quan trọng không kém: nếu số liệu nói "giữ center crop", nhóm có một con số để trả lời khi bị hỏi.
- **Biết nhánh detection của IP102 thật sự có bao nhiêu ảnh ánh xạ được** — ba nguồn số liệu đang không khớp và chưa ai đối chiếu.
- **Sáu lớp không được phủ annotation** được nêu tên, không chỉ đếm.
- Notebook này **độc lập với toàn bộ nhánh gần trùng**, nên chạy được ngay Tuần 1 (T7 06/9) trong lúc chờ embedding và ứng viên.

**Cạm bẫy.**

- **Không hard-code 18.983 dòng.** Chạy trên toàn bộ annotation ánh xạ thành công, và báo cáo con số thật.
- Phép so sánh center crop và padding không có câu trả lời tiên nghiệm: padding không bao giờ cắt mất đối tượng nhưng đưa vào viền chiếm tới 50% diện tích ở ảnh `ar = 2` và tạo một đặc trưng nhân tạo (vị trí viền) tương quan với aspect ratio. Nếu phải chọn, cách kiểm chứng là **một lượt chạy đối chứng trong E4 với đúng một biến thay đổi**; chênh lệch dưới `max(0,005; 2σ)` thì giữ center crop vì nó khớp ràng buộc triển khai.

**DONE khi.** Bốn chỉ số đã có số · kết luận hành động đã ghi vào `N_crop_loss_study.md` · `configs/preprocess_v1.yaml` phản ánh đúng kết luận đó.

*Phục vụ:* RQ7 (E10), E4, E5, §5.6 · *Công việc:* T26 · *Phụ thuộc:* `01`.

---

### 3.13. `11_leakage_report.ipynb`

**Một câu.** Đo mức rò rỉ gần trùng trên **phép chia gốc của IP102** tại hai điểm hoạt động, và dựng toàn bộ bảng/hình của RQ1.

**Vì sao tồn tại.** Con số notebook này sinh ra sẽ xuất hiện trong Chương 3 báo cáo dưới dạng một **tuyên bố khoa học**: "phép chia gốc IP102 có X% ảnh đánh giá gần trùng với train". Nếu bộ phát hiện gắn cờ quá tay, nhóm thổi phồng mức rò rỉ và hội đồng có quyền bác. Đó là lý do tầng ĐO dùng `τ_strict` (bảo vệ precision) còn tầng CHẶN ở `07` dùng `τ_recall` (bảo vệ recall).

**Đọc / Ghi.**

| | Nội dung |
|---|---|
| Đọc | `master_manifest` (có cả `orig_split` và `split`), `near_duplicate_clusters.csv`, `E_orig_leakage_flags`, `cleaning_config.yaml` **đã commit** |
| Ghi | `reports/leakage_report.md`, `reports/figures/leakage/*.png` |
| Tham số ô 1 | `tau=("strict","recall")` |

**Ý nghĩa từng đầu ra.**

| Mã | Nội dung | Câu hỏi nó trả lời |
|---|---|---|
| B-L1 | Tổng hợp rò rỉ theo split gốc, hai ngưỡng, CI 95% | Mức rò rỉ tổng thể là bao nhiêu, và bất định đến đâu |
| B-L2 | 102 dòng rò rỉ theo lớp, có cột "thuộc K20-VN?" | Rò rỉ tập trung ở đâu |
| B-L3 | Phân bố kích thước cụm | Cụm có phình không (đề cương yêu cầu báo cáo) |
| B-L4 | Ma trận cụm × split gốc | Bao nhiêu cụm đi qua ranh giới split |
| H-L1 | Histogram Hamming của cặp gần nhất, tô màu theo nhãn người | Ngưỡng nên đặt ở đâu, và vì sao |
| H-L2 | Hai panel hiệu chuẩn | Hai ngưỡng đặt đúng chỗ chưa |
| H-L3 | Rò rỉ theo lớp, sắp giảm, tô đậm 20 lớp K20-VN | Rò rỉ có nhắm vào lớp ta quan tâm không |
| H-L4 | Scatter rò rỉ × `log n_c` + hồi quy | Lớp lớn có rò rỉ nhiều hơn không |
| **H-L5** | **12 cặp train‖test đặt cạnh nhau, kèm `H` và `MSE`** | **Bằng chứng trực quan — hình mà hội đồng sẽ nhớ** |
| H-L6 | Chênh lệch Macro-F1 giữa test gần trùng và không gần trùng | Bằng chứng chính của RQ1 — **thuộc Giai đoạn 2**, khung hình chuẩn bị sẵn ở đây |

**Ô 5 phải hiện.** B-L1 đến B-L4 và H-L1 đến H-L4, cộng lưới H-L5. Notebook cũ `03_ChungMinh_PhatHien.ipynb` đã dựng hình tương tự (`phat_hien_H1_ro_ri_cap_anh.png`) — **tái sử dụng bố cục, chỉ thay số liệu bằng bản đã hiệu chuẩn**.

**Tự kiểm ở ô 6.** MAJ-06 ngay tại **ô 3**, không chờ tới Mục X: `leakage_meta.cleaning_config_sha256 == build_info.configs.cleaning_config_sha256` và `calibration_locked_at < leakage_started_at`. Đây là quy ước bắt buộc số 6 của Mục XII.3 — không notebook nào đọc `orig_split == "test"` trước khi config đã commit.

**Thu hoạch.**

- **Con số của Chương 3**, có CI 95%, tại hai điểm hoạt động. `[ĐÃ ĐO]` hiện có 9,35% ở `H ≤ 2` và 11,94% ở `H ≤ 5` + `MSE < 300`, nhưng cả hai đều là **rò rỉ trong lớp** vì phép đo cũ chỉ tìm ảnh train gần nhất *trong cùng lớp*.
- **Rò rỉ liên lớp — lần đầu được đo.** Đây là lỗ hổng thật sự của phép đo hiện có: một ảnh test gần trùng với ảnh train của lớp khác vẫn là rò rỉ (mô hình đã thấy điểm ảnh đó) **và** đồng thời là bằng chứng nhãn mâu thuẫn.
- **Tỷ lệ tách riêng cho val và cho test** — mẫu số của E1, hiện chưa ai tách.
- **Số cụm xuyên split và số ảnh bị ảnh hưởng**, trên đồ thị đầy đủ chứ không chỉ ở `H = 0` (`[ĐÃ ĐO]` 1.411 nhóm).

**Cạm bẫy.**

- **Không được chọn cờ sau khi xem chênh lệch.** Phân tích chính của E1 tách test gốc theo cờ `strict`; cờ `recall` là sensitivity analysis. Quyết định này đã được đăng ký trước và không được đảo sau khi thấy kết quả.
- **Nhánh cụt là có chủ ý.** Notebook này không quay lại chỉnh tham số của `04b`/`04c`. Nếu con số "chưa đẹp", đó là con số.
- Cám dỗ "chỉnh ngưỡng cho con số rò rỉ đẹp hơn" là có thật, và không ai phát hiện được nếu không có hàng rào hash + timestamp. Đừng tự làm khó mình bằng cách chạy notebook này trước khi commit config.

**DONE khi.** MAJ-06 PASS · B-L1..B-L4 và H-L1..H-L5 đã sinh · rò rỉ liên lớp đã có số · `leakage_report.md` nói rõ đâu là số ở `τ_strict` và đâu là số ở `τ_recall`.

*Phục vụ:* RQ1 (E1) · *Công việc:* T25 · *Phụ thuộc:* `04c` **và** `cleaning_config.yaml` đã commit.

---

## 4. Ba bảng truy vết

### 4.1. Notebook → test cổng chất lượng

Cột "tự kiểm" là những test mà chính notebook đó phải chạy ở ô 6; cột "bị kiểm lại" là nơi `08` chấm lại lần hai.

| Notebook | Tự kiểm ở ô 6 | Nếu FAIL thì hỏng ở đâu |
|---|---|---|
| `01_scan` | CRIT-02, MAJ-07 (một phần) | Toàn bộ mẫu số của mọi phép đếm |
| `03_exact_duplicates` | CRIT-05, CRIT-06 | `n_c` sai → trọng số E3 sai |
| `04a_embed` | (nội bộ: số dòng, NaN, index) | Kênh phát hiện hình học biến mất |
| `04b_nd_candidates` | (nội bộ: phép thử tổng hợp, đủ tầng) | Hiệu chuẩn không có nguồn sự thật |
| `04c_nd_cluster` | Cổng hiệu chuẩn, MAJ-09, MAJ-12 | Mọi cụm sai → mọi thứ sau Tuần 2 sai |
| `05_label_audit` | CRIT-08, MAJ-03, MAJ-04, WARN-01, WARN-08 | Nhãn treo lọt vào tập K |
| `06_build_clean_pool` | CRIT-01, CRIT-05, MAJ-03, MAJ-04, MAJ-07 | Cache orphan, phân hoạch không cộng đủ |
| `07_build_split` | CRIT-03/04/07/09/09b/10/11/12/13/14/15, MAJ-01/11/13 | Rò rỉ tái sinh trong chính benchmark |
| `08_validate_dataset` | **cả 40 test** + REP-01 + REP-02 | — (đây là nơi chấm) |
| `09_eda` | HC-08, WARN-02/03/05/07 | Diễn giải E3 sai hướng |
| `10_crop_study` | WARN-04 | Trần hiệu năng bị đặt bởi preprocessing |
| `11_leakage_report` | MAJ-06 (ở **ô 3**) | Tuyên bố khoa học của Chương 3 mất giá trị |

### 4.2. Notebook → RQ và thí nghiệm

| RQ | Thí nghiệm | Notebook giao hàng | Thứ được giao |
|---|---|---|---|
| RQ1 | E1 | `04c`, `07`, `11` | Hai phép chia song song, hai cờ rò rỉ, cặp train khớp số lượng |
| RQ2 | E2 | `07`, `09` | K20-VN và K20-Count là view của cùng manifest; bảng E4-5 |
| RQ3 | E3, E4 | `03`, `06`, `07`, `09` | `n_train_c` chính xác, IR theo ảnh và theo cụm, Head/Medium/Tail |
| RQ4 | E5, E6, E7 | `07` | Các tập K lồng nhau đúng thứ hạng, cùng split, cùng nhãn |
| RQ5 | E8 | `07` | Đúng tập train đã khóa; Giai đoạn 1 **không sinh dữ liệu KD mới** |
| RQ6 | E9 | `07` | `test_balanced`, held-out-class OOD chia 30/70 theo cụm |
| RQ7 | E10 | `07`, `10` | Tập hiệu chuẩn PTQ lấy từ train; preprocessing khớp Flutter |

### 4.3. Notebook → tuần → công việc

| Tuần | Ngày | Notebook chạy | Công việc |
|---|---|---|---|
| **T1** 01–07/9 | T2 | (dựng khung thư mục, config rỗng) | T01 |
| | T3 | `01_scan` | T02, T03 · song song ⟨người⟩ chấm P/A/D (T17) |
| | T4 | `03_exact_duplicates` | T04 · đếm sơ bộ lớp đạt sàn 334 |
| | T5 | `04a_embed` | T05 · T14 chuẩn hóa tên lớp |
| | T6 | `04b_nd_candidates` | T06, T07 |
| | T7 | `10_crop_study` | T26 |
| **T2** 08–14/9 | T2–T3 | ⟨người⟩ Label Studio 600 cặp | T08 |
| | T4 | `04c_nd_cluster` (hiệu chuẩn) | T09 · **commit `cleaning_config.yaml`** |
| | T5 | **ngày dự phòng cổng hiệu chuẩn** | T10, T11 nếu PASS |
| | T6–T7 | `04c` (rà cụm lớn), `05` (hiệu chuẩn blur) | T11, T16, T20 |
| | CN | `11_leakage_report` | T25 — chỉ sau khi config đã commit |
| **T3** 15–21/9 | T2–T5 | `05_label_audit` | T12, T13, T28 |
| | T6 | ⟨người⟩ khóa bảng P/A/D | T17 |
| | T7 | `06_build_clean_pool` | T18, T19 |
| | CN | (chạy thử `08` sớm) | T15 nếu còn giờ |
| **T4** 22–28/9 | T2–T3 | `07_build_split` | T21, T22 |
| | T4–T5 | `08_validate_dataset` | T23, T24 |
| | T6 | `09_eda`, hoàn thiện `11` | T27, T29 |
| | T7 | đóng gói Kaggle + datasheet | T30 |
| | CN | **KHÓA DATASET** — checklist Mục XV | — |

---

## 5. Ba kịch bản chạy

**Kịch bản 1 — chạy lần đầu, có người ngồi xem.** Mở từng notebook theo thứ tự phụ thuộc, luôn **Restart & Run All**, đọc ô 5 trước khi sang notebook tiếp theo. Đây là chế độ mặc định trong Tuần 1–3, và lý do dùng notebook thay vì script: phần lớn lỗi dữ liệu chỉ lộ ra khi nhìn thấy phân bố, không lộ ra qua mã thoát bằng 0.

**Kịch bản 2 — chạy lại toàn tuyến cho REP-02.** Dùng `papermill` chạy tám notebook của tuyến chính không mở giao diện, mỗi notebook ghi ra một bản có output trong `runs/rep02/`, rồi so hash chuẩn tắc của `master_manifest.parquet` với `build_info["manifest_sha256"]`. Hai điểm cần nói rõ với người đọc báo cáo: `papermill` chạy tuần tự từ ô 0 nên **không có trạng thái ẩn**, đúng bằng mức đảm bảo của một script; và các bước có người ở giữa đọc kết quả người đã lưu như một file đầu vào bình thường — REP-02 không yêu cầu chấm lại bằng tay, mà yêu cầu **cùng file nhãn thì cho cùng dataset**.

**Kịch bản 3 — sửa một bước ở giữa.** Sửa notebook nào thì chạy lại từ notebook đó trở đi, không chạy lại từ đầu — trừ khi thứ sửa nằm trong `01` hoặc `04c`. Sửa `04c` sau Tuần 2 nghĩa là làm lại từ T10, và mọi kết quả rò rỉ đã sinh phải hủy (MAJ-06). Sau bất kỳ lần sửa nào, chạy lại `08` trước khi tuyên bố xong.

**Sau mỗi lượt chạy chính thức**, xuất notebook ra HTML kèm output vào `reports/notebooks/{ngày}/`. Đây là cách cả nhóm và giảng viên hướng dẫn xem được kết quả mà không phải cài môi trường, và là ảnh chụp trạng thái tại thời điểm đó. **File HTML commit được; file `.ipynb` thì không.**

---

## 6. Checklist trước khi commit một notebook

Bảy dòng, kiểm bằng mắt trong hai phút:

1. Đủ **bảy khối ô**, đúng thứ tự, ô 1 có thẻ `parameters` và **chỉ chứa tham số**.
2. **Không có định nghĩa hàm nghiệp vụ nào trong notebook.** Nếu một đoạn code cần được test hoặc được gọi lại lần thứ hai, nó thuộc về `src/pestid/`.
3. Ô 5 có **đủ ba thứ**: bảng tóm tắt (có cột % bên cạnh cột tuyệt đối), biểu đồ phân bố, và lưới ảnh mẫu nếu bước này ra phán quyết trên ảnh.
4. Ô 6 chạy **đúng những assertion của cổng chất lượng liên quan tới bước này** và in PASS/FAIL.
5. Ô 7 ghi `{notebook}_run.json`: thời điểm, commit git, phiên bản thư viện, hash mọi input và output, `execution_count` cuối, và `clean_run: true|false`.
6. Mọi vòng lặp sinh ra thứ tự gán đều duyệt trên danh sách đã **`sorted()`**.
7. Đã ghép cặp `jupytext` sang `.py` dạng percent; **git theo dõi bản `.py`**, `.gitignore` loại `.ipynb`.

Và hai điều không bao giờ được vi phạm: notebook **không ghi vào `data/raw`**, và notebook **không ghi đè một thư mục `clean_vX` đã khóa**.

---

## 7. Những gì không thuộc về notebook

Ranh giới này là thứ giữ cho tuyến xử lý tái lập được, nên nó đáng được viết ra rõ ràng:

| Việc | Thuộc về đâu | Vì sao không ở notebook |
|---|---|---|
| Mọi hàm nghiệp vụ | `src/pestid/*.py` | Cần unit test và cần gọi lại lần thứ hai |
| 40 assertion của cổng | `src/pestid/gate.py` | Notebook `08` gọi chúng, không định nghĩa chúng |
| Bảng màu và style biểu đồ | `src/pestid/viz.py` | Mọi hình trong báo cáo phải dùng chung một bảng màu |
| Ngưỡng và tham số | `configs/*.yaml` | Ngưỡng rải trong code là ngưỡng không khóa được bằng checksum |
| Chấm nhãn, chấm P/A/D | Label Studio + bàn giấy | Notebook đọc kết quả người như một file đầu vào |
| Hồ sơ hiệu chuẩn có chữ ký | `docs/data/*.md` | Chữ ký là hành vi của người, không phải đầu ra của code |

Câu rút gọn của cả mục này, lấy nguyên từ tài liệu mẹ: **notebook chỉ được chứa lời gọi, câu lệnh hiển thị, và văn bản giải thích.**

---

## 8. Bốn câu hỏi sổ tay này chưa trả lời được

Ghi ra để không ai tưởng chúng đã được giải quyết:

1. **Ai chạy notebook nào.** Mục XIV chia việc theo ngày và theo số người, nhưng chưa gán tên. Ngày đầu Tuần 1 phải thay `[GIẢ ĐỊNH: 5 thành viên, 3 người rà được dữ liệu]` bằng số thật, và gán tên vào cột "Ai".
2. **Máy nào chạy `01_scan`.** Mốc 12–18 phút với 4 tiến trình giả định `[GIẢ ĐỊNH: CPU 4 nhân, 16 GB RAM, 20 GB đĩa trống]` và ổ SSD. Trên ổ cứng cơ, con số này khác hẳn — phải đo lại và ghi vào `build_info`, không coi là hằng số phần cứng.
3. **Điều gì xảy ra nếu `05` phình quá dư địa.** Chiến lược cắt giảm đã có thứ tự (hạ cleanlab 400→200 → rà 100% cụm liên lớp chạm K20-VN, ngoài K20-VN thì quarantine toàn cụm → bỏ hàng đợi outlier), nhưng tài liệu mẹ nhấn mạnh: **hai bước đầu phải được nhóm thống nhất từ Tuần 2**, không đợi tới lúc vỡ ngân sách mới bàn.
4. **Phiên bản thư viện.** Mỗi notebook in phiên bản ở ô 2, nhưng chưa có file khóa phiên bản. `requirements.txt` hiện có cần được ghim phiên bản trước lượt REP-02 đầu tiên, nếu không "cùng commit, cùng config" vẫn có thể cho hai kết quả.

---

*Hết. Mọi thay đổi của sổ tay này phải kèm một dòng lý do và ngày, đặt ngay dưới tiêu đề — cùng quy tắc với `CHANGELOG_dataset.md`.*
