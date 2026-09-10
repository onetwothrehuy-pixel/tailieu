# Nghiên cứu chuyên sâu: cấu trúc nhiễu của IP102 và các cải tiến đề xuất

Tài liệu này khác [DE_XUAT_DO_CHINH_XAC.md](DE_XUAT_DO_CHINH_XAC.md): ở đó là những việc nên làm
dựa trên phương pháp đã có; ở đây là **phân tích riêng** trên dữ liệu của dự án, các dẫn xuất toán
học, và một số đề xuất tôi chưa thấy ai làm.

Quy ước nhãn cho từng khẳng định:

* **(đo)** — số đo thật, chạy trên dữ liệu của dự án, có thể chạy lại.
* **(dẫn xuất)** — suy ra bằng toán từ giả thiết được nêu rõ; đúng nếu giả thiết đúng.
* **(đề xuất)** — ý tưởng của tôi, **chưa kiểm chứng**, cần bạn thẩm định.
* **(tài liệu)** — kết quả đã công bố của người khác.

---

# Phần I — Bốn phát hiện mới về dữ liệu

Bốn kết quả dưới đây đo trên chỉ mục 22.589 ảnh và 200 ảnh đại diện. Cả bốn đều thay đổi cách nên
thiết kế hệ, nên tôi đặt trước mọi đề xuất.

## I.1 — 5 điểm trong "77%" là rò rỉ bản sao gần **(đo)**

`knn.py` loại ảnh trùng theo `sha256`. Đó là bộ lọc **bản sao chính xác**. Nó không bắt được ảnh
đã resize, nén lại, hay đổi định dạng — mà chỉ mục đầy những ảnh như vậy.

Đo trên 200 ảnh đại diện, loại thêm bản sao gần bằng dHash (khoảng cách Hamming ≤ 4):

| | Giữ bản sao gần | Loại bản sao gần | Chênh |
|---|---|---|---|
| recall@1 | 76% | **71%** | −5,0 điểm |
| recall@5 | 91% | 90% | −1,5 điểm |
| recall@10 | 94% | 94% | −0,5 điểm |

**200/200 ảnh đại diện có ít nhất một bản sao gần trong top-50 của chỉ mục.** Không phải một vài
ca hiếm — là toàn bộ.

Vì sao chỉ recall@1 bị ảnh hưởng: bản sao gần luôn chiếm đúng vị trí số 1. Từ k = 5 trở đi, các
láng giềng thật đã chen vào và con số trở nên trung thực.

**Hệ quả:** trần thật của nhóm đề cử ứng viên là **71% @1, 90% @5**, không phải 77%/89%. Mọi so
sánh "LLM vs k-NN" từ trước đến nay đang so với một chuẩn được nâng khống 5 điểm.

Điều trớ trêu: `visual_context.py` **đã có** bộ lọc dHash và dùng nó đúng chỗ. Chỉ có `knn.py` —
module tôi viết sau — là bỏ sót. Đây là lỗi cần sửa, không phải đề xuất.

## I.2 — Chỉ mục dư thừa 21% **(đo)**

21,1% số ảnh trong chỉ mục có láng giềng gần nhất là bản sao gần của chính nó, và **100% các cặp
đó mang cùng nhãn**.

Con số 100% quan trọng hơn con số 21%. Nó nói rằng nhãn đã được **sao chép sang bản sao**. Hệ quả:

* Một ảnh bị gán sai kéo theo mọi bản sao của nó cũng sai. Nhiễu **không độc lập** giữa các mẫu.
* Trong mọi cơ chế bỏ phiếu theo láng giềng, một cặp bản sao đóng góp hai phiếu nhưng chỉ mang một
  đơn vị thông tin. k-NN đang **tự tin quá mức** một cách có hệ thống.

Đây là vi phạm giả thiết nền của gần như mọi phương pháp học với nhãn nhiễu: chúng giả định nhiễu
độc lập theo mẫu, hoặc ít nhất chỉ phụ thuộc lớp.

## I.3 — Đồ thị nhầm lẫn thưa nhưng đuôi phẳng **(đo)**

Trên 4.000 ảnh lấy mẫu, leave-one-out 1-NN:

| | |
|---|---|
| Độ khớp 1-NN | 78,5% (71% sau khi loại bản sao gần) |
| Số ca 1-NN khác lớp | 861 / 4.000 |
| Số cặp lớp thực sự xuất hiện | **531 / 5.151** cặp có thể |
| Số cặp cần để phủ 50% số ca | 122 cặp |
| Top-10 cặp phủ | chỉ 12% |

Hai kết luận **trái chiều nhau** và cả hai đều đúng:

