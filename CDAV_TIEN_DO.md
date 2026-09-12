# CDAV — Báo cáo tiến độ, kết quả hiện tại và hướng đi

> Cập nhật: 13/09/2026. Phạm vi: toàn bộ các lượt CDAV trong `output/cdav/`.
> Mọi "đúng/sai" dưới đây so với **nhãn tham chiếu của bộ dữ liệu**, trừ khi ghi rõ khác.
> Phương pháp chi tiết: [CDAV.md](CDAV.md). Hướng dẫn chạy từng lượt:
> [CDAV_VALIDATION_V2.md](CDAV_VALIDATION_V2.md), [CDAV_VALIDATION_V3.md](CDAV_VALIDATION_V3.md),
> [CDAV_V3_LIVE.md](CDAV_V3_LIVE.md).

---

## 1. Tóm tắt

- **Hạ tầng đã xong và đáng tin.** Module CDAV bật/tắt được; khi tắt, graph giữ nguyên hành vi cũ.
  Mọi lượt thí nghiệm đều có checksum, chia dữ liệu theo nhóm và chặn rò rỉ. Đã kiểm tra lại
  độc lập kết quả v3: mọi con số tái lập đúng. 789 test đạt.
- **Chưa đạt mục tiêu vận hành.** Chưa có cấu hình nào vừa đạt precision khoảng 90% vừa tự nhận
  đủ nhiều ảnh. Trên ảnh khó (tập validation/test mới), các cấu hình an toàn chỉ tự nhận
  0–21% số ảnh. Artifact v3 hiện tại **không thể tự nhận ảnh nào ở τ = 0,90**.
- **Nút thắt là tín hiệu đầu vào, không phải nhãn.** Người dùng đã duyệt 136 ảnh khó và xác
  nhận cả 136 nhãn tham chiếu đều đúng. Vấn đề còn lại: checker yếu (đúng 38–42% trên ảnh khó),
  và nhãn đúng nằm ngoài cặp (a, b) ở 35–37% số ảnh.
- **Về giả thuyết:** H1 được ủng hộ. H2 chỉ đúng khi danh sách ứng viên sai. H3 chưa được chứng minh.

---

## 2. Tiến độ theo giai đoạn

| # | Giai đoạn | Ngày | Dữ liệu | Trạng thái | Thư mục |
|---|---|---|---|---|---|
| 0 | Triển khai module CDAV (16 module, graph, CLI, test) | 11/9 | — | Xong | `label_verifier/cdav/` |
| 1 | Phase A gốc: đo can thiệp K0/KR/KS/KW, có classifier | 11/9 | 200 ảnh đại diện (100 fit / 49 cal / 51 test) | Xong | `output/cdav/profile` |
| 2 | Kiểm tra lại: replay graph thật, loại bỏ classifier | 12/9 | Cùng 200 ảnh | Xong | `output/cdav/validation_steps_1_2` |
| 3 | Open-pair guard (xác suất nhãn thuộc cặp + hiệu chuẩn correctness) | 12/9 | Fit từ 200 ảnh cũ; test mới 102 ảnh | Xong | `output/cdav/open_pair_v1` |
| 4 | Validation v2: học từ cùng nguồn với dữ liệu khó | 12/9 | 306 ảnh val (102 fit / 51 cal / 51 rel / 102 audit) | Xong | `output/cdav/validation_v2` |
| 5 | Validation v3: kiểm định chéo lồng nhau, chọn cấu hình | 12–13/9 | 204 ảnh phát triển | Xong | `output/cdav/validation_v3` |
| 6 | Chạy graph thật với v3, có Checker 3 | 13/9 | 102 ảnh val mới | **Dừng ở 27/102** | `output/cdav/v3_live_v1` |
| 7 | Duyệt nhãn 136 ảnh, thay key C3, sửa lỗi resume | 13/9 | 136 ảnh phát triển | Xong | `validation_v2/human_review_decisions.json` |

---

## 3. Kết quả chính

