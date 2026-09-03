# PestID — Runbook vận hành tuyến `quy_trinh_xu_ly_data`

Checklist đưa 19 notebook trong [`notebooks/quy_trinh_xu_ly_data/`](../../notebooks/quy_trinh_xu_ly_data)
từ trạng thái hiện tại đến `training_ready = true`.

Mỗi bước ghi rõ **ai làm** — máy hay người. Hai việc tốn nhiều công người nhất
(600 phiếu calibration ở bước 2, rà nhãn 5 hàng đợi ở bước 8) là đường găng thật sự;
phần máy chạy có thể làm song song với chúng.

Cập nhật: 03/09/2026 · nhánh `fix/tach-tuyen-quy-trinh-xu-ly-data`

---

## Nguyên tắc: một notebook, một trạng thái

Trước đây bốn việc được thực hiện bằng cách **chạy lại notebook lần hai với trạng thái
khác** qua một cờ: `04c` (`LOCK_CONFIG`, rồi `GENERATE_LEAKAGE_FLAGS`), `05` (`finalize`),
`10` (`finalize_config`). Ba vấn đề:

1. Để **ký một quyết định**, phải chạy lại toàn bộ phần tính toán đắt tiền của pass 1.
2. Một cờ bật nhầm ghi đè lên thứ đã ký, hoặc chạm vào original test trước hạn.
3. Với `05`, pass 2 **dựng lại hàng đợi từ đầu** rồi mới đối chiếu với phiếu người — nếu
   thượng nguồn đổi giữa chừng, hàng đợi tái sinh khác hàng đợi người đã cầm đi chấm.

Nay mỗi pass-2 là một notebook riêng, **không tính lại gì**: chỉ đọc artifact của pass 1
và đối chiếu hash với run log của nó trước khi hành động.

| Pass 1 (tính) | Pass 2 (ký / quyết định) |
|---|---|
| `04c_nd_cluster` | `04d_lock_cleaning_config` · `04e_leakage_flags` |
| `05_label_audit` | `05b_finalize_label_audit` |
| `10_crop_study` | `10b_lock_preprocess_config` |

`tests/test_pipeline_isolation.py` giữ ràng buộc: cờ pass-2 không được quay lại notebook
gốc, và mỗi cặp (ghi run log ↔ đọc run log) là một hợp đồng được kiểm.

`06` và `07` **giữ nguyên cờ `publish`**: pass 2 của chúng bắt buộc phải dựng lại toàn bộ
artifact để publish, nên tách file chỉ tạo ra một bản sao gần-giống — đúng thứ rủi ro đang
muốn tránh. Cả hai đã từ chối ghi đè phiên bản đã tồn tại.

---

## Trạng thái đang ở đâu

- `data/interim/` đã xoá để chạy lại từ đầu. Phiếu chấm vòng calibration cũ lưu ở
  [`docs/data/archive/calib_v0_20260903/`](../data/archive/calib_v0_20260903).
- `configs/cleaning_config.yaml`, `configs/preprocess_v1.yaml`: **chưa có**, sinh ở bước 4 và 11.
- `configs/split_config.yaml`: `status: draft` → `07` chưa publish được.
- `docs/data/evidence/`: đã có `PAD_ranking_locked.csv`. Còn thiếu `preprocess_parity.json`
  và `REP03_cross_machine.json`; `PAD_agreement.json` xem bước 13.
- Tuyến gọn v1 (`run_all.py`, `configs/pipeline_config.yaml`) **đã xoá** — nó chính là thứ
  từng ghi đè `C_duplicate_clusters.csv` của tuyến này.

---

## Đường găng

### 1 · `01 → 02 → 03 → 04a → 04b`

*Máy. Không chặn bởi gì.*

- [ ] `01_scan` với `force_rescan=True` — quét 75.222 ảnh, dựng lại `cache256_scan`
- [ ] `02_exact_embed` · `03_exact_duplicates` · `04a_embed`
- [ ] `04b_nd_candidates` — sinh ứng viên **và phát 600 phiếu mù**

> `04b` lần trước mất ~74 phút (log: 11:13:01Z → 12:27Z). Chưa có số cho lần quét lạnh của `01`.

Kiểm sau bước này: `D_calib_sample__r1.csv` và `__r2.csv` tồn tại và **khác thứ tự dòng**.

### 2 · 600 phiếu calibration — hai người chấm mù, độc lập

*Người. Chặn toàn bộ phần sau.*

- [ ] Người A → `D_calib_sample__r1_labeled.csv`
- [ ] Người B → `D_calib_sample__r2_labeled.csv`
- [ ] Không trao đổi đáp án, **giữ nguyên thứ tự dòng**