* Đồ thị nhầm lẫn **thưa** — chỉ 10% số cặp từng xảy ra. Nhầm lẫn có cấu trúc, không ngẫu nhiên.
* Nhưng **đuôi phẳng** — cần 122 quy tắc để phủ nửa số ca. Đây là lý do "sổ định nghĩa theo cặp"
  thất bại, và tôi giữ nguyên kết luận đó.

Điều mới: cấu trúc không nằm ở **cặp** mà ở **cụm**.

## I.4 — Không gian nhãn của IP102 **không phải một phân hoạch** **(đo)**

Gom đồ thị nhầm lẫn thành thành phần liên thông (ngưỡng cạnh ≥ 4 ca) cho ra các cụm sau — tôi chỉ
chạy thuật toán trên vector DINOv2, **không** dùng bất kỳ thông tin phân loại học nào:

```
{3 asiatic rice borer, 4 yellow rice borer, 14 grub, 16 wireworm,
 18 black cutworm, 19 large cutworm, 20 yellow cutworm, 22 corn borer,
 23 army worm, 26 peach borer, 38 cabbage army worm, 45 flax budworm,
 86 Prodenia litura}                                    ← 13 lớp, TẤT CẢ đều là ấu trùng

{24 aphids, 27 english grain aphid, 29 bird cherry-oataphid,
 52 therioaphis maculata, 89 Toxoptera citricidus, 91 Aphis citricola}   ← họ Aphididae

{7 brown plant hopper, 8 white backed plant hopper,
 9 small brown plant hopper, 69 Cicadella viridis, 101 Cicadellidae}     ← nhóm rầy

{46 alfalfa plant bug, 47 tarnished plant bug, 57 Apolygus lucorum,
 70 Miridae}                                                            ← họ Miridae

{83 Tetradacus c Bactrocera minax, 84 Dacus dorsalis, 85 Bactrocera tsuneonis}  ← ruồi đục quả

{49 lytta polita, 50 legume blister beetle, 51 blister beetle}
```

Các cụm này trùng khít với **họ/giống sinh học** hoặc **giai đoạn phát triển**. Thuật toán không hề
biết điều đó — nó chỉ nhìn embedding ảnh.

Bây giờ là phần quan trọng nhất. Nhìn cụm thứ tư:

> lớp **70 là "Miridae"** — tên một **họ**. Lớp 46, 47, 57 là các **loài trong chính họ đó**.

Tương tự: **101 "Cicadellidae"** chứa 11 và 69; **58 "Limacodidae"** là một họ; **24 "aphids"**,
**51 "blister beetle"**, **14 "grub"**, **16 "wireworm"**, **23 "army worm"** đều là tên bao trùm.

Với một ảnh *Apolygus lucorum*, cả nhãn **57** lẫn nhãn **70** đều **đúng**. Đây không phải lỗi
nhãn. Đây là **không gian nhãn không phải phân hoạch** — nó là một thứ tự bộ phận có quan hệ bao
hàm, và bộ dữ liệu đối xử với nó như thể là phân hoạch.

Định lượng phần "nhầm lẫn không thể sửa được":

| Nguyên nhân | Tỉ lệ trong tổng số ca 1-NN khác lớp |
|---|---|
| Cặp **lồng nhau** (12 cặp, ví dụ 47↔70, 50↔51, 69↔101) | **12%** |
| Cụm **ấu trùng hình sâu** (cùng hình thái, khác loài, khác cả cây trồng) | **12%** |
| **Cộng** | **24%** |

Chỉ **12 cặp** giải thích 12% toàn bộ nhầm lẫn — mật độ cao gấp mười lần so với top-10 cặp thường
(12% cho 10 cặp thì tương đương, nhưng 12 cặp lồng nhau là một **quy tắc có thể phát biểu**, còn
10 cặp kia thì không).

**Hệ quả trực tiếp:** không có mô hình nào đạt 100% trên IP102, và trần ấy không do năng lực mô
hình. Với những cặp lồng nhau, sự thật là một **tập**, không phải một điểm.

Một hệ quả nữa, phá một đề xuất trước của chính tôi: cụm ấu trùng 13 lớp trải trên **năm loại cây
trồng khác nhau** (lúa, ngô, cải, lanh, đào). Đề xuất "ràng buộc theo cây trồng" (D1 trong tài liệu
trước) sẽ **không giúp gì** cho cụm này, và nếu suy cây trồng từ nền ảnh thì còn làm hại — vì ấu
trùng thường được chụp cận cảnh, không có nền cây.

---

# Phần II — Dẫn xuất toán học

## II.1 — Nhiễu kho tham chiếu là trần cứng của truy hồi **(dẫn xuất)**

Ký hiệu: ảnh truy vấn `x` có lớp thật `y*`; truy hồi trả về láng giềng `z`.