### 3.1 Phụ thuộc do candidate (H1, H2) — 200 ảnh đại diện

Can thiệp có kiểm soát trên cùng ảnh (`output/cdav/profile/report.txt`):

| Chỉ số | Checker 1 | Checker 2 |
|---|---:|---:|
| Candidate Susceptibility (KR so với K0) | 33,0% | 27,5% |
| Lật sang đúng (candidate giúp) | 20,0% | 16,0% |
| Lật sang sai (candidate hại) | 5,0% | 4,5% |
| Số ảnh đúng: K0 → KR | 107 → 137 | 111 → 134 |

| Điều kiện so với K0 | Agreement Inflation | Wrong Agreement Inflation |
|---|---:|---:|
| KR (danh sách truy hồi thật) | +9,5 điểm | **−4,5 điểm** |
| KS (đảo thứ tự) | +2,0 điểm | −3,5 điểm |
| KW (danh sách sai, có lớp dễ nhầm đứng đầu) | −6,5 điểm | **+15,5 điểm** |

**Diễn giải:** danh sách ứng viên thật làm tăng đồng thuận mà **không** tăng đồng thuận sai, tức
là candidate đang giúp. Nhưng khi danh sách sai, cả hai checker cùng bị neo về lớp sai
(WAI +15,5). Hai lưu ý: đây là ảnh đại diện (ca dễ), và lượt đo này có classifier bị nghi
rò rỉ huấn luyện. Chưa đo lại H1/H2 trên ảnh khó.

### 3.2 Lợi ích ban đầu phụ thuộc classifier — 51 ảnh test cũ

| Cấu hình (graph thật, τ = 0,90) | Nhận | Đúng | Sai |
|---|---:|---:|---:|
| Luật compare gốc | 37 | 31 | 6 |
| CDAV **có** classifier | 41 | 41 | 0 |
| CDAV **không** classifier (cả 4 biến thể) | 40 | 34 | 6 |

Bỏ classifier thì CDAV không chặn được lỗi nào trong 6 lỗi của compare. Covariance cải thiện xếp
hạng (AURC 0,0574 so với 0,0641 của fusion độc lập) nhưng không đổi quyết định nào ở ngưỡng
0,90. Hiệu chỉnh susceptibility chỉ nhân một hằng số, nên không tạo khác biệt.

### 3.3 Open-pair guard trên 102 ảnh test mới (khó hơn)

| Cấu hình (full, τ = 0,90) | Nhận | Đúng | Sai | Coverage | Sang C3 |
|---|---:|---:|---:|---:|---:|
| Luật compare | 64 | 40 | 24 | 62,7% | 25 |
| Chỉ mô hình cặp | 82 | 44 | 38 | 80,4% | 7 |
| Guard nhãn thuộc cặp | 21 | 18 | 3 | 20,6% | 68 |
| Guard + hiệu chuẩn correctness | 8 | 7 | 1 | 7,8% | 81 |

Guard giảm được lỗi, nhưng chủ yếu bằng cách từ chối nhiều hơn. Nhãn tham chiếu nằm ngoài cặp ở
**38/102 ảnh (37,3%)**, trong khi con số này trên tập đại diện chỉ là 15,7%. Tập đại diện cũ
không phản ánh độ khó thật.

### 3.4 Validation v2 — 102 ảnh audit

| Cấu hình (full, τ = 0,90) | Nhận | Đúng | Sai | Precision |
|---|---:|---:|---:|---:|
| Luật compare | 49 | 35 | 14 | 71,4% |
| Profile cũ, guard nhãn thuộc cặp | 15 | 13 | 2 | 86,7% |
| Profile cũ, guard + hiệu chuẩn | 5 | 4 | 1 | 80,0% |
| **Profile v2 (mọi biến thể)** | **0** | 0 | 0 | — |

Profile v2 được xác suất trung thực hơn (Brier 0,4083 → 0,2551) nhưng không vượt được 0,90.

### 3.5 Validation v3 — kiểm định chéo lồng nhau trên 204 ảnh phát triển