[`docs/data/HuongDan_ChamMu_Calibration.md`](../data/HuongDan_ChamMu_Calibration.md).
`04c` từ chối nếu thứ tự sai — đó là cách phát hiện phiếu bị copy.
Gate còn `NEEDS_MORE_LABELS` thì lặp vòng `+50` tới trần `CALIB_N_MAX = 1200`.

### 3 · `04c_nd_cluster` — tính và báo cáo

*Máy. Không ký gì.*

- [ ] PASS Calibration Gate (kappa ≥ 0,70, precision ≥ 0,90, recall ≥ 0,98)
- [ ] Bảng `independence_report`: mọi dòng `verified = True`
- [ ] Có cảnh báo `perfect_agreement` thì **xem lại trước khi sang bước 4**

### 4 · `04d_lock_cleaning_config` — ký ngưỡng

*Máy chạy, người ký. Không tính lại gì.*

- [ ] Đặt `ACCEPTED_BY="<tên nhóm trưởng>"` rồi chạy

Notebook từ chối nếu gate chưa PASS, scorer trên đĩa không phải scorer đã sinh ra
metadata, hoặc artifact `04c` đã đổi sau lần chạy cuối.

### 5 · Commit `cleaning_config.yaml`

*Người.*

```bash
git add configs/cleaning_config.yaml docs/data/D_threshold_calibration.md
git commit -m "chore: khoa nguong near-duplicate sau calibration gate"
```

Hàng rào P-03 kiểm file này **byte-identical với HEAD** trước khi bất kỳ code nào đọc
`orig_split == "test"`. Chưa commit thì bước 6, 7 và 15 đều chặn.

### 6 · `04e_leakage_flags` — sinh cờ rò rỉ

*Máy. Chặn bởi bước 5.*

- [ ] Chạy → `E_orig_leakage_flags.parquet`

> Đây là **lần đầu tiên** cả tuyến được phép đọc `orig_split == "test"`; hàng rào P-03 là
> cell đầu tiên của notebook. `07` yêu cầu file này, nên thứ tự 5 → 6 là bắt buộc.

### 7 · `05_label_audit` — phát hàng đợi

*Máy. Chặn bởi bước 5.*

- [ ] Chạy → `F_label_review_queue.csv` + `F_blur_sampling_key.csv`
      (5 hàng đợi: `exact_cross_class`, `nd_cross_class`, `cleanlab` 400, `outlier` 150,
      `blur` 100/tầng)

### 8 · Rà nhãn — hai phiếu độc lập

*Người. Khối lượng lớn nhất của cả tuyến.*

- [ ] Hai người chấm độc lập → `F_label_review_results.csv`
- [ ] Rà taxonomy → `F_taxonomy_review.csv`

### 9 · `05b_finalize_label_audit` — chốt phán quyết

*Máy chạy, người ký.*

- [ ] Đặt `accepted_by="<tên>"` rồi chạy

Notebook đối chiếu hash `F_label_review_queue.csv` với run log của `05`: nếu hàng đợi trên
đĩa khác bản đã phát cho người chấm, nó dừng thay vì đối chiếu lệch âm thầm.
Bất đồng hoặc `unsure` → `uncertain`; **không có người thứ ba phá hoà**.
`label_audit.csv` append-only — sửa bằng action mới, không sửa lịch sử.

### 10 · `10_crop_study`

*Máy. Chạy được ngay sau bước 1.*

- [ ] Đo mất đối tượng do `resize 256 → center crop 224`

### 11 · `10b_lock_preprocess_config` + commit

*Máy chạy, người ký.*

- [ ] Nhóm chấp nhận kết luận → chạy với `accepted_by="<tên>"`
- [ ] `git commit configs/preprocess_v1.yaml`

### 12 · `PAD_ranking_locked.csv` — ĐÃ XONG

- [x] 102/102 lớp, `pad_rank` liền mạch 1–102, ở `docs/data/evidence/`

`06` đọc **thẳng** `pad_rank` làm `rank_vn`, không tính lại — nên chuỗi phá hoà
(`score_total → score_D → n_clean → ip102_id`) không chạy và thứ hạng không thể trượt khỏi
bảng đã ký. Quan trọng vì `n_clean_in_ranking` lệch `n_clean` thực tế ở **cả 102/102 lớp**.