* `r = P(lớp thật của z = y*)` — chất lượng truy hồi thuần túy;
* `η = P(nhãn quan sát của z ≠ lớp thật của z)` — tỉ lệ nhiễu của kho.

Giả thiết: khi nhãn của `z` sai, xác suất nó rơi trúng `y*` là không đáng kể.

Độ chính xác đo được (nhãn truy vấn tin cậy vì là ảnh đã duyệt):

```
A  =  P(nhãn quan sát của z = y*)  ≈  r · (1 − η)
```

Thay số đo được `A = 0,71`:

| Giả định η | r suy ra |
|---|---|
| 0 (kho sạch tuyệt đối) | 0,71 |
| 0,10 | 0,79 |
| 0,15 | 0,835 |
| 0,20 | 0,89 |

**Đạo hàm ∂A/∂η = −r ≈ −0,8.** Nghĩa là: **giảm nhiễu kho 10 điểm thì recall@1 tăng ~8 điểm.**

Đây là tỉ lệ đổi tốt nhất trong toàn hệ. Nó nói rằng làm sạch kho tham chiếu có giá trị cao hơn
mọi cải tiến encoder — vì encoder tốt hơn chỉ nâng `r`, còn kho bẩn thì nhân cả `r` với `(1−η)`.

Nó cũng nói rằng **hệ có thể tự bootstrap**: dùng hệ để làm sạch kho, kho sạch hơn làm hệ tốt hơn.
Xem II.4.

## II.2 — Đo độ chính xác **không cần nhãn đúng** **(dẫn xuất + tài liệu)**

Hệ có ba tín hiệu độc lập về bản chất: ensemble đã hiệu chuẩn, k-NN, LLM. Với **ba** bộ phân loại
mà lỗi **độc lập có điều kiện** trên lớp thật, có thể giải ra độ chính xác của từng bộ **mà không
cần một nhãn đúng nào**.

Trực giác qua trường hợp nhị phân. Gọi `e_i` là tỉ lệ lỗi của bộ `i`. Tỉ lệ đồng thuận giữa `i`
và `j`:

```
a_ij  =  (1 − e_i)(1 − e_j)  +  e_i · e_j        (cùng đúng, hoặc cùng sai ⇒ cùng đáp án)
      =  1 − e_i − e_j + 2 e_i e_j
```

Ba phương trình `a₁₂, a₁₃, a₂₃`, ba ẩn `e₁, e₂, e₃` → giải được. Với đa lớp, cách làm tương ứng là
mô hình **Dawid–Skene**: ước lượng đồng thời ma trận nhầm lẫn của từng "người chấm" và nhãn thật
bằng EM (Dawid & Skene 1979; phương pháp phổ có bảo đảm lý thuyết: Zhang, Chen, Zhou & Jordan 2014).

**Điều quan trọng hơn cả kết quả: đây là một phép kiểm giả thiết.** Nếu nghiệm cho ra `e_i` âm,
lớn hơn 1, hoặc ba phương trình không nhất quán, thì **giả thiết độc lập bị bác bỏ** — tức là các
tín hiệu đang sai giống nhau.

Ta đã biết một trường hợp như vậy: 40/40 lượt phân xử dùng cùng model với Checker 1. Phép kiểm này
sẽ tự động phát hiện ra tình huống đó, trên dữ liệu sản xuất không nhãn, mà không cần ai đi soi log.

**(đề xuất)** Đưa phép kiểm này thành một mục trong `report.py`: chạy trên mọi lô đã hoàn tất, in
ra độ chính xác ước lượng của từng checker và cờ cảnh báo khi hệ phương trình không nhất quán.

## II.3 — Cỡ mẫu cần cho mỗi lượt A/B **(dẫn xuất)**

Phép thử đúng cho A/B ghép cặp là McNemar. Chỉ các **cặp lệch** (một cấu hình đúng, cấu hình kia
sai) mang thông tin. Với `b` cặp nghiêng về B và `c` nghiêng về A, dưới `H₀` thì `b ~ Bin(b+c, 0,5)`.

Số cặp lệch cần để đạt lực 80% ở mức α = 0,05:

```
n_d  ≈  ( z_{α/2}·0,5  +  z_β·√(p(1−p)) )²  /  (p − 0,5)²
```

Tỉ lệ lệch đo được trong các lượt đã chạy là **17,5%** (7/40). Suy ra:

| Tỉ lệ thắng thật của B | Cặp lệch cần | **Số ảnh cần** |
|---|---|---|
| 80 : 20 | 19 | **110** |
| 75 : 25 | 29 | **165** |
| 70 : 30 | 47 | **266** |
| 65 : 35 | 85 | **484** |
| 60 : 40 | 194 | **1 106** |