| Phương pháp (dự đoán ngoài fold) | Nhận | Đúng | Sai | Coverage | Sang C3 |
|---|---:|---:|---:|---:|---:|
| Luật compare | 112 | 67 | 45 | 54,9% | 60 |
| v2 học lại trong fold | 2 | 1 | 1 | 1,0% | 170 |
| Quy trình v3 | 3 | 2 | 1 | 1,5% | 169 |

- Brier v3 − v2 = −0,0005, khoảng bootstrap 95% [−0,013; 0,013], đi qua 0: **không cải thiện**.
- Việc chọn cấu hình không ổn định: các fold chọn oas/temperature 3 lần, diagonal/logistic 1 lần,
  diagonal/temperature 1 lần. Brier giữa các cấu hình chỉ lệch khoảng 0,005.
- **Artifact cuối (diagonal/temperature, T = 3,548, fit từ 30 ảnh)** chặn confidence ở xác suất
  trong cặp. Giá trị cao nhất trên chính dữ liệu học chỉ 0,894 (sau guard: 0,864), nên **0%
  tự nhận ở τ = 0,90**. Con số 1,5% ở bảng trên là của các model trong từng fold, không phải
  artifact đã giao.

### 3.6 Chạy thật với v3 — 27/102 ảnh (dừng giữa chừng)

- CDAV đẩy **cả 23/23** ảnh đi qua nó sang Checker 3, kể cả 17 ca hai checker đã đồng thuận.
  Confidence cao nhất 0,833.
- Trên 13 ca đồng thuận mà C3 trả lời được, C3 chỉ đổi 1 nhãn (vẫn sai). Số đúng giữ nguyên
  9/13 dù có C3. Tức là tốn thêm 13 lượt gọi C3 mà độ đúng không đổi.
- Dừng vì key C3 hỏng. 4 ảnh (vị trí 23, 25, 26, 27) bị ghi UNRESOLVED do lỗi API; 3 trong 4 ảnh
  có nhãn đồng thuận đúng.
- Kết quả một phần, mẫu rất nhỏ. Chỉ để mô tả, không dùng để chỉnh cấu hình.

### 3.7 Duyệt nhãn 136 ảnh khó

Người dùng (thanhhuyjqk) đã duyệt cả 136 ảnh trong hàng đợi (68 fit, 35 calibration,
33 reliability) và xác nhận **toàn bộ nhãn tham chiếu đúng**. Đã import, không nhãn nào đổi.

Hệ quả: (1) các lỗi đo được trên phần phát triển là lỗi thật của hệ thống, không phải do nhãn
sai; (2) học lại v4 với các nhãn này sẽ cho kết quả y hệt v3, nên **không học lại**.

---

## 4. Tình trạng ba giả thuyết

| Giả thuyết | Kết luận hiện tại | Bằng chứng | Còn thiếu |
|---|---|---|---|
| **H1** — candidate chung làm đổi dự đoán của checker | **Ủng hộ** | CS 27–33%, khoảng Wilson tách xa 0 | Chưa đo trên ảnh khó |
| **H2** — candidate chung làm tăng đồng thuận sai | **Không ủng hộ với danh sách thật**; ủng hộ khi danh sách sai | WAI −4,5 (KR) và +15,5 (KW) | Chưa đo trên ảnh khó (37% nhãn ngoài cặp, tức danh sách hay sai) |
| **H3** — hiệu chỉnh + covariance giảm false accept tốt hơn bỏ phiếu | **Chưa chứng minh** | Không classifier: không chặn thêm lỗi nào ở 0,90. Guard giảm lỗi nhưng chỉ bằng cách hạ coverage xuống 8–21% | Cấu hình đạt coverage hợp lý trên tập độc lập |

---

## 5. Nguyên nhân gốc

1. **Tín hiệu yếu so với mục tiêu 0,90.** Trên 204 ảnh phát triển: hai checker đồng thuận chỉ
   đúng 60%; Checker 1 đúng 78/204, Checker 2 đúng 85/204; k-NN top-1 đúng 99/204. Nhãn duyệt đã
   loại trừ khả năng các con số này thấp do nhãn sai.