**Hệ quả phải ký:** bảng chỉ có điểm tổng, không có điểm từng trục → MAJ-09 không thể có
Krippendorff alpha theo trục → **HC-09 không thoả**. Khai thành `accepted_risks` có chữ ký
trong `split_config.yaml` (bước 14) và nêu trong datasheet.

### 13 · Hai file bằng chứng còn lại

*Người. Không notebook nào sinh ra — thiếu là `08` fail, không có đường vòng.*

- [ ] `docs/data/evidence/preprocess_parity.json` — tiền xử lý lúc train khớp bit-đối-bit
      với cache 256 đã phát hành · MAJ-08
- [ ] `docs/data/evidence/REP03_cross_machine.json` — dựng lại trên máy thứ hai ra đúng
      `manifest_sha256` · REP-03
- [ ] `PAD_agreement.json` — với bảng một nguồn hiện tại **không tạo được**; MAJ-09 đi qua
      `accepted_risks`. Chỉ tạo được nếu nhóm chấm kép lại 102 lớp × 3 trục (~5,1 h người)
- [ ] Commit

[`docs/data/evidence/README.md`](../data/evidence/README.md).

### 14 · `06_build_clean_pool` và khoá `split_config.yaml`

*Máy, rồi người ký. Chặn bởi 6, 9, 12.*

- [ ] `06` với `publish=False`, xử lý hết `H_followup_review_queue.csv` và
      `H_pass2_oversized_queue.csv` (quay lại bước 8 nếu còn ảnh cần rà)
- [ ] `06` với `publish=True`, `accepted_by`, `change_reason` → `data/processed/clean_v1.0/`
- [ ] `split_config.yaml`: `status: locked`, `accepted_by`, `accepted_risks` cho mọi Major
      dự kiến FAIL (đủ `test_id`/`reason`/`accepted_by`/`accepted_at`), rồi commit

Phiên bản đã tồn tại **không bao giờ bị ghi đè**; muốn dựng lại phải đổi `version`.

### 15 · `07_build_split` — ba run

*Máy. Chặn bởi 6, 11, 14.*

- [ ] `publish=False` → xem `data/interim/07_preview/`, không còn Critical FAIL
- [ ] `publish=True`, `out="runs/A"`
- [ ] `publish=True`, `out="runs/B"` → **REP-01**: `manifest_sha256` phải trùng `runs/A`
- [ ] Dựng lại từ đầu vào đã khoá, `out="runs/REP02"` → **REP-02**

### 16 · `08_validate_dataset` — cổng chất lượng

*Máy. Chặn bởi 13, 15.*

- [ ] `strict=True` → `training_ready = true`

40 phép kiểm: CRIT-01…16 + CRIT-09b, REP-01/02, 13 MAJ, 8 WARN. Critical **không thể** bỏ
qua. Major FAIL chỉ được chấp nhận khi có đủ chữ ký trong `build_info.json`.

### 17 · Báo cáo, tập con, gói Kaggle

*Máy. Chặn bởi 16.*

- [ ] `09_eda` — 20 biểu đồ bốn tầng
- [ ] `11_leakage_report` — B-L1..L4, H-L1..L6
- [ ] `12_build_subset` — tập con theo lớp (`first_n` / `class_ids` / `class_names` / `existing_set`)
- [ ] `13_export_kaggle` — gói ImageFolder + `.zip` có checksum

---

## Việc chạy song song được ngay

1. **Xếp lịch hai người** cho 600 phiếu calibration (bước 2) và cho rà nhãn (bước 8)
2. **Dựng `preprocess_parity.json`** (bước 13) — cần người làm pipeline train
3. **Quyết định về HC-09**: chấp nhận rủi ro có chữ ký (rẻ, giữ bảng đã chốt) hay chấm kép
   lại 102 lớp × 3 trục (~5,1 h người, và thứ hạng có thể đổi)

## Quy tắc chung

- **Mọi notebook chạy Restart & Run All.** `clean_run` trong run log ghi lại điều đó; một
  lượt chạy nhảy cóc ô không phải bằng chứng REP-02.
- **Run log ở `reports/quy_trinh_xu_ly_data/run_logs/`**, được git theo dõi.
- **Không dùng chung thư mục ghi với `run_lean.py`.** `tests/test_pipeline_isolation.py`
  giữ ràng buộc đó.
- Trước mỗi lần xoá `data/interim/`: **sao lưu phiếu chấm của người**
  (`*__r*_labeled.csv`, `F_label_review_results.csv`, `F_taxonomy_review.csv`,
  `D_oversized_edge_reviews.csv`, `H_pass2_oversized_reviews.csv`).

```bash
python -m pytest tests -q --ignore=tests/lean
```