Lượt A/B 40 ảnh đã chạy quan sát 8 : 2. Ngay cả nếu tỉ lệ thật đúng là 80 : 20, n = 40 vẫn chưa
đủ — cần 110. Đó là lý do p = 0,109 chứ không phải vì hiệu ứng không có thật.

**Khuyến nghị vận hành:** n = 165 là điểm cân bằng. Dưới 110 thì đừng chạy, vì kết quả không kết
luận được gì và vẫn tốn nguyên tiền API.

## II.4 — Làm sạch nhãn như bài toán năng lượng trên đồ thị **(đề xuất, dựa trên tài liệu)**

Đây là đề xuất trung tâm của tài liệu này.

Ta có hai nguồn thông tin về nhãn của mỗi ảnh trong kho:

* `P ∈ ℝ^{N×102}` — xác suất **đã hiệu chuẩn** của ensemble (ECE 0,015);
* `W` — ma trận kề của đồ thị k-NN trên embedding, `w_ij = exp(−(1 − cos_ij)/σ²)`.

Đặt `L = D − W` là Laplacian. Tìm nhãn mềm `Y` cực tiểu:

```
E(Y)  =  ‖Y − P‖²_F   +   λ · tr(Yᵀ L Y)
         └─ tin mô hình ─┘   └─ ảnh giống nhau thì nhãn giống nhau ─┘
```

Đây là hàm lồi ngặt, nghiệm đóng:

```
Y*  =  (I + λL)^{-1} P
```

Ma trận `(I + λL)` xác định dương và thưa, nên giải bằng gradient liên hợp: **O(số cạnh × số vòng
lặp)**, tức là vài giây cho 22.589 × 102. Không cần huấn luyện gì.

Tính chất: `λ → 0` cho lại ensemble thuần; `λ → ∞` cho lan truyền nhãn thuần (Zhu, Ghahramani &
Lafferty 2003). `λ` chọn bằng cross-validation trên tập ảnh đã duyệt.

**Ba sửa đổi tôi cho là cần thiết, xuất phát thẳng từ Phần I:**

1. **Xoá cạnh bản sao gần** (dHash ≤ 4) trước khi dựng `L`. Theo I.2, các cạnh này nối những ảnh
   luôn cùng nhãn — chúng mang trọng số đầy nhưng **không mang thông tin**. Giữ lại thì mô hình
   khuếch đại nhiễu tương quan và trở nên tự tin sai. Đây là điểm mà mọi bài lan truyền nhãn tiêu
   chuẩn bỏ qua, vì chúng giả định mẫu độc lập.

2. **Không lan truyền qua cạnh vượt cụm bao hàm.** Theo I.4, cạnh 47↔70 nối một loài với họ chứa
   nó. Lan truyền qua đó sẽ xoá mất sự phân biệt loài/họ. Xử lý riêng bằng II.5.

3. **Lặp và kiểm tra điểm bất động.** Đặt `T(P) = (I + λL)^{-1} P` rồi lặp. Vì `(I + λL)^{-1}` là
   ánh xạ co trên không gian nhãn mềm (mọi trị riêng nằm trong `(0, 1]`), phép lặp hội tụ. Câu hỏi
   nghiên cứu thật sự: **điểm bất động có gần nhãn sạch hơn điểm xuất phát không?** Theo II.1, mỗi
   vòng giảm `η` sẽ nâng `r` cho vòng sau. Tôi không biết điều kiện đủ để vòng lặp này không trôi
   về một nghiệm sụp đổ, và đó chính là chỗ đáng nghiên cứu.

## II.5 — Trừu tượng hoá thay vì bỏ qua **(đề xuất)**

Hiện tại, khi hệ không chắc, nó trả `UNRESOLVED` — ảnh bị loại khỏi tập dùng được, thông tin mất
sạch. Theo I.4, một phần đáng kể các ca "không chắc" có dạng rất đặc biệt: tập ứng viên là
`{57 Apolygus lucorum, 70 Miridae}`, tức **một loài và cái họ chứa nó**.

Với tập như vậy, câu trả lời đúng không phải "không biết". Nó là **70 Miridae** — cận trên nhỏ
nhất của tập trong quan hệ bao hàm. Câu trả lời ấy **chắc chắn đúng**, chỉ kém cụ thể hơn.

Hình thức hoá. Cho `⊑` là quan hệ bao hàm trên 102 lớp (đọc từ tên lớp, người xác nhận một lần).
Với tập dự đoán `S`:

```
nếu tồn tại cận trên nhỏ nhất ⊔S  trong không gian nhãn:
        trả  ⊔S,  đánh dấu  specificity = "family"
ngược lại:
        trả  UNRESOLVED
```