2. **Mất giả thuyết đúng trước bước hợp nhất.** Nhãn đúng nằm ngoài cặp (a, b) ở 35–37% số ảnh
   khó. Mọi mô hình theo cặp đều bị trần cứng ở đây.
3. **Dữ liệu hiệu chuẩn quá ít.** Mỗi calibrator chỉ có 30–41 ảnh, nên temperature học rất lớn
   (T = 3,5), nén xác suất và làm 0,90 không thể chạm tới.
4. **Classifier đang bị bỏ ra** vì nghi rò rỉ huấn luyện. Đây là tín hiệu mạnh nhất trong lượt
   đầu nhưng chưa có bản ngoài fold (out-of-fold) để dùng an toàn.

---

## 6. Vấn đề đã phát hiện

### Đã sửa

| Vấn đề | Tác động | Sửa |
|---|---|---|
| Hướng cặp: lật mô hình toàn cục theo class_id | Đảo dấu bằng chứng ở một nửa số cặp | `covariance.for_pair` chỉ lật mô hình theo cặp |
| `PairModel.flipped()` chỉ đổi chỗ trung bình, không đổi dấu | Như trên | Đổi dấu cả hai trung bình |
| KW loại lớp dễ nhầm đã có trong danh sách | Can thiệp KW mất tác dụng | Ưu tiên lớp dễ nhầm lên đầu |
| Quy ước hash pixel khác nhau (CDAV và metadata) | Kiểm tra trùng giữa các partition sai | Dùng đúng quy ước của dataset |
| Live run tự nạp label notes mặc định | Metadata lọt vào request C3 | `LabelNotesRegistry([])` |
| Resume live run giữ bản ghi lỗi hạ tầng | Lỗi API bị tính là UNRESOLVED thật, không bao giờ chạy lại | Tách sang `infra_failed_attempts.jsonl` rồi chạy lại; report đếm riêng `infra_failed` |
| Phép so checksum v2 trong live report | File duyệt nhãn cập nhật làm report thất bại | Bỏ qua `review_template.csv` và `human_review_decisions.json` |
| Key Checker 3 bị vô hiệu (HTTP 401) | Live run dừng | Đã thay key, gọi thử được |

### Còn mở

- Artifact v3 không đạt τ = 0,90 (xem 3.5).
- `v3_live_v1` còn 75 ảnh mới và 4 ảnh cần chạy lại.
- `validation_v3/uncertainty.json` không có script sinh ra trong repo (đã tính lại tay và khớp).
- Evaluator offline chưa có kết quả Checker 3, nên các bảng "Sang C3" chưa phải kết quả cuối.
- Phần lớn mã CDAV, script và tài liệu **chưa commit** (`git status` còn nhiều file mới).

---

## 7. Nên làm gì tiếp

### Ngay bây giờ

1. **Quyết định về live run.** Chạy tiếp tốn tối đa khoảng 229 lượt gọi API, nhưng với artifact
   v3 thì kết quả chủ yếu đo Checker 3, không đo được CDAV. Chỉ nên chạy nếu cần số liệu nền của
   C3 (C3 sửa được bao nhiêu ca đồng thuận sai); nếu không, tạm dừng.
   ```bash
   python workflows/label_verifier/scripts/evaluate_v3_live.py run --workers 3
   ```
2. **Commit toàn bộ công việc** (module `cdav/`, `scripts/`, tests, docs), trừ `.env.local` và
   `output/` lớn. Nếu mất cây làm việc bây giờ thì mất cả ba ngày thí nghiệm.
3. **Không học lại v4** chỉ vì đã có nhãn duyệt (nhãn không đổi).

### Ngắn hạn — gỡ nút thắt tín hiệu (xếp theo giá trị)