Kèm theo là hai chỉ số thay vì một:

| Chỉ số | Định nghĩa |
|---|---|
| **exact precision** | `ŷ = y` |
| **consistent precision** | `ŷ = y` hoặc `ŷ ⊒ y` (trả lời tổ tiên đúng) |

Theo I.4, 12% số ca nhầm sẽ chuyển từ "sai" sang "đúng nhưng kém cụ thể". Đó không phải làm đẹp số
liệu — đó là **báo cáo đúng cái mà hệ thật sự biết**. Một nhãn "Miridae" đúng có ích hơn nhiều so
với một ô trống, và có ích hơn một nhãn loài đoán bừa.

**Điều kiện để đây không phải là gian lận:** hai chỉ số phải luôn in cùng nhau, và trường
`specificity` phải nằm trong bản ghi để phía dùng dữ liệu tự quyết định có chấp nhận nhãn cấp họ
hay không.

## II.6 — Ngưỡng riêng theo lớp, suy từ hàm lợi ích **(dẫn xuất)**

macro-F1 = 0,55 so với accuracy = 0,71 nói rằng đuôi dài đang bị bỏ rơi. Với bài toán **làm sạch**,
đây không chỉ là vấn đề công bằng — nó là vấn đề hiệu quả.

Giá trị của việc sửa một nhãn ở lớp `c` tỉ lệ với `1/n_c`: lớp có 21 ảnh thì mỗi nhãn sai chiếm
1/21 của lớp đó, còn lớp có 400 ảnh thì chỉ 1/400. Lợi ích kỳ vọng của bộ làm sạch:

```
U  =  Σ_c  (số nhãn sửa đúng trong lớp c) / n_c
```

Cực đại `U` dưới ràng buộc precision toàn cục cho ra ngưỡng chấp nhận **khác nhau cho từng lớp**:
`τ_c` giảm khi `n_c` giảm — tức là **chấp nhận rủi ro cao hơn ở lớp hiếm**, vì phần thưởng ở đó
lớn hơn.

Điều này ngược với trực giác thông thường ("lớp hiếm khó hơn nên phải thận trọng hơn"), và nó là
hệ quả trực tiếp của việc mục tiêu là **làm sạch** chứ không phải **dự đoán**. Với conformal
prediction, biến thể *class-conditional* thực hiện đúng việc này với bảo đảm theo từng lớp.

Số đo hiện có (min 21 ảnh/lớp, trung vị 200, max 400) cho chênh lệch `1/n_c` gần **20 lần** giữa
lớp hiếm nhất và lớp đông nhất. Đây không phải hiệu chỉnh nhỏ.

---

# Phần III — Đề xuất mới

## N1 — Phân rã cơ chế nhiễu bằng bất tương hợp hình học–phân loại **(đề xuất)**

Phần I cho thấy nhiễu của IP102 có **ít nhất ba cơ chế khác nhau**, và chúng có chữ ký hình học
đối lập nhau:

| Cơ chế | Trong không gian đặc trưng | Trong cây phân loại | Cách xử lý đúng |
|---|---|---|---|
| **A. Nhầm loài gần** | **gần** | **gần** | encoder tốt hơn, ảnh đối chiếu |
| **B. Lồng nhau (loài ⊂ họ)** | **gần** | **bao hàm** | trừu tượng hoá (II.5) — không phải lỗi |
| **C. Đồng dạng ấu trùng** | **gần** | **xa** | không sửa được bằng ảnh; cần metadata giai đoạn |

Bộ phát hiện đề xuất — với ảnh `x`, nhãn quan sát `ỹ`, lớp trội trong láng giềng `ŷ_knn`:

```
d_feat  = 1 − độ tương tự cosin trung bình tới k láng giềng mang nhãn ỹ
d_taxon = khoảng cách trong cây phân loại giữa ỹ và ŷ_knn

B  ⟸  ỹ ⊑ ŷ_knn  hoặc  ŷ_knn ⊑ ỹ
C  ⟸  d_feat nhỏ  ∧  d_taxon lớn         ← chữ ký ngược, đây là dấu hiệu nhận biết
A  ⟸  d_feat nhỏ  ∧  d_taxon nhỏ  ∧  không bao hàm
```

Vì sao đây là đóng góp: mọi phương pháp phát hiện lỗi nhãn tôi biết (confident learning, AUM,
early-learning) trả về **một điểm số vô hướng** — "ảnh này đáng nghi bao nhiêu". Chúng không nói
**vì sao**. Nhưng ba cơ chế trên cần ba hành động hoàn toàn khác nhau, và gộp chúng vào một điểm
số là vứt đi đúng cái thông tin cần dùng.

Đây là đề xuất tôi tự tin nhất trong tài liệu, vì chữ ký hình học của cơ chế C — *gần trong ảnh, xa
trong phân loại* — đã hiện ra trong số đo I.4 mà không cần tôi đi tìm.

Điều kiện tiên quyết: bảng ánh xạ 102 lớp sang danh pháp khoa học và cây phân loại. Việc thủ công
một lần cho 102 dòng, dùng lại được cho mọi thứ khác (kể cả BioCLIP zero-shot).

## N2 — Bộ phân biệt cấp cụm thay cho quy tắc cấp cặp **(đề xuất)**

I.3 nói quy tắc theo cặp thất bại (cần 122 cặp cho 50%). I.4 nói cụm thì ít và có nghĩa (6 cụm phủ
phần lớn khối lượng nhầm lẫn tập trung).

Đề xuất kiến trúc hai tầng:

```
tầng 1:  phân loại 102 lớp như hiện nay  →  ra một cụm (hoặc "không thuộc cụm nào")
tầng 2:  nếu rơi vào cụm C, gọi một bộ phân biệt CHUYÊN cho cụm đó
```

Bộ phân biệt tầng 2 chỉ phải phân biệt 3–13 lớp thay vì 102, và có thể là:

* một linear probe huấn luyện riêng trên các lớp trong cụm — rẻ, vài phút;
* hoặc một lượt gọi LLM với **đúng** các ảnh đối chiếu của các lớp trong cụm — đây là bài toán mà
  mô hình ngôn ngữ-thị giác thật sự mạnh (so sánh, không phải hồi tưởng).

Điểm khác biệt so với `--candidates` hiện tại: ứng viên hiện do k-NN chọn theo từng ảnh, nên tập
ứng viên khác nhau mỗi lần và không có bộ phân biệt chuyên biệt nào có thể học được. Cụm thì **cố
định**, nên tầng 2 huấn luyện được, kiểm thử được, và có tập đối chiếu ổn định.

Kiểm chứng rẻ: lấy cụm `{49, 50, 51}` (3 lớp), huấn luyện một linear probe trên đặc trưng DINOv2 có
sẵn, đo độ chính xác trong cụm so với bộ 102 lớp trên đúng các ảnh đó. Một buổi chiều.

## N3 — Neural collapse dự đoán rằng đề xuất A2 sẽ **hại** lớp hiếm **(đề xuất, dựa trên tài liệu)**

Ở tài liệu trước tôi đề xuất đổi encoder chỉ mục sang bản DINOv2 đã fine-tune. Tôi cần rút lại một
nửa đề xuất đó.

**(tài liệu)** Ở giai đoạn cuối huấn luyện, mạng sâu rơi vào *neural collapse* (Papyan, Han &
Donoho 2020): phương sai trong lớp co về 0 và các trung tâm lớp xếp thành simplex ETF. Dưới mất cân
bằng nặng, xảy ra *minority collapse* (Fang et al. 2021): **trung tâm của các lớp hiếm chập vào
nhau**, trở nên không phân biệt được.

**(dẫn xuất)** Truy hồi cần phương sai trong lớp — nó cần biết ảnh nào giống ảnh nào, chứ không chỉ
lớp nào khác lớp nào. Đặc trưng đã collapse là **tối ưu cho phân loại và tệ cho truy hồi**, và mức
độ tệ tỉ lệ nghịch với `n_c`.

Dữ liệu của ta: `n_c` từ 21 đến 400 (chênh 19 lần), macro-F1 thấp hơn accuracy 15 điểm. Cả hai đều
là dấu hiệu điển hình của minority collapse.

**Dự đoán kiểm chứng được:** đổi sang encoder fine-tune sẽ nâng recall@1 ở nhóm lớp đông và **hạ**
ở nhóm lớp hiếm. Nếu đo tổng thể thì có thể thấy "+3 điểm" và tưởng là thắng, trong khi đã phá đúng
phần đuôi dài mà II.6 nói là có giá trị cao nhất.

**Vì vậy: A2 phải đo theo từng nhóm `n_c`, không được đo tổng thể.** Nếu dự đoán đúng, cách xử lý
là ghép hai đặc trưng (pretrained cho đuôi, fine-tune cho đầu), hoặc lấy đặc trưng ở tầng giữa —
tầng chưa collapse.

Đây là ví dụ cho thấy vì sao đo tổng thể một con số là nguy hiểm: nó có thể che một sự đánh đổi
ngược dấu.

## N4 — Cross-fitting để cắt vòng luẩn quẩn **(đề xuất, dựa trên tài liệu)**