4. **Giảm tỷ lệ nhãn ngoài cặp.** Thay cặp nhị phân (a, b) bằng hợp nhất trên top-3 đến top-5
   ứng viên, hoặc thêm lớp k-NN top-2/top-3 khi guard báo xác suất thuộc cặp thấp. Mục tiêu đo
   được: đưa tỷ lệ nhãn đúng trong tập giả thuyết từ khoảng 65% lên trên 85% trên phần phát
   triển. Đây là trần cứng lớn nhất hiện nay.
5. **Đưa classifier trở lại một cách an toàn.** Làm cross-fitting (N4 trong
   `NGHIEN_CUU_CHUYEN_SAU.md`): mỗi ảnh chỉ nhận xác suất từ model chưa từng thấy nó. Lượt đầu cho
   thấy đây là tín hiệu mạnh nhất (41/41 khi có classifier).
6. **Checker mạnh hơn cho C1/C2.** `gemini-3.5-flash-lite` và `3.1-flash-lite` chỉ đúng
   38–42% trên ảnh khó. Thử một model mạnh hơn trên cùng 204 ảnh phát triển; bằng chứng đã cache
   nên chỉ tốn lượt gọi mới.

### Trung hạn — làm cho hiệu chuẩn có ý nghĩa

7. **Tăng dữ liệu hiệu chuẩn:** 3–5 ảnh mỗi lớp cho calibration/reliability thay vì 30–41 ảnh
   tổng, để temperature không bị kéo quá lớn.
8. **Chốt chính sách ngưỡng trước khi đánh giá:** chọn τ trên CV phát triển theo mục tiêu
   rõ ràng (ví dụ cận dưới Wilson của precision ≥ 0,90 tại coverage lớn nhất), ghi vào protocol,
   **rồi** mới chạy trên tập mới. Không chỉnh τ theo audit, test cũ hay kết quả live.
9. **Đo lại H1/H2 trên ảnh khó** với K0/KR/KI trên 204 ảnh phát triển. Trên ảnh khó, danh sách
   ứng viên sai thường xuyên hơn, nên hiệu ứng KW (WAI +15,5) có thể xuất hiện ở điều kiện thật.

### Dài hạn — xác nhận

10. Sau khi thiết kế đã đóng băng: một tập kiểm chứng **mới, chưa xem**, có nhãn duyệt, chạy graph
    đầy đủ gồm Checker 3, so hai pipeline có/không CDAV cùng lúc.
11. Chuẩn hoá tái lập: mọi số trong báo cáo phải có script sinh ra (kể cả bootstrap), và chạy toàn
    bộ test trước mỗi lượt đóng băng.

---

## 8. Quy tắc cần giữ

- **Không sửa bất kỳ file nào trong `output/cdav/validation_v3/`.** Live run băm cả thư mục;
  sửa sẽ chặn resume.
- Audit v2, 102 ảnh test của `open_pair_v1` và 102 ảnh `v3_live_v1` chỉ dùng để **đánh giá**:
  không fit, không chọn cấu hình, không chọn ngưỡng.
- Giữ ranh giới fit / calibration / reliability và chia theo nhóm bản sao gần.
- Không đọc confidence 0,90 như cam kết precision 90% nếu chưa có khoảng Wilson trên tập độc lập.
- Lỗi hạ tầng không phải kết luận về ảnh: luôn đếm riêng.

---

## 9. Chỉ mục file kết quả

| Lượt | File đọc trước |
|---|---|
| Phase A gốc | `output/cdav/profile/report.txt` |
| Kiểm tra lại, bỏ classifier | `output/cdav/validation_steps_1_2/ANALYSIS.md` |
| Open-pair | `output/cdav/open_pair_v1/INDEPENDENT_REPORT.md` |
| Validation v2 | `output/cdav/validation_v2/ANALYSIS.md`, `REPORT.md` |
| Validation v3 | `output/cdav/validation_v3/ANALYSIS.md`, `REPORT.md`, `nested_cv.json` |
| Live v3 | `output/cdav/v3_live_v1/EXECUTION_NOTES.md`, `live_records.jsonl` |
| Duyệt nhãn | `output/cdav/validation_v2/human_review_decisions.json` |