Vấn đề ít ai để ý: ensemble được huấn luyện trên dữ liệu **bao gồm** kho tham chiếu. Nên `p(y|x)`
của nó và ước lượng k-NN từ kho **không độc lập** — chúng chia nhau cùng những nhãn sai. Mọi kết
luận từ "hai tín hiệu đồng thuận" đều bị thổi phồng.

Cách chữa mượn từ double/debiased machine learning (Chernozhukov et al. 2018): **cross-fitting**.
Chia kho thành `K` phần. Với ảnh thuộc phần `i`:

* dùng chỉ mục truy hồi dựng **chỉ từ các phần ≠ i**;
* dùng model huấn luyện **chỉ trên các phần ≠ i**.

Khi ấy hai tín hiệu độc lập có điều kiện thật sự, và II.2 mới áp dụng hợp lệ. Chi phí: huấn luyện
`K` lần thay vì 1 lần — với `K = 5` và model tiny thì đây là chi phí một đêm GPU, không phải rào
cản.

Đây cũng chính là điều kiện bắt buộc của confident learning mà tôi đã nhấn mạnh ở tài liệu trước:
xác suất phải là **out-of-fold**. Cross-fitting làm cả hai việc bằng một lần chạy.

## N5 — Ghi lại quỹ đạo huấn luyện để phát hiện nhãn sai **(đề xuất, dựa trên tài liệu)**

**(tài liệu)** Mạng sâu học mẫu sạch trước, ghi nhớ nhãn sai sau (Arpit et al. 2017). Từ đó có
*Area Under the Margin* (Pleiss et al. 2020): theo dõi biên `z_{ỹ} − max_{c≠ỹ} z_c` qua các epoch;
mẫu bị gán sai có AUM thấp một cách đặc trưng, vì mạng phải "chống lại" chính nó để khớp nhãn ấy.

Vì sao đáng làm ở đây: dự án **sẽ** huấn luyện lại model (N4 yêu cầu 5 lần). AUM chỉ cần **ghi log
biên mỗi epoch** — gần như miễn phí nếu làm ngay lúc huấn luyện, và **không thể làm được nữa** sau
khi huấn luyện xong.

Giá trị: AUM là tín hiệu dựa trên **động lực học tối ưu**, độc lập hoàn toàn với tín hiệu hình học
(k-NN) và tín hiệu ngôn ngữ (LLM). Đó là chiều thứ tư, và theo II.2, càng nhiều tín hiệu độc lập
thì càng ước lượng được chính xác mà không cần nhãn.

**Nếu chỉ làm được một việc trước lần huấn luyện tới, hãy làm việc này** — vì nó là việc duy nhất
trong tài liệu có cửa sổ thời gian đóng lại.

## N6 — Conformal prediction dưới nhãn nhiễu **(đề xuất, có cảnh báo)**

Ở tài liệu trước tôi đề xuất conformal prediction. Cần bổ sung một cảnh báo mà tôi đã bỏ sót.

Conformal bảo đảm phủ **so với phân phối của nhãn dùng để hiệu chuẩn**. Nếu tập hiệu chuẩn có
nhiễu `η`, thì bảo đảm là cho **nhãn nhiễu**, không phải nhãn sạch. Với `α = 0,1` và `η = 0,15`,
mức phủ thật đối với nhãn sạch có thể lệch đáng kể.

**(tài liệu)** Có kết quả cho thấy conformal khá bền với một số dạng nhiễu phân tán, và trong nhiều
trường hợp nhiễu làm tập dự đoán **bảo thủ hơn** (rộng hơn mức cần) chứ không mất bảo đảm
(Einbinder et al. 2022). Bảo thủ thì an toàn cho bài toán của ta.

**(đề xuất)** Nhưng ta có một lối thoát sạch mà bài toán chung không có: **ảnh trong
`input/daidien/clean/` đã được người duyệt**. Hiệu chuẩn conformal **chỉ** trên tập đó thì bảo đảm
là bảo đảm thật đối với nhãn sạch. Điều kiện: tập ấy phải **hoán đổi được** với dữ liệu sản xuất —
mà nó **không**, vì đó là các ca *dễ nhất* của mỗi lớp.

Đây là mâu thuẫn thật, chưa giải được: nhãn sạch thì lệch phân phối, nhãn đúng phân phối thì bẩn.
Hướng khả dĩ là weighted conformal prediction dưới covariate shift (Tibshirani et al. 2019), với
trọng số ước lượng bằng một bộ phân loại "ảnh này thuộc tập duyệt hay tập sản xuất". Tôi nêu ra vì
đây là chỗ **nên cẩn thận**, không phải chỗ đã có lời giải.

---

# Phần IV — Thứ tự và cách kiểm chứng

Sắp lại toàn bộ, gồm cả tài liệu trước, theo (giá trị × độ chắc chắn ÷ chi phí):

| # | Việc | Loại | Chi phí |
|---|---|---|---|
| 0 | **Sửa `knn.py`: loại bản sao gần bằng dHash** | **lỗi**, không phải đề xuất | 1 giờ |
| 1 | Đo lại mọi chuẩn sau khi sửa #0 | bắt buộc | 1 giờ |
| 2 | **N5** — ghi AUM trong lần huấn luyện tới | cửa sổ đóng lại | gần như 0 |
| 3 | Bảng 102 lớp → danh pháp + cây phân loại | mở khoá N1, N2, II.5 | thủ công, 1 ngày |
| 4 | **II.5** — trừu tượng hoá thay vì bỏ qua | thắng nhanh, 12% số ca | 1 ngày |
| 5 | **II.3** — nâng n lên 165, thêm bootstrap CI | mọi thứ sau đều dựa vào | 1 ngày |
| 6 | **II.4** — làm sạch bằng Laplacian | đóng góp trung tâm | 2–3 ngày |
| 7 | **N4** — cross-fitting 5 fold | tiền đề cho II.2, cleanlab | 1 đêm GPU |
| 8 | **II.2** — đo độ chính xác không cần nhãn | giám sát liên tục | 1 ngày |
| 9 | **N1** — phân rã ba cơ chế nhiễu | đóng góp mới nhất | 2 ngày |
| 10 | **N2** — bộ phân biệt cấp cụm | thử trên 1 cụm trước | 1 ngày cho thử nghiệm |
| 11 | **N3** — đo A2 theo nhóm `n_c` | tránh một quyết định sai | nửa ngày |
| 12 | **II.6** — ngưỡng theo lớp | cần #5 và #7 | 2 ngày |

Việc số 0 nên làm hôm nay: nó không phải cải tiến, nó là sửa một phép đo sai đang làm chuẩn đối
chiếu cao hơn thực tế 5 điểm.

## Ba phép kiểm mà mọi đề xuất phải qua

1. **Đo theo nhóm `n_c`, không chỉ tổng thể.** N3 cho thấy một cải tiến có thể nâng đầu và hạ đuôi
   mà tổng thể vẫn dương.
2. **Đo cặp precision + coverage, không bao giờ một số.** Và với II.5, thêm cặp thứ hai: exact vs
   consistent precision.
3. **Chạy lại cùng cấu hình hai lần trước khi tin.** Phép này đã từng lộ ra 13 điểm nhiễu do nhiệt
   độ; nếu không làm, "+17 điểm" đã được báo cáo như tiến bộ thật.

---

# Phụ lục — Tài liệu tham khảo

| Chủ đề | Nguồn |
|---|---|
| Gộp nhiều người chấm không tin cậy | Dawid & Skene (1979); Zhang, Chen, Zhou & Jordan, *Spectral Methods meet EM*, NeurIPS 2014 |
| Lan truyền nhãn trên đồ thị | Zhu, Ghahramani & Lafferty, ICML 2003 |
| Cross-fitting / debiased ML | Chernozhukov et al., *Double/Debiased Machine Learning*, 2018 |
| Ghi nhớ nhãn nhiễu | Arpit et al., *A Closer Look at Memorization in Deep Networks*, ICML 2017 |
| Phát hiện nhãn sai qua biên | Pleiss et al., *Identifying Mislabeled Data using the Area Under the Margin*, NeurIPS 2020 |
| Neural collapse | Papyan, Han & Donoho, PNAS 2020; Fang et al., *Minority Collapse*, PNAS 2021 |
| Conformal dưới dịch chuyển phân phối | Tibshirani et al., *Conformal Prediction Under Covariate Shift*, NeurIPS 2019 |
| Conformal dưới nhãn nhiễu | Einbinder et al., 2022 |
| Phân loại có quyền từ chối | Chow (1970); El-Yaniv & Wiener, JMLR 2010 |
| Phát hiện lỗi nhãn | Northcutt, Jiang & Chuang, *Confident Learning*, JAIR 2021 |
| Bộ dữ liệu | Wu et al., *IP102*, CVPR 2019 |

**Lưu ý về cách dùng tài liệu này.** Mọi mục **(đề xuất)** là giả thuyết của tôi, chưa ai kiểm
chứng, kể cả tôi. Các dẫn xuất ở Phần II đúng **nếu** giả thiết nêu kèm đúng — và giả thiết độc lập
có điều kiện ở II.2 thì ta **đã biết là bị vi phạm** trong cấu hình hiện tại. Chỉ các mục **(đo)**
ở Phần I là số thật, và chúng chạy lại được bằng các đoạn mã trong lịch sử phiên làm việc này.
