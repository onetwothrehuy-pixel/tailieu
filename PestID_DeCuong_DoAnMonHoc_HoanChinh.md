**TRƯỜNG ĐẠI HỌC CÔNG NGHIỆP TP. HỒ CHÍ MINH**

**KHOA CÔNG NGHỆ THÔNG TIN**

**ĐỀ CƯƠNG ĐỒ ÁN MÔN HỌC**

**XÂY DỰNG HỆ THỐNG NHẬN DIỆN CÔN TRÙNG GÂY HẠI NÔNG NGHIỆP BẰNG CNN**

*(PestID: A CNN-based Agricultural Pest Classification System)*

**Ngành/Chuyên ngành:** KHOA HỌC MÁY TÍNH      **Mã chuyên ngành:** ⟨điền⟩

**GIẢNG VIÊN HƯỚNG DẪN:** Lê Thị Vĩnh Thanh — Khoa Công nghệ Thông tin, Trường Đại học Công nghiệp TP. Hồ Chí Minh

**SVTH – MSSV:**

| STT | Họ và tên | MSSV | Mảng phụ trách |
|:---:|---|---|---|
| 1 | ⟨điền⟩ | ⟨điền⟩ | Dữ liệu & Tiền xử lý |
| 2 | ⟨điền⟩ | ⟨điền⟩ | Dữ liệu & Tiền xử lý |
| 3 | ⟨điền⟩ | ⟨điền⟩ | Mô hình & Thực nghiệm |
| 4 | ⟨điền⟩ | ⟨điền⟩ | Mô hình & Thực nghiệm |
| 5 | ⟨điền⟩ | ⟨điền⟩ | Ứng dụng & Báo cáo |

**LỚP:** ⟨điền⟩        **KHÓA:** ⟨điền⟩

**Thời gian thực hiện:** 3–4 tháng (14 tuần, 01/9/2026 – 07/12/2026)        **Ngày duyệt đề cương:** ⟨điền⟩

**THÀNH PHỐ HỒ CHÍ MINH, NĂM 2026**

---

# **MỤC LỤC**

> **PHẦN MỞ ĐẦU**
> 1. Lý do chọn đề tài
> 2. Mục tiêu nghiên cứu
> 3. Câu hỏi nghiên cứu
> 4. Đối tượng và phạm vi nghiên cứu
> 5. Tổng quan tình hình nghiên cứu liên quan
> 6. Phương pháp nghiên cứu
> 7. Ý nghĩa khoa học và thực tiễn

> **BỐ CỤC ĐỒ ÁN MÔN HỌC (DỰ KIẾN)**

> **TIẾN ĐỘ THỰC HIỆN**

> **TÀI LIỆU THAM KHẢO**


---

# **PHẦN MỞ ĐẦU**

## **1. Lý do chọn đề tài**

### ***1.1. Phát biểu bài toán***

> Cho một bức ảnh chụp côn trùng gây hại bằng điện thoại phổ thông ngoài đồng ruộng, hệ thống phải trả về tên loài dịch hại có khả năng cao nhất kèm mức độ tin cậy. Đây là bài toán **phân loại ảnh đơn nhãn, mịn (single-label fine-grained image classification)** trên 20 lớp côn trùng gây hại phổ biến tại Việt Nam — tập K20, gồm 20 hạng đầu của một bảng xếp hạng chấm cả 102 lớp của bộ dữ liệu chuẩn IP102 [1] theo bối cảnh dịch hại trong nước (Mục 6.2). Hệ thống chạy trên điện thoại, không yêu cầu kết nối máy chủ, và là công cụ tra cứu tham khảo chứ không thay thế chẩn đoán của cán bộ bảo vệ thực vật.

### ***1.2. Bối cảnh và lý do lựa chọn***

Nông nghiệp vẫn là trụ cột của nền kinh tế Việt Nam, và côn trùng gây hại là một trong những nguyên nhân trực tiếp làm giảm năng suất, chất lượng nông sản cũng như thu nhập của người sản xuất. Trong quy trình bảo vệ thực vật hiện hành, việc xác định đúng loài dịch hại là bước quyết định: chỉ khi định danh chính xác mới có thể chọn đúng biện pháp phòng trừ, đúng hoạt chất và đúng thời điểm. Tuy nhiên, khâu này hiện phụ thuộc gần như hoàn toàn vào kinh nghiệm cá nhân của nông dân hoặc vào cán bộ bảo vệ thực vật — lực lượng mỏng, khó có mặt kịp thời tại từng thửa ruộng khi dịch bùng phát. Hệ quả là tình trạng phun thuốc theo thói quen, phun sai đối tượng, phun thừa liều diễn ra phổ biến, vừa tốn chi phí vừa gây hại cho môi trường và an toàn thực phẩm.

Trong khi đó, điện thoại thông minh đã trở thành thiết bị phổ cập ở nông thôn. Nếu một mô hình học sâu có thể nhận diện loài côn trùng gây hại ngay từ ảnh chụp bằng điện thoại phổ thông, người nông dân sẽ có thêm một kênh tra cứu nhanh trước khi ra quyết định — không thay thế chuyên gia, nhưng rút ngắn đáng kể khoảng cách giữa lúc phát hiện dịch hại và lúc có thông tin định danh đáng tin cậy. Đây chính là bài toán mà đồ án môn học này hướng tới.

Về mặt kỹ thuật, nhận diện côn trùng gây hại là một bài toán **phân loại ảnh mịn (fine-grained image classification)** thuộc loại khó nhất trong thị giác máy tính: các loài trong cùng một họ khác nhau rất ít về hình thái, trong khi cùng một loài lại biến đổi mạnh qua các giai đoạn vòng đời và theo góc chụp, ánh sáng, nền cảnh. Bộ dữ liệu chuẩn quốc tế cho bài toán này là **IP102** (Wu và cộng sự, CVPR 2019) với **75.222 ảnh thuộc 102 lớp côn trùng gây hại** trên tám nhóm cây trồng [1]. Mức độ khó của IP102 thể hiện rõ qua lịch sử kết quả: baseline ResNet-50 do chính nhóm tác giả công bố năm 2019 chỉ đạt **49,4%**, và sau sáu năm với hàng chục công trình cải tiến, kết quả cao nhất mà nhóm rà soát xác minh được là **78,4%** [11] — **chưa phương pháp nào vượt ngưỡng 80%**.

Rà soát tài liệu cho thấy trần độ chính xác này không đến từ việc thiếu kiến trúc mạnh, mà đến từ ba đặc tính của chính dữ liệu: (i) **phân bố đuôi dài nghiêm trọng** với tỷ lệ mất cân bằng khoảng 81 lần giữa lớp nhiều ảnh nhất và lớp ít ảnh nhất; (ii) **nhầm lẫn hình thái** do IP102 gộp trứng, sâu non, nhộng và trưởng thành của cùng một loài vào một lớp, đồng thời trộn nhãn cấp loài với nhãn cấp chi và họ (`aphids`, `Thrips`, `Miridae`, `Cicadellidae`); (iii) **khoảng cách giữa độ chính xác và khả năng triển khai** — mô hình đạt 75,74% của Qian và cộng sự (2025) có tới 224,7 triệu tham số, không thể chạy trên điện thoại [18]. Các nghiên cứu hiện có chủ yếu vá ba vấn đề này ở **tầng mô hình** (thêm mô-đun attention, hợp nhất đa tỷ lệ, kiến trúc lai CNN–Transformer) mà chưa xử lý ở **tầng dữ liệu** — nơi nguyên nhân gốc thực sự nằm.

Một khoảng trống thứ tư mang tính bối cảnh: trong phạm vi tài liệu nhóm đã rà soát, **chưa nghiên cứu IP102 nào chọn tập con theo mức độ phổ biến thực tế của dịch hại tại một quốc gia cụ thể**; tất cả đều chọn theo số lượng ảnh có sẵn. Với Việt Nam, điều này gây lãng phí kép: khoảng 30 lớp trong IP102 gắn với lúa mì, củ cải đường và cỏ linh lăng — những cây trồng Việt Nam không sản xuất thương mại — trong khi hai dịch hại quan trọng bậc nhất của sản xuất trong nước lại **không có lớp tương ứng nào trong IP102**: sâu keo mùa thu *Spodoptera frugiperda*, đối tượng gây hại nghiêm trọng trên ngô kể từ khi xâm nhập Việt Nam năm 2019, và rầy chổng cánh *Diaphorina citri*, môi giới truyền bệnh vàng lá gân xanh trên cây có múi. Nói cách khác, ngay cả một mô hình đạt độ chính xác tuyệt đối trên IP102 cũng không thể nhận diện được hai đối tượng mà cán bộ khuyến nông Việt Nam cần nhất. Đây là giới hạn nền tảng của bộ dữ liệu chứ không phải của phương pháp, và đề tài xử lý nó bằng ba việc: nêu rõ giới hạn ngay trong phần phạm vi, thiết kế cơ chế từ chối trả lời khi mô hình không chắc chắn (Mục 6.6), và để ngỏ đường mở rộng lớp cho các khóa sau khi có đủ dữ liệu ảnh trong nước.

Xuất phát từ thực tiễn và các lý do nêu trên, đồ án môn học lựa chọn hướng nghiên cứu xây dựng hệ thống **PestID**: chọn lọc một tập con IP102 theo bối cảnh Việt Nam, làm sạch dữ liệu ở mức có thể kiểm chứng, so sánh thực nghiệm ba kiến trúc học sâu tiêu biểu **MobileNetV2 [2] / EfficientNet-B0 [3] / ViT-Tiny [4][5]** trên cùng một quy trình huấn luyện chuyển giao, đề xuất một mô hình cải tiến của nhóm, và đóng gói kết quả thành một ứng dụng demo chạy được trên thiết bị di động. Đề tài vừa có ý nghĩa khoa học khi bổ sung bằng chứng thực nghiệm cho một bài toán phân loại mịn còn nhiều khoảng trống, vừa có giá trị ứng dụng thực tiễn trong bối cảnh chuyển đổi số nông nghiệp tại Việt Nam.

## **2. Mục tiêu nghiên cứu**

### ***2.1. Mục tiêu chính***

Mục tiêu chính của đồ án môn học là xây dựng và đánh giá một hệ thống nhận diện côn trùng gây hại nông nghiệp từ ảnh chụp, đạt **Accuracy ≥ 0,85 trên tập kiểm thử cân bằng `test_balanced` của tập con chính IP102-VN-K20 (20 lớp)** — không phải trên toàn bộ 102 lớp của IP102, nơi kết quả cao nhất thế giới hiện mới là 78,4% [11]. Bốn tập con K15, K20, K35 và K45 đều được cắt từ **cùng một bảng xếp hạng 102 lớp** nên lồng nhau theo cấu trúc (K15 ⊂ K20 ⊂ K35 ⊂ K45); ba mốc còn lại chỉ nhằm khảo sát ảnh hưởng của số lớp tới độ chính xác, còn mục tiêu ≥ 0,85 được đối chiếu trên K20.

Hệ thống sử dụng phương pháp học chuyển giao (transfer learning) từ trọng số huấn luyện trước trên ImageNet, so sánh có kiểm soát bảy kiến trúc nền — MobileNetV2 [2], EfficientNet-B0 [3], ViT-Tiny [4][5], MobileNetV4-Conv-S [36], TinyViT-5M [37], FastViT-T12 [38] và iFormer-M [39] — trên cùng một quy trình huấn luyện, sau đó đề xuất một mô hình cải tiến nhằm nâng độ chính xác **mà không tăng số tham số**, qua đó giữ được khả năng triển khai trên điện thoại phổ thông.

Do dữ liệu mất cân bằng, chỉ số chính để kết luận là **macro-F1**; Accuracy được đọc trên tập kiểm thử đã cân bằng số ảnh mỗi lớp để bảo đảm con số ≥ 0,85 phản ánh đúng năng lực phân loại thay vì lợi thế của các lớp nhiều mẫu.

### ***2.2. Mục tiêu cụ thể***

> 1. **Xây dựng tập dữ liệu IP102-VN có căn cứ.** Chấm toàn bộ 102 lớp gốc của IP102 [1] trên **một bảng xếp hạng duy nhất** theo ba tiêu chí có trọng số — mức độ phổ biến và nhu cầu nhận diện tại Việt Nam (40%), mức ảnh hưởng đối với cây trồng (35%) và mức sẵn sàng của dữ liệu (25%) — rồi cắt tại bốn vị trí để sinh **bốn tập con lồng nhau K = 15 / 20 / 35 / 45 lớp, trong đó K20 là tập chính**. Cách chọn này neo vào bối cảnh dịch hại trong nước thay vì số ảnh có sẵn như thông lệ quốc tế, kèm tài liệu ghi rõ điểm thành phần của từng lớp, bảng ánh xạ loài × cây trồng và danh sách ảnh bị loại có lý do.

> 2. **Làm sạch và kiểm chứng chất lượng dữ liệu.** Phát hiện và khử ảnh trùng lặp, ảnh gần trùng, hiện tượng rò rỉ giữa tập huấn luyện và tập kiểm thử, cùng các nhóm ảnh có nhãn mâu thuẫn; đạt toàn bộ các phép kiểm tra chấp nhận về tính tự chứa, tính tái lập và tính truy vết ngược về IP102 gốc.

> 3. **So sánh thực nghiệm bảy kiến trúc nền.** Huấn luyện MobileNetV2, EfficientNet-B0, ViT-Tiny, MobileNetV4-Conv-S, TinyViT-5M, FastViT-T12 và iFormer-M trên cùng một tập dữ liệu, cùng một quy trình tăng cường dữ liệu và cùng một bộ siêu tham số; báo cáo đầy đủ Accuracy, macro-F1, balanced accuracy, số tham số, FLOPs và độ trễ suy luận.

> 4. **Đề xuất và kiểm chứng mô hình cải tiến PestID-KD.** Chưng cất tri thức (knowledge distillation) từ một mạng teacher mạnh sang mạng student nhẹ, kết hợp tăng cường dữ liệu nâng cao (CutMix, MixUp) và cơ chế xử lý mất cân bằng lớp; thực hiện nghiên cứu loại trừ (ablation study) ba mức để tách bạch đóng góp của từng thành phần.

> 5. **Phân tích lỗi có chiều sâu.** Báo cáo độ chính xác tách theo ba nhóm Head/Medium/Tail theo cách làm của Qian và cộng sự [18], công bố ma trận nhầm lẫn chi tiết cho các cặp loài dễ nhầm và bản đồ nhiệt Grad-CAM cho ít nhất 5 cặp — phần mà đa số nghiên cứu hiện có bỏ qua.

> 6. **Xây dựng ứng dụng demo và đo hiệu năng triển khai.** Xuất mô hình tốt nhất sang định dạng **TensorFlow Lite** có lượng tử hóa số nguyên 8 bit, đo kích thước mô hình và độ trễ suy luận trên điện thoại thật, xây dựng giao diện cho phép chụp/tải ảnh và trả về kết quả nhận diện kèm độ tin cậy, có hiển thị dòng cảnh báo bắt buộc: *"Kết quả chỉ mang tính tham khảo, không thay thế chẩn đoán của cán bộ bảo vệ thực vật."*

## **3. Câu hỏi nghiên cứu**

Đề tài được tổ chức xoay quanh sáu câu hỏi nghiên cứu; mỗi câu hỏi gắn với ít nhất một thí nghiệm có đánh số ở Mục 6.5 và một chỉ số dùng để kết luận ở Mục 6.6.

> **RQ1 — Về dữ liệu.** Mức rò rỉ giữa tập huấn luyện và tập kiểm thử trong phép chia chuẩn của IP102 là bao nhiêu, và nó thổi phồng độ chính xác công bố lên bao nhiêu điểm?
> *Trả lời bằng:* thí nghiệm **E6** — huấn luyện cùng một mô hình, đo trên tập kiểm thử đã khử rò rỉ và tập kiểm thử gốc, lấy hiệu số.

> **RQ2 — Về cách chọn tập con.** Tập con cắt từ một bảng xếp hạng neo vào bối cảnh dịch hại Việt Nam có đặc tính thống kê và độ khó khác gì so với tập con chọn theo số lượng ảnh như thông lệ quốc tế?
> *Trả lời bằng:* thí nghiệm **E1** — so sánh phân bố lớp, hệ số Gini, tỷ lệ mất cân bằng và độ chính xác linear probe giữa IP102-VN-K20 và một bộ đối chứng 20 lớp chọn theo số ảnh nhiều nhất. Bộ đối chứng chỉ được dựng **sau khi bảng hạng đã khóa**, và kết quả của nó không được dùng để điều chỉnh bảng hạng (Mục 6.2.2).

> **RQ3 — Về kiến trúc.** Trong điều kiện dữ liệu tinh chỉnh chỉ vài nghìn ảnh mỗi lớp, kiến trúc nào cho tỷ lệ độ chính xác trên chi phí tốt nhất, và nhận định "khi dữ liệu ít, mạng tích chập vượt Vision Transformer" [13] có đúng trên bài toán này không?
> *Trả lời bằng:* thí nghiệm **E3** — so sánh có kiểm soát bảy kiến trúc nền trên cùng dữ liệu, cùng tăng cường, cùng siêu tham số — và **E4** khảo sát ảnh hưởng của số lớp trên bốn mốc K = 15 / 20 / 35 / 45.

> **RQ4 — Về mô hình đề xuất.** Chưng cất tri thức từ một mạng teacher mạnh có nâng được độ chính xác của mạng student nhẹ **mà không tăng số tham số** hay không, và mức cải thiện đến từ thành phần nào?
> *Trả lời bằng:* thí nghiệm **E5** và bảng nghiên cứu loại trừ ba mức.

> **RQ5 — Về khả năng triển khai.** Sau lượng tử hóa, mô hình tốt nhất mất bao nhiêu điểm độ chính xác, còn bao nhiêu megabyte và chạy hết bao nhiêu mili-giây trên một điện thoại phổ thông?
> *Trả lời bằng:* thí nghiệm **E7** — đo kích thước và độ trễ thật, đối chiếu chỉ tiêu ≤ 15 MB và ≤ 500 ms/ảnh.

> **RQ6 — Về khoảng cách miền.** Độ chính xác sụt bao nhiêu khi chuyển từ ảnh Internet của IP102 sang ảnh chụp ngoài đồng bằng điện thoại phổ thông tại Việt Nam?
> *Trả lời bằng:* thí nghiệm **E8** — đánh giá mô hình tốt nhất trên bộ 100–200 ảnh tự thu. Trong phạm vi tài liệu nhóm đã rà soát, chưa nghiên cứu nào công bố con số này, nên câu hỏi được đặt ở dạng đo và báo cáo, không kèm ngưỡng cam kết.

Ba câu hỏi RQ1, RQ2 và RQ6 là phần đóng góp mới của đề tài; RQ3 và RQ5 là phần kiểm chứng lại tri thức đã có trên một bối cảnh dữ liệu mới; RQ4 hiện thực hóa một đề xuất đã được nêu trong tài liệu 2025 nhưng chưa ai thực hiện [18].

## **4. Đối tượng và phạm vi nghiên cứu**

### ***4.1. Đối tượng nghiên cứu***

Đối tượng nghiên cứu của đề tài là **ảnh chụp côn trùng gây hại cây trồng nông nghiệp** và các mô hình học sâu phân loại ảnh mịn áp dụng cho loại ảnh này. Cụ thể, đối tượng được xác định trên ba phương diện: (i) **dữ liệu ảnh côn trùng gây hại** thuộc bộ IP102 [1], giới hạn ở tập con các loài có ý nghĩa với sản xuất nông nghiệp Việt Nam; (ii) **các kiến trúc học sâu** cho bài toán phân loại ảnh — mạng tích chập nhẹ MobileNetV2 [2], mạng tích chập cân bằng theo tỷ lệ hợp thành EfficientNet-B0 [3], và Vision Transformer cỡ nhỏ ViT-Tiny [4][5] — cùng các chiến lược học chuyển giao đi kèm; (iii) **các kỹ thuật cải thiện mô hình trong điều kiện tài nguyên hạn chế**: chưng cất tri thức, tăng cường dữ liệu nâng cao, xử lý phân bố đuôi dài và lượng tử hóa mô hình.

### ***4.2. Phạm vi không gian***

Đề tài được triển khai trong phạm vi dữ liệu ảnh công khai và môi trường tính toán học thuật. Dữ liệu nghiên cứu khai thác từ bộ **IP102** công bố công khai kèm bài báo CVPR 2019 [1]; đề tài **không** thu thập dữ liệu trực tiếp theo thời gian thực từ các nền tảng có giới hạn truy cập. Về bối cảnh ứng dụng, tập con được chọn gắn với các nhóm cây trồng trụ cột của Việt Nam có mặt trong IP102 — lúa, ngô, cây có múi và xoài — và loại trừ các nhóm cây trồng không sản xuất thương mại tại Việt Nam.

Môi trường huấn luyện là **Kaggle Notebook bản miễn phí, cấu hình GPU NVIDIA T4 × 2** (2 × 16 GB). Đây là ràng buộc thực tế quan trọng và định hình toàn bộ thiết kế thực nghiệm.

**Cách khai thác hai GPU.** Đề tài **không dùng huấn luyện phân tán DistributedDataParallel** cho một mô hình. Lý do là mọi kiến trúc trong phạm vi đề tài đều nhỏ — batch 64 ở độ phân giải 224×224 nằm gọn trong 16 GB của một T4 — nên phân tán một mô hình lên hai GPU chỉ cho khoảng 1,7 lần tốc độ mà thêm nhiều phức tạp về đồng bộ và tái lập. Thay vào đó, đề tài chạy **hai thí nghiệm độc lập song song, mỗi GPU một thí nghiệm**, tách bằng biến môi trường `CUDA_VISIBLE_DEVICES`. Vì nền tảng tính hạn mức theo **thời gian phiên** chứ không theo số GPU, cách chia này cho **gấp đôi thông lượng với cùng chi phí hạn mức** — và bài toán trọng tâm của đề tài vốn là so sánh nhiều kiến trúc trên cùng một tập dữ liệu, tức đúng dạng công việc phù hợp nhất với cách chia này.

**Các hạn mức phải tính đến.** Hạn mức GPU khoảng 30 giờ mỗi tuần, phiên chạy tối đa khoảng 12 giờ và có thể bị ngắt sớm hơn khi không tương tác. Vì vậy mọi lượt huấn luyện đều lưu checkpoint sau mỗi epoch kèm trạng thái bộ tối ưu và bộ lập lịch learning rate, để chạy tiếp được từ đúng điểm ngắt thay vì phải huấn luyện lại từ đầu. Việc rút ngắn thời gian huấn luyện dựa vào kích thước mô hình nhỏ, độ phân giải đầu vào 224×224 và huấn luyện độ chính xác hỗn hợp (AMP fp16), chứ không dựa vào việc mở rộng phần cứng.

### ***4.3. Phạm vi thời gian***

Đồ án môn học được thực hiện trong **3–4 tháng**, từ tháng 9/2026 đến tháng 12/2026, phù hợp với khuôn khổ một đồ án môn học và với báo cáo dự kiến 60–70 trang. Nghiên cứu sử dụng dữ liệu tĩnh được thu thập và công bố tại một thời điểm xác định; đề tài không theo dõi diễn biến dịch hại theo mùa vụ hay theo thời gian dài. Kết quả nghiên cứu do đó hướng tới việc kiểm chứng tính khả thi và hiệu quả của giải pháp đề xuất ở quy mô thử nghiệm trong khung thời gian thực hiện đồ án.

### ***4.4. Phạm vi nội dung***

Phạm vi nội dung tập trung vào bài toán **phân loại ảnh đơn nhãn** (mỗi ảnh thuộc đúng một lớp côn trùng), **không** mở rộng sang phát hiện và định vị đối tượng (object detection) dù IP102 có kèm khoảng 19.000 ảnh gắn khung [9][10], và không xử lý đa phương thức. Đề tài chỉ áp dụng các mô hình học sâu và kỹ thuật học chuyển giao hiện đại, không xem xét các phương pháp học máy truyền thống dựa trên đặc trưng thủ công. Cụ thể, nội dung nghiên cứu bao gồm:

> * Khảo sát đặc trưng của dữ liệu ảnh côn trùng gây hại: phân bố đuôi dài, nhầm lẫn hình thái giữa các loài gần nhau và giữa các giai đoạn vòng đời, nhãn cấp chi/họ chồng lấn nhãn cấp loài, ảnh trùng lặp và rò rỉ giữa các tập.

> * Xây dựng tập con IP102-VN theo bộ tiêu chí có trọng số gắn với bối cảnh Việt Nam, kèm quy trình làm sạch và bộ kiểm tra chấp nhận có thể tái lập.

> * Xây dựng và so sánh bảy kiến trúc nền trải từ năm 2018 tới 2025 trên cùng một quy trình học chuyển giao.

> * Đề xuất mô hình cải tiến PestID-KD dựa trên chưng cất tri thức, kèm nghiên cứu loại trừ tách bạch đóng góp của từng thành phần; khảo sát thêm nhánh mở rộng PestID-CropGate sử dụng thông tin cây trồng làm điều kiện đầu vào.

> * Đánh giá định lượng (Accuracy, macro-F1, balanced accuracy, recall từng lớp, ma trận nhầm lẫn) và định tính (Grad-CAM, phân tích các trường hợp sai điển hình).

> * Đóng gói mô hình tốt nhất thành phiên bản triển khai TensorFlow Lite có lượng tử hóa và xây dựng ứng dụng demo nhận diện qua ảnh chụp.

**Nằm ngoài phạm vi.** Đề tài không nhận diện được các loài không có lớp trong IP102 — trong đó có hai dịch hại trọng yếu của Việt Nam là sâu keo mùa thu *Spodoptera frugiperda* và rầy chổng cánh *Diaphorina citri* (xem Mục 1.2). Hệ thống xử lý giới hạn này bằng cơ chế từ chối trả lời khi độ tin cậy thấp, chứ không che giấu nó.

### ***4.5. Đặc tả đầu vào và đầu ra***

```
  Đầu vào                     Xử lý                        Đầu ra
  ─────────                   ─────                        ──────
  Ảnh RGB đơn                 Resize cạnh ngắn → 256       Nhãn top-1  + độ tin cậy
  (chụp hoặc tải lên)    →    CenterCrop 224×224      →    Nhãn top-3  + độ tin cậy
  cạnh nhỏ nhất ≥ 64 px       Chuẩn hóa ImageNet           Cờ "không chắc chắn"
  JPEG/PNG                    Mô hình phân loại            Dòng cảnh báo bắt buộc
  (tùy chọn) cây trồng        Softmax trên 20 lớp
```

**Ví dụ đầu ra của ứng dụng demo:**

```json
{
  "top1": { "ten_loai": "Sâu đục thân ngô châu Á", "ma_lop": 22, "do_tin_cay": 0.91 },
  "top3": [
    { "ten_loai": "Sâu đục thân ngô châu Á", "do_tin_cay": 0.91 },
    { "ten_loai": "Sâu keo",                 "do_tin_cay": 0.05 },
    { "ten_loai": "Sâu xanh",                "do_tin_cay": 0.02 }
  ],
  "khong_chac_chan": false,
  "canh_bao": "Kết quả chỉ mang tính tham khảo, không thay thế chẩn đoán
               của cán bộ bảo vệ thực vật."
}
```

**Quy ước xử lý biên.** Khi độ tin cậy top-1 dưới ngưỡng τ (chốt trên tập kiểm định, dự kiến 0,50), hệ thống đặt cờ `khong_chac_chan = true`, hiển thị cả ba ứng viên top-3 và khuyến nghị người dùng liên hệ cán bộ bảo vệ thực vật thay vì đưa ra một tên loài duy nhất. Việc **không** ép hệ thống luôn trả lời một nhãn là quyết định có chủ đích: 20 lớp của IP102-VN không phủ hết dịch hại Việt Nam, nên một câu trả lời sai chắc nịch nguy hiểm hơn một câu trả lời có bảo lưu. Chất lượng của cơ chế này được đo bằng một thí nghiệm riêng ở Mục 6.6.

## **5. Tổng quan tình hình nghiên cứu liên quan**

Nhằm xác định khoảng trống nghiên cứu và làm rõ tính mới của đề tài, các công trình liên quan đến nhận diện côn trùng gây hại nông nghiệp bằng học sâu — đặc biệt là trên bộ dữ liệu IP102 — được khảo sát theo hai nhóm: nghiên cứu quốc tế và nghiên cứu trong nước.

### ***5.1. Các nghiên cứu quốc tế***

Nghiên cứu quốc tế về nhận diện côn trùng gây hại tập trung gần như tuyệt đối vào bộ dữ liệu IP102 và đi theo bốn hướng chính: nâng cấp kiến trúc, xử lý phân bố đuôi dài, làm nhẹ mô hình để triển khai, và so sánh CNN với Transformer.

> * **Wu và cộng sự (2019)** giới thiệu IP102 — bộ dữ liệu chuẩn cho bài toán này, gồm 75.222 ảnh thuộc 102 lớp trên 8 nhóm cây trồng, phân bố đuôi dài tự nhiên, chia sẵn 45.095 train / 7.508 val / 22.619 test. Các baseline ResNet-50, VGG, DenseNet chỉ đạt 49,4–67%, cho thấy đây là bài toán phân loại mịn rất khó và đặt nền tảng cho mọi nghiên cứu sau này [1].

> * **Ung và cộng sự (2021)** đề xuất kết hợp nhiều mô hình CNN với cơ chế attention và feature pyramid, đạt **74,13%** trên IP102 — vượt kết quả tốt nhất lúc bấy giờ khoảng 7 điểm. Nghiên cứu chứng minh học chuyển giao kết hợp ensemble có hiệu quả rõ rệt, nhưng chi phí tính toán của ensemble khiến hướng này khó triển khai trên thiết bị di động [6].

> * **Setiawan và cộng sự (2022)** xây dựng một khung huấn luyện tối ưu cho MobileNetV2 với learning rate động, tăng cường dữ liệu CutMix, đóng băng theo tầng và điều chuẩn thưa, đạt khoảng **75,6%** với chỉ ~4,2 triệu tham số. Kết luận đáng chú ý: **chiến lược tăng cường dữ liệu và điều chuẩn quan trọng hơn việc tăng kích thước mô hình** trên IP102 [7].

> * **Peng và Wang (2022)** đề xuất khung CTF gồm một backbone CNN nối với đầu phân loại Transformer, đạt 74,897% ở độ phân giải 224px và 75,583% ở 480px. Chênh lệch 0,7 điểm nhưng chi phí tính toán gấp khoảng 4,6 lần — một căn cứ định lượng cho việc giữ độ phân giải 224px trong điều kiện tài nguyên hạn chế [20].

> * **Peng và cộng sự (2024)** cải tiến MobileNetV2 thành PestNet bằng khối attention ASGE, mô-đun hợp nhất hai nhánh đa kích thước nhân và huấn luyện với AdamW + Mixup. Trên tập con **Pest37** (37 lớp trích từ IP102) đạt **87,62% accuracy và 86,90% F1**, tăng 4,20 điểm accuracy so với MobileNetV2 gốc trong khi **giảm 14,1% tham số và 37,5% FLOPs**. Đây là mốc tham chiếu quan trọng nhất cho mục tiêu ≥ 0,85 của đề tài, với điều kiện phải luôn ghi rõ con số này đo trên tập con chứ không phải IP102 đầy đủ [8].

> * **Qian và cộng sự (2025)** đề xuất kiến trúc lai hợp nhất đặc trưng đa tỷ lệ và attention hỗn hợp, đạt 75,74% accuracy / 75,38% F1 trên IP102. Giá trị lớn nhất của công trình nằm ở phần phân tích: nhóm công bố độ chính xác **tách theo Head 72,87% / Medium 77,29% / Tail 75,79%** — phát hiện ngược đời rằng nhóm lớp nhiều mẫu lại kém hơn nhóm lớp ít mẫu do quá khớp; đồng thời chỉ đúng nguyên nhân gốc là IP102 gộp bốn giai đoạn vòng đời vào một lớp. Nhóm tác giả tự nêu hạn chế và **đề xuất dùng mô hình của họ làm teacher để chưng cất sang MobileNet nhưng chưa thực hiện** [18].

> * **Zou và cộng sự (2025)** với AdaptPest-Net kết hợp mạng tích chập, đồ thị và cơ chế Mamba, đạt **78,4% — kết quả cao nhất đã được xác minh trên IP102 đầy đủ**. Cùng hướng kiến trúc phức tạp còn có Pest-ConFormer 77,81% [19], DeWi 76,44% [26] và DWViT-ES 76,00% [27]. Điểm chung: mức tăng chỉ khoảng 4 điểm trong 4 năm dù độ phức tạp mô hình tăng nhiều lần [11].

> * **Một cảnh báo về giao thức so sánh.** Angelescu và cộng sự (2025) công bố F1 84,25% trên IP102 bằng ensemble các Vision Transformer hiệu quả — cao bất thường so với dải 74–78% của mọi công trình khác, nhiều khả năng do dùng phép chia tập hoặc tập con khác [24]. Trường hợp này cho thấy khi lập bảng mốc kết quả, **chỉ được đưa vào các con số cùng giao thức đánh giá**; đây cũng là lý do đề tài dựng riêng một bảng mốc đã xác minh thay vì trích dẫn rời rạc.

> * **Hướng khai thác quy trình sử dụng thay vì kiến trúc.** Chen và cộng sự (2023) đề xuất nhận dạng dựa trên **nhiều ảnh của cùng một cá thể** kết hợp lọc thích nghi, đạt 73,9% trên IP102 với lập luận rất hợp bối cảnh sản xuất — người nông dân có thể dễ dàng chụp nhiều ảnh. Cải thiện ở đây đến từ cách sử dụng chứ không từ độ lớn mô hình, nên gần như miễn phí về kích thước triển khai [25].

> * **Hướng xử lý phân bố đuôi dài** được Chen và cộng sự (2024) tiếp cận bằng tăng cường dữ liệu theo thể hiện kết hợp tinh chỉnh đặc trưng có ràng buộc, cải thiện 5,73 điểm so với baseline ViT [16]; Wang (2025) thay GAN bằng mô hình khuếch tán để sinh ảnh cho lớp hiếm [17]. Cả hai cải thiện lớp hiếm nhưng chưa giải quyết đồng thời hiện tượng quá khớp ở lớp nhiều mẫu.

> * **Hướng làm nhẹ mô hình để triển khai** có Zhang và cộng sự (2024) chứng minh chưng cất tri thức tạo được mô hình nhẹ cho bài toán sâu bệnh hại [22]; Akhtar và cộng sự (2025) so sánh 18 mô hình qua ba chế độ lượng tử hóa và ghi nhận **huấn luyện nhận biết lượng tử hóa (QAT) giảm 75,59% kích thước mô hình**, trong đó MobileNetV2 cho tỷ lệ hiệu năng/kích thước tốt nhất [14]; Kang và cộng sự (2023) triển khai mạng chưng cất nhỏ trên thiết bị biên [23]; Doan (2025) xây dựng hệ thống phát hiện côn trùng thời gian thực chạy trực tiếp trên điện thoại — tham chiếu gần nhất về mặt kỹ thuật triển khai cho sản phẩm của đề tài [30].

> * **Hướng so sánh CNN với Transformer** cho kết luận trực tiếp liên quan tới lựa chọn kiến trúc của đề tài. Jeevan và Sethi (2024) khảo sát có hệ thống nhiều backbone trên nhiều miền ảnh và kết luận rằng **các kiến trúc dựa trên attention thường kém hơn mạng tích chập khi tinh chỉnh với ít dữ liệu**, trong đó ConvNeXt, RegNet và EfficientNet cho kết quả tốt nhất — đây là căn cứ chính cho giả thuyết ở RQ3 [13]. Ở chiều bổ sung, Struniawski và cộng sự (2026) so sánh toàn diện các kiến trúc cổ điển và học sâu trên một bài toán phân loại ảnh sinh học mịn khác (phấn hoa) và ghi nhận rằng khi tách được từng cá thể ra khỏi nền, **cả hai họ kiến trúc đều hội tụ về mức phân biệt gần như hoàn hảo** — nghĩa là khoảng cách CNN–ViT phụ thuộc mạnh vào chất lượng và cách chuẩn bị dữ liệu chứ không phải là một hằng số của kiến trúc; đây là lý do bổ sung để đề tài đầu tư nặng vào tầng dữ liệu [15]. Touvron và cộng sự (2021) giải quyết chính điểm yếu dữ liệu ít của ViT bằng DeiT: DeiT-Tiny khoảng 5,7 triệu tham số đạt **74,5%** top-1 ImageNet sau 300 epoch chỉ với ImageNet-1K nhờ token chưng cất và tăng cường mạnh — so với **72,2%** của cùng kiến trúc khi không dùng token chưng cất, và lên tới **76,6%** nếu kéo dài tới 1.000 epoch [5]. Cặp số 72,2% ↔ 74,5% này là bằng chứng định lượng gần nhất cho giả thuyết của mô hình đề xuất PestID-KD. Ở chiều ngược lại, EfficientFormerV2 cho thấy ViT được tối ưu có thể vượt MobileNetV2 3,5–4 điểm với độ trễ tương đương [12]. Ở đúng quy mô tập dữ liệu mà đề tài dự định sử dụng, Utku và cộng sự (2025) thử nghiệm kiến trúc lai CNN + ViT trên một tập 15 lớp côn trùng gây hại — đây là tham chiếu gần nhất về cách thiết kế thực nghiệm cho tập con cỡ hai chục lớp [28].

### ***5.2. Các nghiên cứu trong nước***

Nghiên cứu trong nước về bài toán này còn rất hạn chế cả về số lượng lẫn phạm vi.

> * **Ung, Ung và Nguyễn (2021)** — nhóm tác giả Việt Nam — là công trình trong nước nổi bật nhất về IP102, đạt 74,13% bằng ensemble CNN kết hợp attention và feature pyramid [6]. Nghiên cứu sử dụng nguyên bộ IP102 gốc với 102 lớp, không điều chỉnh theo bối cảnh dịch hại trong nước.

> * **Nguyễn và cộng sự (2024)** tiếp nối hướng này với DeWi, đạt 76,44% trên IP102. Đóng góp nằm ở **chiến lược huấn luyện** (hàm mất mát triplet margin kết hợp Mixup) chứ không phải kiến trúc mới, nên chi phí cài đặt thấp — đây là điểm phù hợp với điều kiện của một nhóm sinh viên [26].

> * **Doan (2025)** xây dựng hệ thống phát hiện côn trùng thời gian thực trên thiết bị di động — công trình trong nước gần nhất với phần sản phẩm của đề tài, cho thấy hướng triển khai trên điện thoại là khả thi trong điều kiện nghiên cứu Việt Nam [30].

> * Ngoài ba công trình trên, phần lớn nghiên cứu về nhận diện sâu bệnh hại tại Việt Nam tập trung vào bệnh trên lá lúa, lá cây ăn quả hoặc sử dụng các bộ dữ liệu nhỏ tự thu, chưa hình thành một bộ dữ liệu ảnh côn trùng gây hại của Việt Nam được công bố mở.

> * Ở khía cạnh cơ sở dữ liệu bối cảnh, hệ thống văn bản quản lý nhà nước về bảo vệ thực vật lại khá đầy đủ và có thể khai thác làm căn cứ chọn loài: quy chuẩn về phương pháp điều tra phát hiện dịch hại cây trồng [33], Danh mục đối tượng kiểm dịch thực vật [34] và các báo cáo tình hình sinh vật gây hại định kỳ của cơ quan bảo vệ thực vật [35]. Đây là nguồn dữ liệu mà chưa nghiên cứu học sâu nào về IP102 sử dụng.

### ***5.3. Nhận xét chung và khoảng trống nghiên cứu***

> **Về phạm vi của các nhận định dưới đây.** Các khoảng trống được phát biểu **trong phạm vi 40 tài liệu mà nhóm đã rà soát và xác minh nguồn** (giai đoạn 2019–2026, từ CVPR/ICCV/ICLR/ICML/NeurIPS/ECCV, Elsevier, Springer, MDPI, Frontiers và arXiv), chứ không phải một khẳng định về toàn bộ tài liệu hiện có. Nếu phát hiện công trình phản ví dụ trong quá trình thực hiện, nhóm sẽ cập nhật lại phần này của báo cáo.

Có thể thấy, các nghiên cứu quốc tế đã khai thác gần cạn hướng cải tiến kiến trúc: trần độ chính xác trên IP102 đầy đủ dừng ở 74–78% sau sáu năm, trong khi độ phức tạp mô hình tăng nhiều lần. Nguyên nhân gốc — nhãn nhiễu, nhiều giai đoạn vòng đời trong cùng một lớp, nhãn cấp chi/họ chồng lấn nhãn cấp loài — được nhận diện nhưng chỉ được vá ở tầng mô hình, chưa được xử lý ở tầng dữ liệu. Các nghiên cứu trong nước thì mới dừng ở việc áp dụng IP102 nguyên trạng, chưa gắn với bối cảnh dịch hại Việt Nam và chưa tạo ra dữ liệu gốc trong nước.

Từ đó, đề tài xác định **sáu khoảng trống nghiên cứu** và lựa chọn tập trung vào bốn khoảng trống đầu tiên trong phạm vi một đồ án môn học:

> 1. **Nguyên nhân gốc chưa được xử lý ở tầng dữ liệu.** Không nghiên cứu nào trong phạm vi rà soát công bố phiên bản IP102 đã được làm sạch nhãn chồng lấn, khử trùng lặp và khử rò rỉ giữa các tập. Mọi con số accuracy công bố trên IP102 đều đo trên phép chia gốc chưa kiểm chứng về mặt này [1][11][18].

> 2. **Không có nghiên cứu nào chọn tập con theo bối cảnh quốc gia.** Toàn bộ tài liệu khảo sát chọn tập con theo số lượng ảnh — Pest37 của Peng và cộng sự [8] là ví dụ điển hình — chứ không theo mức độ phổ biến thực tế của dịch hại tại một quốc gia. Đây là khoảng trống hoàn toàn bỏ ngỏ.

> 3. **Ma trận nhầm lẫn chi tiết chưa được công bố.** Các nghiên cứu báo cáo accuracy tổng, một số ít báo cáo tách Head/Medium/Tail [18], nhưng không công trình nào chỉ rõ cặp loài nào bị nhầm với nhau bao nhiêu — trong khi đây chính là thông tin cần thiết để hiểu bài toán phân loại mịn.

> 4. **Khoảng cách giữa độ chính xác và khả năng triển khai chưa được bắc cầu.** Đề xuất dùng mô hình mạnh làm teacher để chưng cất sang mạng nhẹ đã được nêu tên trong một công trình 2025 nhưng **chưa ai thực hiện** [18]. Trong khi đó, các mảnh ghép kỹ thuật đều đã sẵn có: chưng cất tri thức cho bài toán sâu bệnh hại [22], mô hình nền chuyên ngành côn trùng học có trọng số công khai [31][32][40], và công cụ lượng tử hóa đã được đo đạc [14].

> 5. **Thiếu số liệu đo trên thiết bị thật.** Đa số nghiên cứu chỉ báo tham số và FLOPs; số ít báo độ trễ thì đo trên GPU máy trạm chứ không phải điện thoại phổ thông [14][30].

> 6. **Chưa khai thác ngữ cảnh cây trồng.** Người dùng thực tế luôn biết mình đang trồng cây gì — thông tin miễn phí thu hẹp đáng kể không gian lớp — nhưng chưa mô hình nào nhận cây trồng làm điều kiện đầu vào. Ý tưởng này được nêu trong phần hướng phát triển của [18] nhưng chưa được thực hiện.

Đề tài hướng đến lấp bốn khoảng trống đầu bằng việc: xây dựng tập con IP102-VN theo tiêu chí bối cảnh Việt Nam và làm sạch có kiểm chứng (khoảng trống 1 và 2); công bố ma trận nhầm lẫn chi tiết kèm Grad-CAM (khoảng trống 3); và hiện thực hóa mô hình chưng cất tri thức PestID-KD mà tài liệu đã đề xuất nhưng chưa thực hiện (khoảng trống 4). Khoảng trống 5 được xử lý ở mức đo đạc trên điện thoại của thành viên nhóm; khoảng trống 6 được khảo sát ở mức thí nghiệm kiểm chứng và chỉ triển khai đầy đủ nếu quỹ thời gian cho phép.

## **6. Phương pháp nghiên cứu**

Phương pháp nghiên cứu được thiết kế theo mạch **Vấn đề → Dữ liệu → Mô hình → Thực nghiệm → Hệ thống**, kết hợp nghiên cứu lý thuyết với nghiên cứu thực nghiệm, và bảo đảm mọi kết luận đều dựa trên số liệu đo được chứ không phải nhận định định tính.

### ***6.1. Phương pháp nghiên cứu lý thuyết***

> * **Phương pháp phân tích và tổng hợp tài liệu:** thu thập và phân tích các công trình về học sâu, phân loại ảnh mịn, nhận diện côn trùng gây hại và các kỹ thuật làm nhẹ mô hình; tổng hợp thành hệ thống lý thuyết nền tảng cho đề tài. Nguồn tài liệu ưu tiên giai đoạn 2021–2026 từ CVPR/ICCV/ICLR/ICML/NeurIPS/ECCV, Elsevier, Springer, MDPI, Frontiers và arXiv; bài tổng quan năm 2025 của Ejaz và cộng sự về phân loại sâu hại bằng học sâu [29] được dùng làm khung định vị các hướng nghiên cứu trước khi đọc sâu từng công trình.

> * **Phương pháp so sánh, đối chiếu:** đối chiếu các kiến trúc học sâu (mạng tích chập nhẹ, mạng tích chập cân bằng theo tỷ lệ hợp thành, Vision Transformer) và các chiến lược học chuyển giao (đóng băng backbone, tinh chỉnh toàn phần, tinh chỉnh theo tầng) để lựa chọn cấu hình phù hợp, cân bằng giữa độ chính xác và chi phí tính toán trong hạn mức Kaggle Notebook T4 × 2.

> * **Phương pháp hệ thống hóa:** xây dựng bảng mốc kết quả đã xác minh trên IP102 (từ baseline 49,4% năm 2019 đến 78,4% năm 2025) làm khung tham chiếu để đặt kết quả của nhóm vào đúng bối cảnh, thay vì so sánh rời rạc giữa các con số có giao thức đánh giá khác nhau.

### ***6.2. Phương pháp thu thập và chọn lọc dữ liệu***

Dữ liệu nghiên cứu lấy từ bộ **IP102** công khai [1] — 75.222 ảnh, 102 lớp, phép chia gốc 45.095 train / 7.508 val / 22.619 test. Đo trực tiếp trên bản tải về, lớp lớn nhất là `Cicadellidae` với **5.740 ảnh** và lớp nhỏ nhất là `Erythroneura apicalis` với **71 ảnh**, tức tỷ lệ mất cân bằng **80,8 lần**. Vì phạm vi một đồ án môn học không cho phép huấn luyện và phân tích đủ sâu trên toàn bộ 102 lớp, đề tài trích các tập con theo nguyên tắc **một bảng xếp hạng duy nhất, bốn điểm cắt**.

Toàn bộ 102 lớp được đưa vào cùng một bảng, chấm theo cùng ba tiêu chí, sắp xếp giảm dần rồi cắt tại bốn vị trí:

```text
102 lớp  →  chấm ba tiêu chí  →  xếp hạng 1–102  →  cắt tại 15, 20, 35 và 45

K15 = hạng 1–15        K20 = hạng 1–20
K35 = hạng 1–35        K45 = hạng 1–45
```

Vì bốn tập đều là tiền tố của cùng một bảng hạng nên quan hệ `K15 ⊂ K20 ⊂ K35 ⊂ K45` được bảo đảm bằng **chính cấu trúc của phép cắt**, không phải bằng một bước kiểm tra sau. **K20 là tập chính** — mọi mục tiêu, câu hỏi nghiên cứu và cam kết số liệu của đề cương đều neo vào K20. K15, K35 và K45 là ba mốc quy mô phục vụ khảo sát ảnh hưởng của số lớp ở thí nghiệm E4.

**Vì sao rút về một bảng hạng.** Phương án trước của nhóm dùng nhiều tầng: một bộ tiêu chí độ phổ biến làm ngưỡng lọc để giữ 60 lớp đầu, một điểm tổng hợp thứ hai xếp hạng lại trong nhóm đó, kèm hạn ngạch cứng chèn thêm một số lớp bắt buộc. Cách đó có hai điểm yếu không sửa được bằng cách trình bày rõ hơn.

> * **Không truy được về một nguồn duy nhất.** Một lớp có thể được giữ theo quy tắc này nhưng bị loại theo quy tắc khác, và người đọc không nhìn thẳng từ một bảng ra thành phần của từng tập K. Riêng nhóm lớp hạn ngạch còn nằm hẳn ngoài hệ thống điểm, nên mỗi lần trình bày lại phải giải thích một ngoại lệ.

> * **Tiêu chí xếp hạng đọc chính thứ đề tài sắp đo.** Thành phần `S_tách` của điểm tổng hợp cũ đo mức dễ nhận dạng của lớp trong không gian đặc trưng — tức xếp hạng lớp theo đúng đại lượng mà chương thực nghiệm sẽ báo cáo. Việc đó làm độ chính xác công bố đẹp lên bởi một phần không phải tiến bộ kỹ thuật, và hai biện pháp bảo đảm liêm chính kèm theo (chỉ chấm trên khối phát triển, giữ một bộ đối chứng) chỉ **đo được** phần thổi phồng chứ không **loại bỏ** được nó.

Quy trình mới bỏ hẳn cả hai vấn đề: chỉ còn một đường quyết định, và **không tiêu chí nào trong bảng điểm được đo bằng kết quả mô hình**.

#### **6.2.1. Ba tiêu chí chấm điểm**

Mỗi lớp được chấm từ 0 đến 5 theo ba tiêu chí; nhóm có thể dùng bước 0,1 khi điểm đồng thuận nằm giữa hai mức.

| Mã | Tiêu chí | Trọng số | Câu hỏi cần trả lời | Căn cứ đánh giá |
|:---:|---|---:|---|---|
| **P** | Mức độ phổ biến và nhu cầu nhận diện tại Việt Nam | 40% | Đối tượng có thường gặp và có nhu cầu nhận diện thực tế hay không? | Báo cáo tình hình sinh vật gây hại của cơ quan bảo vệ thực vật [35]; quy chuẩn điều tra phát hiện dịch hại cây trồng [33]; Danh mục đối tượng kiểm dịch thực vật [34] |
| **A** | Mức ảnh hưởng đối với cây trồng | 35% | Đối tượng gây hại trên cây trồng quan trọng và tạo thiệt hại đáng kể hay không? | Vị trí của cây ký chủ trong cơ cấu sản xuất Việt Nam và mức thiệt hại năng suất được ghi nhận |
| **D** | Mức sẵn sàng của dữ liệu | 25% | Lớp có đủ ảnh sạch, nhãn sử dụng được và phù hợp để dựng tập huấn luyện hay không? | Số ảnh còn lại sau làm sạch C1–C7, khả năng định danh của nhãn, mức chồng lấn phân loại học |

```text
Điểm tổng = 8 × P + 7 × A + 5 × D           (tối đa 100 điểm)
```

| Mức chấm | Diễn giải chung cho cả ba tiêu chí |
|---:|---|
| 0 | Không có căn cứ hoặc không phù hợp với mục tiêu đề tài |
| 1 | Rất thấp |
| 2 | Thấp |
| 3 | Trung bình |
| 4 | Cao |
| 5 | Rất cao |

Điểm tổng được làm tròn đến **0,5 điểm** để bảng dễ đọc và không tạo cảm giác chính xác giả. Khi hai lớp bằng điểm, phá hòa theo thứ tự cố định:

```text
D cao hơn  →  số ảnh sạch nhiều hơn  →  IP102 ID nhỏ hơn
```

Ba khóa này xác định duy nhất và tái lập được. **Số ảnh sạch chỉ là dữ liệu hỗ trợ cho tiêu chí `D` và là khóa phá hòa thứ hai; nó không được dùng như một bảng hạng thứ hai.**

**Ba điều bộ tiêu chí này cố tình không làm.**

> * **Không có vòng loại đặt trước bảng điểm.** Không còn bước "giữ 60 lớp đầu" hay bất kỳ điều kiện loại tuyệt đối nào. Các đặc tính từng dùng làm điều kiện loại — nhãn không định danh được, nhãn cấp chi/họ chồng lấn nhãn cấp loài, số ảnh sạch quá thấp — nay được chấm vào tiêu chí `D` và tự đẩy lớp xuống cuối bảng. Ví dụ rõ nhất là `Rầy lá (họ)` (IP102 ID 101) với **5.645 ảnh**, lớp nhiều ảnh nhất trong toàn bộ dữ liệu sau làm sạch: vì đây là nhãn cấp họ chồng lấn với các lớp cấp loài đã có mặt nên `D` thấp, và lớp này rơi xuống **hạng 58**, nằm ngoài K45. Kết quả trùng với quy tắc loại của phương án cũ, nhưng lần này đến từ chính bảng điểm chứ không từ một quy tắc riêng.

> * **Không có hạn ngạch, không chèn lớp thủ công.** Các đối tượng trọng yếu của sản xuất trong nước được giữ vì chúng **đạt điểm cao**, không vì một danh sách bắt buộc đặt bên ngoài hệ thống điểm: `Rầy nâu hại lúa` (ID 7) ở hạng 3 và `Rầy lưng trắng` (ID 8) ở hạng 16 đều vào K20 nhờ `P` và `A` gần tối đa. Cột `chon_theo ∈ {điểm, hạn_ngạch}` của phiên bản trước vì thế không còn cần thiết và được bỏ khỏi `classes.csv`.

> * **Không dùng kết quả mô hình để xếp hạng.** Accuracy, macro-F1, độ chính xác linear probe và mọi thước đo "dễ nhận dạng" quan sát sau huấn luyện **không được phép** xuất hiện trong bảng điểm hoặc dùng để điều chỉnh thứ hạng, ở bất kỳ vòng nào. Đây là ràng buộc thay thế cho hai biện pháp bảo đảm liêm chính của phương án cũ, và nó mạnh hơn: khi tiêu chí chọn lớp không đọc kết quả mô hình thì phần thổi phồng do chọn lớp dễ **không tồn tại** để phải đo. Bộ đối chứng vẫn được giữ ở thí nghiệm E1, nhưng với một vai trò khác — trả lời RQ2 về khác biệt giữa cách chọn theo bối cảnh Việt Nam và thông lệ quốc tế là chọn theo số lượng ảnh.

#### **6.2.2. Quy trình chấm và khóa bảng hạng**

Bảy bước dưới đây là toàn bộ quy trình chọn lớp của đề tài. Không có bước nào khác được phép chèn thêm.

> 1. **Dựng khung bảng.** Một hàng cho mỗi IP102 ID từ 0 đến 101; mỗi hàng giữ đồng thời tên hiển thị tiếng Việt và nhãn gốc IP102.

> 2. **Nạp dữ liệu tham chiếu.** Hiển thị số ảnh sạch sau C1–C7, trạng thái nhãn (định danh được hay không, cấp loài hay cấp chi/họ) và thông tin bối cảnh Việt Nam để mọi thành viên chấm trên cùng một nền thông tin.

> 3. **Chấm ba điểm `P`, `A`, `D` cho từng lớp** trong một buổi làm việc chung; tính điểm tổng bằng **một công thức duy nhất** ở Mục 6.2.1.

> 4. **Sắp xếp 102 lớp** theo điểm tổng giảm dần, phá hòa theo ba khóa cố định.

> 5. **Kiểm tra 45 hạng đầu**: mỗi hạng ứng với một IP102 ID duy nhất, và mỗi lớp có ít nhất **200 ảnh sạch**. Nếu một lớp không đạt, nhóm phải sửa điểm `D` hoặc xử lý lại dữ liệu của lớp đó rồi **xếp lại toàn bảng** — không thay lớp âm thầm ở một vị trí.

> 6. **Khóa bảng hạng theo phiên bản** trước khi dựng dữ liệu và trước khi xem bất kỳ kết quả test nào. Mọi thay đổi sau đó phải tạo một phiên bản mới kèm lý do; phiên bản đang dùng được ghi vào `build_info.json`.

> 7. **Sinh bốn tập K bằng phép cắt tiền tố** trên bảng đã khóa. Không tạo bốn danh sách độc lập.

**Trạng thái của bảng điểm hiện tại.** Bảng ở Mục 6.2.3 là **phương án đề xuất trước buổi chấm chung**, không phải kết quả một cuộc biểu quyết đã diễn ra. Các thành viên còn có thể thảo luận và hiệu chỉnh điểm thành phần; chỉ sau khi thống nhất, nhóm mới khóa phiên bản và bắt đầu dựng dữ liệu. Báo cáo cuối kỳ phải trình bày đúng trạng thái này của bảng điểm tại thời điểm khóa.

**Điều gì được phép thay đổi thứ hạng, điều gì không.** Được phép: phát hiện sai sót về căn cứ nông học của `P` hoặc `A`; số ảnh sạch thay đổi sau khi sửa quy trình làm sạch, dẫn tới phải chấm lại `D`. Không được phép, trong mọi trường hợp: điều chỉnh thứ hạng vì một lớp cho kết quả kém trong thực nghiệm, vì một tập K "trông dễ hơn" ở biểu đồ t-SNE, hay vì linear probe báo trần trên thấp. Ranh giới này là điều kiện để con số cuối cùng của đề tài đọc được.

#### **6.2.3. Kết quả áp dụng — bốn mươi lăm hạng đầu và bốn mốc K**

Bảng dưới là **45 hạng đầu của bảng xếp hạng đề xuất**, nguồn đầy đủ 1–102 kèm điểm thành phần lưu tại `docs/phuong_phap/PestID_BangXepHang_102_Lop_Moi.md`. Cột *Mốc đầu tiên* cho biết lớp bắt đầu xuất hiện ở tập nào; lớp đã xuất hiện thì có mặt trong mọi tập K lớn hơn. Cột *Ảnh sạch* là số ảnh còn lại sau làm sạch C1–C7.

| Hạng | IP102 ID | Tên đối tượng | Điểm | Ảnh sạch | Mốc đầu tiên |
|---:|---:|---|---:|---:|:---:|
| 1 | 0 | Sâu cuốn lá nhỏ | 98,0 | 845 | K15 |
| 2 | 3 | Sâu đục thân bướm | 97,0 | 921 | K15 |
| 3 | 7 | Rầy nâu hại lúa | 96,5 | 751 | K15 |
| 4 | 15 | Dế dũi hại gốc | 95,5 | 1.371 | K15 |
| 5 | 21 | Nhện đỏ hại cây | 95,0 | 505 | K15 |
| 6 | 22 | Sâu đục thân ngô | 94,5 | 1.499 | K15 |
| 7 | 24 | Rệp muội hại ngô | 94,0 | 3.913 | K15 |
| 8 | 39 | Sâu xanh da láng | 93,5 | 1.451 | K15 |
| 9 | 67 | Bọ đèn đốm hại quả | 92,5 | 4.145 | K15 |
| 10 | 68 | Xén tóc đục thân cà phê | 92,0 | 1.080 | K15 |
| 11 | 70 | Bọ xít xanh | 91,5 | 5.028 | K15 |
| 12 | 71 | Bọ phấn trắng | 91,0 | 668 | K15 |
| 13 | 77 | Rệp sáp vảy dính | 90,5 | 305 | K15 |
| 14 | 88 | Sâu vẽ bùa cam quýt | 90,0 | 330 | K15 |
| 15 | 96 | Mọt cắt lá xoài | 89,5 | 202 | K15 |
| 16 | 8 | Rầy lưng trắng | 89,0 | 805 | K20 |
| 17 | 4 | Sâu đục thân bướm hai chấm | 88,5 | 444 | K20 |
| 18 | 84 | Ruồi đục quả phương Đông (ruồi vàng) | 88,0 | 378 | K20 |
| 19 | 86 | Sâu khoang | 87,5 | 1.119 | K20 |
| 20 | 92 | Bọ trĩ vàng hại xoài, ớt | 87,0 | 624 | K20 |
| 21 | 5 | Sâu năn (muỗi hành) | 86,5 | 375 | K35 |
| 22 | 74 | Nhện đỏ cam chanh | 86,0 | 310 | K35 |
| 23 | 23 | Sâu cắn gié / sâu cắn lá ngô | 85,5 | 1.004 | K35 |
| 24 | 91 | Rệp muội xanh hại cam | 85,0 | 279 | K35 |
| 25 | 90 | Rệp muội đen | 84,5 | 203 | K35 |
| 26 | 37 | Bọ nhảy | 84,0 | 726 | K35 |
| 27 | 56 | Sâu xanh bướm cải | 83,5 | 455 | K35 |
| 28 | 11 | Rầy xanh đuôi đen | 83,0 | 381 | K35 |
| 29 | 18 | Sâu xám | 82,5 | 749 | K35 |
| 30 | 48 | Châu chấu | 82,0 | 1.346 | K35 |
| 31 | 76 | Rệp sáp bông (rệp sáp Úc) | 81,5 | 530 | K35 |
| 32 | 82 | Bọ phấn gai đen hại cam | 81,0 | 433 | K35 |
| 33 | 100 | Bọ vòi voi đục quả xoài | 80,5 | 387 | K35 |
| 34 | 87 | Ngài chích hút quả | 80,0 | 286 | K35 |
| 35 | 78 | Rệp sáp sáp hồng | 79,5 | 204 | K35 |
| 36 | 14 | Sùng trắng (ấu trùng bọ hung) | 79,0 | 767 | K45 |
| 37 | 26 | Sâu đục quả (sâu đục trái) | 78,5 | 550 | K45 |
| 38 | 9 | Rầy nâu nhỏ | 78,0 | 511 | K45 |
| 39 | 99 | Xén tóc đục cành xoài | 77,5 | 507 | K45 |
| 40 | 93 | Muỗi năn hại lá, hoa xoài | 77,0 | 463 | K45 |
| 41 | 73 | Sâu non bướm phượng | 76,5 | 444 | K45 |
| 42 | 64 | Rệp sáp phấn Comstock | 76,0 | 260 | K45 |
| 43 | 97 | Sâu đục ngọn, đục chồi xoài | 75,5 | 256 | K45 |
| 44 | 58 | Sâu nái / sâu róm dẹt | 75,0 | 1.396 | K45 |
| 45 | 1 | Sâu xanh nhỏ hại lúa | 74,5 | 438 | K45 |

**Danh sách máy đọc.** Bốn tập K không được viết tay mà sinh từ đúng một mảng, để mã nguồn và báo cáo không thể lệch nhau:

```python
RANK_1_45 = [
    0,  3,  7, 15, 21, 22, 24, 39, 67, 68, 70, 71, 77, 88, 96,
    8,  4, 84, 86, 92,
    5, 74, 23, 91, 90, 37, 56, 11, 18, 48, 76, 82, 100, 87, 78,
   14, 26,  9, 99, 93, 73, 64, 97, 58,  1,
]

K15 = RANK_1_45[0:15]     # tập nhỏ nhất, mốc quy mô dưới
K20 = RANK_1_45[0:20]     # TẬP CHÍNH — mọi mục tiêu của đề cương neo vào đây
K35 = RANK_1_45[0:35]
K45 = RANK_1_45[0:45]     # tập lớn nhất, cũng là tập gốc để chia dữ liệu
```

**Đặc tính bốn tập trước khi chia và áp trần.**

| Mốc | Số lớp | Tổng ảnh sạch | Lớp nhỏ nhất | Lớp lớn nhất | Tỷ lệ mất cân bằng |
|:---:|:---:|---:|---|---|:---:|
| **K15** | 15 | 23.014 | 202 (ID 96) | 5.028 (ID 70) | 24,9× |
| **K20** | 20 | 26.384 | 202 (ID 96) | 5.028 (ID 70) | 24,9× |
| **K35** | 35 | 34.052 | 202 (ID 96) | 5.028 (ID 70) | 24,9× |
| **K45** | 45 | 39.644 | 202 (ID 96) | 5.028 (ID 70) | 24,9× |

Cả hai đầu mút của dải đều nằm trong K15, nên tỷ lệ mất cân bằng **giống nhau ở cả bốn mốc**: mở rộng từ K15 lên K45 làm tăng số lớp và tổng số ảnh nhưng không làm bài toán mất cân bằng nặng thêm. Đây là một tính chất thuận lợi cho thí nghiệm E4, vì chênh lệch macro-F1 giữa bốn mức K đọc được như ảnh hưởng của **số lớp**, không lẫn với ảnh hưởng của mức mất cân bằng. Ở mọi mốc, tỷ lệ 24,9× đã thấp hơn nhiều so với **80,8×** của IP102 đầy đủ, và còn giảm tiếp sau khi áp trần huấn luyện ở Mục 6.3.

**Nhận xét về thành phần bốn tập.** K15 gồm các đối tượng có `P` và `A` cao nhất, trải trên bốn nhóm cây trồng trụ cột của Việt Nam có mặt trong IP102 — lúa (ID 0, 3, 7), ngô (ID 15, 22, 24), cây có múi (ID 88) và xoài (ID 96) — cùng các loài đa ký chủ (ID 21, 39, 67, 70, 71, 77) và cà phê (ID 68). K20 bổ sung năm lớp gồm `Rầy lưng trắng`, `Sâu đục thân bướm hai chấm`, `Ruồi đục quả phương Đông`, `Sâu khoang` và `Bọ trĩ vàng` — đều là đối tượng có trong hệ thống báo cáo dịch hại chính thức. Từ K35 trở đi, bảng bắt đầu nhận các lớp có điểm `P`/`A` khá nhưng `D` hạn chế hơn, thể hiện qua số ảnh sạch nhỏ dần: bảy trong mười lớp cuối của K45 có dưới 550 ảnh sạch. Đây là ranh giới tự nhiên của bộ dữ liệu và là lý do đề tài dừng phép cắt ở hạng 45.

#### **6.2.4. Tên lớp, nhãn gốc và trạng thái phân loại học**

Một số tên hiển thị tiếng Việt đang dùng rộng hơn hoặc hẹp hơn nhãn gốc IP102, rõ nhất ở các ID 24, 67, 68, 70 và 71. Điều này **không làm thay đổi thứ hạng đã chấm**, vì `P`, `A` và `D` được chấm cho đối tượng mà nhãn đó đại diện trong bối cảnh Việt Nam. Nhưng nó bắt buộc manifest phải lưu đủ bốn trường cho mỗi lớp:

```text
display_name_vi        tên hiển thị trong ứng dụng và báo cáo
original_ip102_name    nhãn gốc của IP102, giữ nguyên không sửa
canonical_taxon        đơn vị phân loại tương ứng, nếu xác định được
taxonomy_status        cấp của nhãn: loài / chi / họ / chưa xác định
```

Nhờ trường `taxonomy_status`, phần phân tích lỗi ở Mục 6.6 tách được hai loại nhầm lẫn khác hẳn nhau về bản chất: nhầm giữa hai lớp cấp loài (mô hình chưa học đủ đặc trưng phân biệt) và nhầm giữa một lớp cấp loài với một lớp cấp chi hoặc họ bao trùm nó (nhãn tự mâu thuẫn, không phải lỗi của mô hình).

Tên khoa học chỉ được cập nhật sau khi rà ảnh và có căn cứ định danh. **Không đổi ý nghĩa nhãn của một phiên bản dữ liệu đã công bố**: nếu cần sửa, nhóm tạo phiên bản mới và ghi rõ thay đổi, để mọi kết quả đã báo cáo vẫn truy được về đúng bảng nhãn sinh ra nó.

#### **6.2.5. Nguồn dữ liệu bổ sung và quy trình gán nhãn thực địa**

Song song với tập con IP102-VN, nhóm tự thu 100–200 ảnh sâu hại thực địa tại Việt Nam bằng điện thoại phổ thông, có gán nhãn và kèm siêu dữ liệu về địa điểm, thời gian, cây trồng. Bộ ảnh này **không dùng để huấn luyện** mà chỉ dùng để đo mức sụt độ chính xác khi chuyển từ ảnh Internet sang ảnh chụp ngoài đồng — con số mà chưa nghiên cứu nào trong phạm vi rà soát công bố.

**Quy trình gán nhãn.** Bộ ảnh tự thu là dữ liệu **gốc do nhóm tạo ra**, nên chất lượng nhãn quyết định giá trị của toàn bộ phép đo khoảng cách miền ở Mục 6.6. Quy trình gồm bốn bước:

> 1. **Chụp có kèm ngữ cảnh.** Mỗi cá thể chụp tối thiểu 2 ảnh (một cận cảnh, một có nền cây ký chủ), ghi lại địa điểm, ngày giờ, cây trồng và giai đoạn sinh trưởng. Dùng điện thoại phổ thông, không dùng ống kính macro chuyên dụng, để ảnh giống điều kiện người dùng thật.

> 2. **Gán nhãn độc lập hai vòng.** Hai thành viên gán nhãn độc lập trên cùng bộ ảnh bằng công cụ `label-studio` (mã nguồn mở, chạy cục bộ), đối chiếu với ảnh mẫu của lớp tương ứng trong IP102-VN và với tài liệu định danh của cơ quan bảo vệ thực vật [33][35].

> 3. **Xử lý bất đồng.** Ảnh có hai nhãn khác nhau được đưa vào phiếu rà chung của cả nhóm; nếu vẫn không thống nhất, ảnh được gán nhãn `khong_xac_dinh` và **loại khỏi tập đánh giá** thay vì đoán. Chỉ số đồng thuận Cohen's κ giữa hai người gán được báo cáo trong báo cáo — đây là thước đo trung thực về độ khó của bài toán định danh ngoài đồng.

> 4. **Chỉ giữ lớp có trong IP102-VN.** Ảnh của loài không thuộc 20 lớp của K20 được giữ riêng thành bộ `ngoai_tap` để phục vụ thí nghiệm cơ chế từ chối trả lời ở Mục 6.6, không trộn vào tập đánh giá chính.

### ***6.3. Phương pháp xử lý dữ liệu***

Quy trình xử lý dữ liệu gồm bảy bước làm sạch (C1–C7), thực hiện **trên toàn bộ 75.222 ảnh** trước khi trích tập con, để mọi ngưỡng đều được hiệu chuẩn trên phân bố đầy đủ:

> * **C1 — Kiểm tra toàn vẹn file:** giải mã toàn bộ ảnh, ghi nhận ảnh hỏng, ảnh grayscale/CMYK và ảnh có kênh alpha. Ảnh có vùng trong suốt được dán nền trắng trước khi chuyển sang RGB (chuyển trực tiếp sẽ biến vùng trong suốt thành màu đen).

> * **C2 — Loại ảnh quá nhỏ:** ngưỡng cạnh nhỏ nhất 64 pixel.

> * **C3 — Loại ảnh trùng bit tuyệt đối:** theo mã băm MD5.

> * **C4 — Khử ảnh gần trùng nội bộ:** dùng dHash 64-bit, xác minh chéo bằng sai số bình phương trung bình trên ảnh xám 32×32. **Ngưỡng được hiệu chuẩn chứ không lấy sẵn:** lấy mẫu 300 cặp ở mỗi mức khoảng cách Hamming, kiểm tra bằng mắt, chọn mức cuối cùng còn giữ độ chính xác ≥ 80%. Ngưỡng chốt: Hamming ≤ 2, hoặc Hamming ≤ 5 kèm sai số bình phương trung bình < 300.

> * **C5 — Khử rò rỉ giữa các tập:** đây là bước quan trọng nhất và cũng là đóng góp học thuật rẻ nhất của đề tài. Khảo sát trên 45 lớp ứng viên cho thấy **khoảng 14% ảnh của tập kiểm định và kiểm thử là bản gần trùng của ảnh huấn luyện**, có lớp lên tới trên 40%. Con số 14% là **kết quả đo của nhóm**, không phải trích dẫn: đo trên 45 lớp ứng viên sau bước C4, mẫu số là tổng số ảnh của tập kiểm định và kiểm thử thuộc 45 lớp đó, cùng ngưỡng gần trùng nêu ở C4, chạy ngày 22/8/2026 trong notebook `02_Quet_Toan_Bo.ipynb`. Bằng chứng hình ảnh — lưới ảnh huấn luyện đặt cạnh ảnh kiểm thử kèm khoảng cách Hamming từng cặp — được đưa vào phụ lục báo cáo. Mỗi lần trích dẫn con số này đều phải kèm phạm vi đo, vì con số đo trên phạm vi khác sẽ khác.

> * **C6 — Xử lý nhãn mâu thuẫn:** các nhóm ảnh gần trùng nhưng mang nhãn của hai lớp khác nhau được xuất ra phiếu rà tay có kèm ảnh mẫu của cả hai lớp; nhóm quyết định từng trường hợp và ghi lý do, không xóa tự động. Trên bản dựng thử 20 lớp trước đây, bước này loại **69 ảnh thuộc 34 nhóm mâu thuẫn**; con số tương ứng cho bản chốt K45 được đo lại và báo cáo ở Mục 3.2, vì phạm vi lớp đã khác.

> * **C7 — Chuẩn hóa bảng nhãn:** khóa bảng tên lớp theo chỉ số 0-based ngay từ đầu để tránh lỗi lệch một đơn vị giữa tệp `classes.txt` (đánh số từ 1) và thư mục lớp (đánh số từ 0) — lỗi phổ biến nhất khi dùng IP102 thô, khiến mô hình vẫn huấn luyện bình thường nhưng mọi diễn giải kết quả đều sai.

**Quy tắc chia tập — chia một lần trên K45 rồi lọc xuống.** Đề tài **không** dùng ranh giới train/test gốc của IP102, vì phép chia gốc chính là nơi chứa 14% rò rỉ đã đo được. Thay vào đó, dữ liệu được dựng theo bốn bước, và thứ tự của bốn bước này là điều kiện để thí nghiệm E4 đọc được:

> 1. **Dựng một manifest K45 duy nhất** theo đúng hạng 1–45 của bảng đã khóa ở Mục 6.2.2.

> 2. **Làm sạch, gom nhóm ảnh gần trùng và chia `train` / `val` / `test` một lần trên K45**, phân tầng theo lớp, tỷ lệ **60 / 15 / 25**, với **đơn vị chia là nhóm ảnh gần trùng chứ không phải từng ảnh** — nhờ vậy không có bản trùng nào bị tách đôi giữa hai split. Rò rỉ được chặn **do cấu trúc phép chia**, không phải được vá sau khi đã chia.

> 3. **Sinh K35, K20 và K15 bằng cách lọc manifest K45 theo hạng.** Không chia lại ảnh, không đổi nhãn, không dựng bốn tập độc lập.

> 4. **Mọi ảnh của một lớp có mặt ở nhiều mốc K phải giữ nguyên split, checksum và đường dẫn tương đối ở tất cả các mốc.**

Bước 3 và 4 là phần khác biệt so với phiên bản trước và là điều kiện để câu hỏi của E4 có nghĩa. Nếu bốn tập được chia độc lập thì một ảnh của `Rầy nâu hại lúa` có thể nằm ở `train` của K20 nhưng ở `test` của K45, và khi đó chênh lệch macro-F1 giữa hai mốc lẫn lộn hai nguyên nhân: số lớp thay đổi, và phép chia thay đổi. Chia một lần rồi lọc làm phép so sánh chỉ còn đúng một biến — **số lớp**.

| Thành phần | Giá trị | Hệ quả thống kê | Lý do lựa chọn |
|---|:---:|---|---|
| Tỷ lệ chia | 60 / 15 / 25 | Lớp nhỏ nhất (ID 96, 202 ảnh sạch) cho 121 / 30 / 50 | Tỷ lệ nhỏ nhất còn thỏa cả ba sàn dưới. Ở 70/15/15, lớp này chỉ được 30 ảnh test và không đạt sàn |
| Sàn tập huấn luyện | 100 ảnh/lớp | — | Dưới mức này, tinh chỉnh trên một lớp gần như chỉ ghi nhớ vài mẫu |
| Sàn tập kiểm định | 30 ảnh/lớp | — | Tập kiểm định chỉ dùng để chọn checkpoint, không cần độ chính xác như tập kiểm thử |
| Sàn tập kiểm thử | 50 ảnh/lớp | Khoảng tin cậy 95% của recall ≈ **± 11,1 điểm** (tại p = 0,80) | Mức trần mà bộ dữ liệu cho phép: sàn cao hơn buộc phải cắt bớt lớp khỏi K45. Với 30 ảnh/lớp, khoảng tin cậy nới tới ± 14,3 điểm và recall từng lớp gần như không so sánh được |
| Trần tập huấn luyện | 800 ảnh/lớp | 6 lớp chạm trần ở K15 và K20, 7 lớp ở K35, 8 lớp ở K45 | Chặn các lớp rất lớn (ID 70 có 5.028 ảnh sạch) áp đảo phần còn lại. Trần chỉ áp cho `train`; **không nhân bản ảnh ở `val` hoặc `test`** |
| Đơn vị chia | Nhóm ảnh gần trùng (Hamming ≤ 2) | Rò rỉ bằng 0 theo cấu trúc | Chia theo từng ảnh sẽ tách đôi các nhóm trùng và tái tạo lại đúng vấn đề đang muốn khử |

**Về khoảng tin cậy ± 11,1 điểm.** Đây là một đánh đổi phải nêu thẳng chứ không giấu. Sàn 50 ảnh test cho khoảng tin cậy rộng hơn sàn 80 ảnh của phiên bản trước (± 8,8 điểm), nên **recall của từng lớp riêng lẻ chỉ đọc được ở mức xu hướng**. Bù lại, ba chỉ số dùng để kết luận của đề tài — macro-F1, balanced accuracy và Accuracy trên `test_balanced` — đều là trung bình trên toàn bộ lớp, nên khoảng tin cậy của chúng hẹp hơn nhiều và được ước lượng trực tiếp bằng bootstrap ở Mục 6.6. Nói cách khác: kết luận **giữa các mô hình** vẫn vững, còn kết luận **về một lớp cụ thể** phải phát biểu thận trọng.

**Quy mô ước tính của bốn tập.** Các con số dưới đây tính từ số ảnh sạch hiện có; con số chốt sẽ lệch nhẹ vì đơn vị chia là nhóm ảnh gần trùng chứ không phải từng ảnh:

| Mốc | Số lớp | `train` | `val` | `test` | `test_balanced` |
|:---:|:---:|---:|---:|---:|---:|
| **K15** | 15 | ~8.162 | ~3.444 | ~5.748 | 750 |
| **K20** | 20 | ~10.182 | ~3.946 | ~6.589 | **1.000** |
| **K35** | 35 | ~14.770 | ~5.088 | ~8.499 | 1.750 |
| **K45** | 45 | ~18.084 | ~5.923 | ~9.893 | 2.250 |

**Tập kiểm thử cân bằng.** Ngoài ba tập trên, đề tài dựng thêm một tập **`test_balanced`** với **đúng 50 ảnh mỗi lớp**, lấy từ `test` chứ không sinh thêm ảnh mới — tổng **1.000 ảnh cho tập chính K20**. Con số 50 là mức tối đa mà lớp nhỏ nhất cho phép. Trên tập này, **Accuracy bằng đúng macro-recall**, nên mục tiêu ≥ 0,85 ở Mục 2.1 đọc được trực tiếp mà không bị lợi thế của các lớp nhiều mẫu che lấp.

**Xử lý mất cân bằng.** Trước khi áp trần, tỷ lệ mất cân bằng của cả bốn mốc K đều là **24,9 lần** (Mục 6.2.3) — đã thấp hơn nhiều so với 80,8 lần của IP102 đầy đủ, vì bảng hạng không đưa các lớp nhãn cấp họ rất lớn vào 45 hạng đầu. Sau khi áp trần huấn luyện 800 ảnh/lớp, tỷ lệ trên tập `train` giảm tiếp xuống **6,61 lần** (lớp nhỏ nhất 121 ảnh, lớn nhất 800 ảnh), với entropy chuẩn hóa 0,961 ở K20. Ở mức này, **bộ lấy mẫu có trọng số theo số hiệu dụng** (hệ số β = 0,999) đủ để cân bằng mà không lặp lớp đuôi tới mức quá khớp — và theo Mục 6.5.7, đây là **cơ chế duy nhất** được dùng, không kèm trọng số lớp trong hàm mất mát.

> **Ghi chú về trạng thái thực hiện.** Đề cương này mô tả quy tắc chốt của đề tài. Trạng thái thực tế tính đến ngày lập đề cương:
>
> * **Bản v1** (ba tập con k20/k35/k45 theo ngưỡng ban đầu, đã dựng 23/8/2026) và **bản v2** (`ip102vn_k20_v2`, đã dựng 24/8/2026, 20 lớp · 21.013 ảnh, chia 70/15/15) được dựng theo quy tắc chọn lớp **hai tầng đã bị thay thế**. Hai bản này vẫn được lưu để đối chiếu quy trình và để chứng minh đường ống làm sạch C1–C7 cùng bộ kiểm tra chấp nhận đã chạy được đầu-cuối, **nhưng không còn là dữ liệu dùng cho bất kỳ thí nghiệm nào của đề cương này**.
> * **Bản chốt** — dựng từ bảng hạng đã khóa ở Mục 6.2.2 theo quy tắc chia 60/15/25 nêu trên: một manifest K45 duy nhất, ba tập K35/K20/K15 lọc từ đó. Đây là bản mà toàn bộ chương thực nghiệm sử dụng. Việc dựng bản chốt nằm trong công việc Tuần 3–4 của phần Tiến độ.
> * Vì bảng điểm ở Mục 6.2.3 vẫn đang ở trạng thái **đề xuất trước buổi chấm chung**, thành phần cụ thể của bốn tập K có thể còn thay đổi cho tới khi nhóm khóa phiên bản. Mọi con số quy mô trong Mục 6.3 vì thế được ghi là **ước tính**.

**Kiểm tra chấp nhận.** Trước khi đưa vào huấn luyện, bản dựng phải vượt bộ kiểm tra gồm **12 phép**, chia thành ba nhóm:

> * **Về bảng hạng (1–4):** có đủ 102 hạng duy nhất, không thiếu IP102 ID nào từ 0 đến 101 · điểm giảm dần theo hạng và mọi trường hợp bằng điểm tuân thủ ba khóa phá hòa · K15/K20/K35/K45 có đúng 15/20/35/45 lớp và là bốn tiền tố của **cùng một** bảng hạng · K45 có 45 IP102 ID duy nhất, mỗi lớp ít nhất 200 ảnh sạch.

> * **Về dữ liệu đã chia (5–8):** mỗi lớp đạt cả ba sàn 100 train / 30 val / 50 test · không checksum hay nhóm ảnh gần trùng nào đi qua hai split · không trùng lặp nội bộ trong từng split · mọi lớp chung giữ nguyên split, checksum và đường dẫn tương đối ở cả bốn mốc K.

> * **Về tính tái lập và truy vết (9–12):** manifest lưu đủ bốn trường nhãn ở Mục 6.2.4 · thứ tự nhãn khớp bảng lớp và toàn vẹn theo mã băm SHA-256 · tái lập được từ cùng hạt giống và tự chứa khi giải nén ở môi trường khác · `build_info.json` ghi seed, **phiên bản bảng hạng** và checksum nguồn.

Nếu một phép kiểm tra không đạt, nhóm sửa dữ liệu hoặc chấm lại tiêu chí `D` rồi tạo **phiên bản bảng hạng mới và xếp lại toàn bảng** — không thay lớp dựa trên kết quả đã quan sát, theo đúng ranh giới ở Mục 6.2.2.

**Tăng cường dữ liệu và chuẩn hóa đầu vào.** Ảnh được đưa về 224×224 (độ rộng trung vị của IP102 chỉ khoảng 439 pixel nên nâng lên 384 hay 480 chủ yếu là nội suy). Tăng cường khi huấn luyện gồm `RandomResizedCrop`, lật ngang ngẫu nhiên, `RandAugment` và `RandomErasing`; khi đánh giá chỉ `Resize` + `CenterCrop`. Mọi kiến trúc dùng **cùng một bộ tăng cường** để bảo đảm so sánh công bằng.

### ***6.4. Phương pháp hiểu dữ liệu (EDA)***

Bước hiểu dữ liệu được tách thành một pha riêng và thực hiện **trước** mọi thí nghiệm huấn luyện, vì với bài toán này phần lớn giới hạn hiệu năng nằm ở dữ liệu chứ không ở mô hình. Cụ thể sẽ phân tích:

> * **Phân bố lớp và mức độ mất cân bằng.** Biểu đồ số ảnh theo lớp ở thang logarit cho IP102 đầy đủ và cho bốn tập con K15/K20/K35/K45; đường cong Lorenz và hệ số Gini để định lượng mức bất cân bằng; bảng phân nhóm Head / Medium / Tail làm cơ sở cho việc báo cáo độ chính xác tách theo nhóm ở chương thực nghiệm.

> * **Phân bố theo nhóm cây trồng.** Thống kê số lớp và số ảnh theo tám nhóm cây trồng của IP102, đối chiếu với cơ cấu cây trồng thực tế của Việt Nam để định lượng phần dữ liệu không có giá trị sử dụng trong nước, và dựng ma trận loài × cây trồng làm đầu vào cho nhánh PestID-CropGate.

> * **Kiểm tra chất lượng ảnh.** Quét toàn bộ 75.222 ảnh trong một lượt để tính: mã băm MD5, dHash 64-bit, ảnh thu nhỏ 32×32, phương sai Laplacian sau khi chuẩn hóa cạnh ngắn về 256 pixel, và siêu dữ liệu kích thước/chế độ màu. Từ đó thống kê ảnh hỏng, ảnh grayscale/CMYK/RGBA, ảnh dưới 64 pixel, ảnh mờ, phân bố chiều rộng và chiều cao, dung lượng trung bình. Kết quả quét được lưu vào bộ đệm để các bước sau dùng lại mà không phải đọc đĩa lần nữa.

> * **Đo trùng lặp và rò rỉ có xác minh chéo.** Lập bảng tỷ lệ dương tính đúng theo từng mức khoảng cách Hamming (kiểm chứng bằng mắt trên 300 cặp mỗi mức) để chọn ngưỡng có căn cứ; đo tỷ lệ rò rỉ theo từng lớp và trực quan hóa bằng **lưới ảnh huấn luyện ‖ ảnh kiểm thử đặt cạnh nhau**, mỗi cặp ghi rõ khoảng cách Hamming và sai số bình phương trung bình. Đây là bằng chứng thuyết phục nhất cho phần đóng góp về dữ liệu của đề tài, vì hội đồng khó tin con số "14% rò rỉ" nếu không nhìn thấy ảnh.

> * **Hiệu chuẩn ngưỡng độ nét.** Chia thang phương sai Laplacian thành 8 dải theo phân vị, lấy ngẫu nhiên 25 ảnh mỗi dải (200 ảnh), trộn ngẫu nhiên và giấu điểm số, hai người chấm độc lập theo ba mức *rõ / tạm được / mờ không dùng được*, chọn ngưỡng ở dải mà tỷ lệ bị chấm mờ vượt 50%. **Không lấy ngưỡng có sẵn từ tài liệu khác**, vì các con số thường gặp được hiệu chuẩn cho ảnh chưa chuẩn hóa kích thước.

> * **Phân tích không gian đặc trưng bằng embedding.** Trích vector đặc trưng của toàn bộ ảnh tập con bằng mô hình nền tự giám sát — chi phí khoảng 5 phút GPU cho tập K45 — rồi:
>   - **Trực quan hóa t-SNE / UMAP** không gian đặc trưng theo lớp, để nhìn thấy trực tiếp lớp nào tách bạch, lớp nào chồng lấn, và cụm nào thực chất là hai quần thể ảnh khác nhau bị gộp một nhãn (dấu hiệu của vấn đề giai đoạn vòng đời).
>   - **Dò nhãn nghi sai** bằng giao của hai tín hiệu độc lập: k láng giềng gần nhất (k = 20) và học tự tin (confident learning) huấn luyện chéo 5-fold. Chỉ những ảnh mà **cả hai** tín hiệu cùng nghi ngờ mới đưa vào phiếu rà tay — cách này giảm khối lượng rà tay khoảng bảy lần so với dùng một tín hiệu đơn lẻ.
>   - **Đo độ tinh khiết k-NN và biên với đối thủ mạnh nhất** cho từng lớp, nhằm lập trước danh sách các cặp lớp chồng lấn nặng và ước lượng độ khó của bài toán. Hai chỉ số này là **thông tin mô tả, không phải tiêu chí chọn lớp**: theo ranh giới ở Mục 6.2.2, chúng không được phép xuất hiện trong bảng điểm hay dùng để điều chỉnh thứ hạng. Chúng chỉ trả lời hai câu hỏi khác: cặp nào cần soi riêng ở phần phân tích lỗi, và cặp nào có thành phần biên gần 0 — tức bị đúng một đối thủ nuốt trọn, trường hợp mà **gộp lớp** là lời giải đúng hơn về cả mặt nông học lẫn kỹ thuật vì nông dân xử lý hai loài đó giống nhau. Mọi phép đo này chỉ chạy trên khối phát triển.
>   - **Dò ảnh lạc trong lớp** bằng khoảng cách tới trọng tâm lớp, nhằm xác định tỷ lệ ảnh thực chất là **triệu chứng gây hại trên lá và quả** chứ không phải ảnh con vật. Tỷ lệ này quyết định mô hình đang học nhận diện *côn trùng* hay nhận diện *vết hại* — một thông tin bắt buộc phải nêu trong báo cáo.
>   - **Chạy linear probe** (đóng băng mô hình nền, chỉ khớp một bộ phân loại tuyến tính) để ước lượng **trần trên của tập dữ liệu** trước khi tiêu tốn giờ GPU cho việc tinh chỉnh. Kết quả probe dưới 70% là tín hiệu phải quay lại làm sạch nhãn; từ 70–85% là mức bình thường của bài toán phân loại mịn; trên 85% cho biết mục tiêu của đề cương khả thi. **Kết quả probe không được dùng để đổi thứ hạng lớp hay thay lớp trong một tập K** — nó chỉ dẫn tới việc làm sạch lại dữ liệu, hoặc tới việc điều chỉnh mục tiêu có kèm số liệu chứng minh theo bảng rủi ro ở phần Tiến độ.

> * **Khảo sát các cặp lớp dễ nhầm.** Từ tri thức côn trùng học và từ ma trận nhầm lẫn của linear probe, lập trước danh sách các cặp loài dự báo sẽ nhầm lẫn nặng, để chương thực nghiệm soi riêng thay vì chỉ báo cáo một ma trận nhầm lẫn tổng khó đọc. Kết quả sơ bộ đã xác định được cặp nặng nhất: `Rầy nâu` ↔ `Rầy lưng trắng` với 392 ảnh lẫn giữa hai lớp.

### ***6.5. Phương pháp xây dựng mô hình***

#### **6.5.1. Danh mục thí nghiệm**

Toàn bộ chương trình thực nghiệm gồm chín thí nghiệm có đánh số; mỗi thí nghiệm gắn với một câu hỏi nghiên cứu ở Mục 3 và một chỉ số dùng để kết luận ở Mục 6.6.

| Mã | Thí nghiệm | Trả lời | Chỉ số kết luận | Ước tính GPU T4 |
|:---:|---|:---:|---|:---:|
| **E1** | Đặc tính tập con: IP102-VN-K20 so với bộ đối chứng 20 lớp chọn theo số ảnh nhiều nhất | RQ2 | Hệ số Gini, tỷ lệ mất cân bằng, F1 linear probe | ~15 phút |
| **E2** | Linear probe trên ba mô hình nền đóng băng — xác định trần trên của dữ liệu | RQ3 | Accuracy, macro-F1 | ~25 phút |
| **E3** | **Bảy kiến trúc nền** trên K20 (cùng dữ liệu, cùng tăng cường, cùng siêu tham số) | RQ3 | macro-F1, tham số, FLOPs, độ trễ | ~2,2 giờ |
| **E4** | Khảo sát ảnh hưởng số lớp: ba kiến trúc đại diện trên K = 15 / 20 / 35 / 45 | RQ3 | macro-F1 theo K | ~2,7 giờ |
| **E5** | PestID-KD + nghiên cứu loại trừ ba mức | RQ4 | Δ macro-F1 ở cùng số tham số | ~1,5 giờ |
| **E6** | Đo mức thổi phồng do rò rỉ: phép chia theo nhóm gần trùng so với phép chia gốc IP102 | RQ1 | Hiệu số Accuracy | ~10 phút |
| **E7** | Lượng tử hóa: PTQ so với QAT, đo trên điện thoại thật | RQ5 | MB, ms/ảnh, Δ Accuracy | ~30 phút |
| **E8** | Đánh giá trên bộ 100–200 ảnh thực địa Việt Nam | RQ6 | Mức sụt macro-F1 | ~5 phút |
| **E9** | Thí nghiệm kiểm chứng nhánh PestID-CropGate *(chỉ chạy nếu quỹ thời gian cho phép)* | — | Δ macro-F1 có/không điều kiện cây trồng | ~20 phút |

Cột chi phí ghi theo **giờ GPU**. Vì hai thí nghiệm chạy song song trên hai GPU T4, thời gian thực tế trôi qua xấp xỉ một nửa con số này.

Đề tài tổ chức các mô hình thành **năm nhóm theo thứ tự chi phí tăng dần**, mỗi nhóm trả lời một câu hỏi cụ thể. Nguyên tắc xuyên suốt: mọi mô hình dùng chung một tập dữ liệu, một phép chia, một bộ tăng cường và một hạt giống ngẫu nhiên.

#### **6.5.2. Nhóm M-A — Bảy kiến trúc nền**

Nhóm M-A gồm **bảy kiến trúc**, chia theo hai vai trò. **Ba kiến trúc đầu là bắt buộc theo yêu cầu đề tài**, đồng thời là ba mốc tham chiếu mà phần lớn công bố trên IP102 sử dụng. **Bốn kiến trúc sau** trả lời một câu hỏi mà ba kiến trúc đầu không trả lời được: chúng lần lượt công bố năm 2018, 2019 và 2021 — vậy tới năm 2026, slot "mạng nhẹ chạy được trên điện thoại" đã tiến bộ tới đâu cho bài toán phân loại côn trùng gây hại?

Cả bảy dùng chung một tập dữ liệu, một phép chia, một bộ tăng cường, một bộ siêu tham số và một hạt giống ngẫu nhiên, nên bảng kết quả so sánh được theo cả trục kiến trúc lẫn trục thời gian. Cả bảy đều nằm trong ngân sách 3–9 triệu tham số và dưới 2 GMACs ở độ phân giải 224×224, đều có trọng số huấn luyện trước tải được bằng một dòng lệnh, và đều không cần biên dịch nhân CUDA riêng — điều kiện bắt buộc để chạy được trong hạn mức Kaggle.

| # | Mô hình | Nguồn | Tham số | Top-1 ImageNet | Vai trò trong đề tài |
|:-:|---|---|:---:|:---:|---|
| 1 | **MobileNetV2** | CVPR 2018 [2] | ~3,4 M | ~72,0% | Mốc kiến trúc nhẹ và ứng viên chính cho bản triển khai di động; cũng chính là kiến trúc mà PestNet [8] cải tiến để đạt 87,62% trên Pest37 |
| 2 | **EfficientNet-B0** | ICML 2019 [3] | ~5,3 M | 77,1% | Mốc so sánh với bên ngoài — phần lớn công bố trên IP102 đều dùng kiến trúc này |
| 3 | **ViT-Tiny / DeiT-Tiny** | ICLR 2021 [4] · ICML 2021 [5] | ~5,7 M | 72,2% · 74,5% có chưng cất | Mốc kiến trúc Transformer, kiểm chứng nhận định "khi dữ liệu ít, mạng tích chập vượt Vision Transformer" [13] |
| 4 | **MobileNetV4-Conv-S** | ECCV 2024 [36] | ~3,8 M | 73,8% | Mạng tích chập nhẹ thế hệ mới, cùng ngân sách tham số với MobileNetV2 |
| 5 | **TinyViT-5M** | ECCV 2022 [37] | ~5,4 M | **80,7%** | Transformer cùng hạng tham số với ViT-Tiny nhưng huấn luyện trước bằng chưng cất |
| 6 | **FastViT-T12** | ICCV 2023 [38] | ~6,8 M | 79,1% | Kiến trúc lai tích chập – Transformer có tái tham số hóa cấu trúc |
| 7 | **iFormer-M** | ICLR 2025 [39] | ~8,9 M | 80,4% | Kiến trúc lai mới nhất, tối ưu trực tiếp theo độ trễ trên thiết bị di động |

**Lý do đưa từng kiến trúc bổ sung vào.**

> * **MobileNetV4-Conv-S** là hậu duệ chính thống của MobileNetV2 sau sáu năm. Điểm khác biệt quan trọng không nằm ở độ chính xác ImageNet mà ở cách nó được thiết kế: khối UIB và Mobile MQA được tìm bằng tìm kiếm kiến trúc tự động **có ràng buộc độ trễ đo trên phần cứng di động thật** (CPU, GPU, DSP, NPU của nhiều dòng máy), thay vì tối ưu theo FLOPs như thế hệ trước. Điều này ăn khớp trực tiếp với nguyên tắc ở Mục 6.6 rằng *FLOPs thấp không đảm bảo chạy nhanh, bắt buộc phải đo thật*. Cặp MobileNetV2 ↔ MobileNetV4 vì thế là phép so sánh sạch nhất để kiểm chứng nguyên tắc đó: cùng họ kiến trúc, cùng ngân sách tham số, khác nhau ở chỗ một bên tối ưu FLOPs còn một bên tối ưu độ trễ thật.

> * **TinyViT-5M** tách bạch được một câu hỏi mà ViT-Tiny một mình không tách được. Mục 5.1 dẫn nhận định rằng khi dữ liệu tinh chỉnh hạn chế thì mạng tích chập thường vượt Vision Transformer [13], và ViT-Tiny có mặt chính là để kiểm chứng nhận định đó. Nhưng nếu ViT-Tiny thua, thua **vì kiến trúc Transformer không hợp với dữ liệu ít, hay vì công thức huấn luyện trước của nó yếu?** TinyViT-5M cùng hạng tham số, cùng họ Transformer, chỉ khác ở chỗ được huấn luyện trước bằng chưng cất trên ImageNet-21k — nên phép so sánh trở thành có kiểm soát và trả lời được câu hỏi. Nó còn trùng chủ đề với đóng góp trung tâm của đề tài: PestID-KD là chưng cất ở *tầng tinh chỉnh*, TinyViT là chưng cất ở *tầng huấn luyện trước*; hai tầng bổ sung nhau và có thể dùng TinyViT-5M làm khởi tạo cho student mà **không tăng số tham số**.

> * **FastViT-T12** lấp một chỗ trống trong chính phần tổng quan của đề tài: Mục 5.1 dẫn ba công trình theo hướng lai tích chập – Transformer — Pest-ConFormer [19], khung CTF [20] và ConvViT [28] — nhưng không kiến trúc lai nào có mặt trong nhóm so sánh. FastViT-T12 là kiến trúc lai thật (ba tầng đầu dùng RepMixer tích chập, tầng cuối dùng self-attention) ở đúng cỡ chạy được trên điện thoại. Điểm quyết định cho sản phẩm triển khai là **tái tham số hóa cấu trúc**: lúc huấn luyện, RepMixer có nhiều nhánh song song để tăng năng lực biểu diễn; lúc suy luận, các nhánh này gộp lại thành một lớp tích chập depthwise duy nhất, cho mô hình xuất ra gọn, ít toán tử và độ trễ ổn định.

> * **iFormer-M** là mốc kiến trúc mới nhất trong bảng. Nhóm tác giả báo cáo 80,4% top-1 ImageNet với 1,64 GMACs và **1,10 ms trên iPhone 13**, đạt được mà không cần chưng cất lẫn tái tham số hóa; so với MobileNetV4-Conv-M ở độ trễ tương đương thì cao hơn 0,5 điểm, và nhanh hơn FastViT-SA12 khoảng 1,4 lần. Đưa iFormer-M vào cho đề tài một điểm neo ở biên hiện tại của hướng thiết kế mạng nhẹ, để kết luận về ba kiến trúc bắt buộc được đặt trong đúng bối cảnh năm 2026. **Lưu ý về nguồn trọng số:** khác với sáu kiến trúc còn lại (đều có trong thư viện `timm`), trọng số iFormer-M được phân phối qua Hugging Face Hub từ kho mã của nhóm tác giả. Đây là rủi ro tích hợp nhỏ và đã được đưa vào bảng rủi ro ở phần tiến độ; nếu việc nạp trọng số không thành công trong tuần 6 thì iFormer-M là kiến trúc đầu tiên bị loại khỏi bảng so sánh, sáu kiến trúc còn lại không bị ảnh hưởng.

**Cấu hình học chuyển giao.** Cả bảy khởi tạo từ trọng số huấn luyện trước rồi tinh chỉnh theo tầng. Bốn kiến trúc tích chập và lai (1, 2, 4, 6) dùng learning rate đầu phân loại 1e-3 và backbone 1e-4. Ba kiến trúc có thành phần self-attention chiếm tỷ trọng lớn (3, 5, 7) hạ learning rate backbone thêm một bậc và dùng tăng cường mạnh hơn, theo đúng công thức huấn luyện hiệu quả dữ liệu của DeiT [5].

Mô hình đầu tiên được huấn luyện là **EfficientNet-B0 trên tập K20** — không phải vì nó mạnh nhất, mà vì một lượt huấn luyện chỉ mất khoảng 15 phút GPU, đủ để kiểm chứng toàn bộ đường ống dữ liệu (thứ tự nhãn, bộ lấy mẫu, hàm mất mát) trước khi đầu tư giờ GPU cho các thí nghiệm dài hơn.

#### **6.5.3. Nhóm M-B — Mốc trần trên bằng mô hình nền**

Chạy linear probe với ba mô hình nền đóng băng để biết **giới hạn trên mà dữ liệu cho phép**. Đây là thí nghiệm có giá trị trên mỗi phút GPU cao nhất trong toàn bộ đề tài: nó cho biết trước liệu mục tiêu 0,85 có khả thi hay không, và ma trận nhầm lẫn của nó chỉ ra các cặp lớp chồng lấn trước khi tiêu tốn một giờ GPU nào. Ba ứng viên đại diện ba mức độ chuyên ngành khác nhau:

| Mô hình nền | Mức chuyên ngành | Căn cứ đưa vào |
|---|---|---|
| **DINOv2** [32] | Tổng quát — ảnh Internet nói chung | Mô hình nền tự giám sát được dùng rộng rãi làm chuẩn tham chiếu |
| **BioCLIP 2** [40] | Sinh vật — 214 triệu ảnh, 952.000 đơn vị phân loại, có côn trùng | Nhóm tác giả báo cáo vượt DINOv2 khoảng 10 điểm trên các tác vụ nhận dạng sinh vật; trọng số công khai, chi phí thử gần bằng không |
| **Insect-Foundation** [31] | Côn trùng học — 1 triệu ảnh chuyên ngành | Mô hình nền gần bài toán nhất về mặt miền dữ liệu |

Phép so sánh ba mô hình nền ở ba mức chuyên ngành (tổng quát → sinh vật → côn trùng) trên cùng một tập con là một kết quả phụ có giá trị tham chiếu cho các nghiên cứu sau, và trong phạm vi rà soát của nhóm chưa công trình nào trên IP102 thực hiện.

#### **6.5.4. Nhóm M-C — Mô hình đề xuất chính: PestID-KD (đóng góp mới của nhóm)**

Đây là phương án cải tiến trung tâm của đề tài, hiện thực hóa đúng điều mà Qian và cộng sự (2025) đề xuất trong phần hạn chế nhưng chưa thực hiện [18].

> * **Teacher — chọn theo tiêu chí định lượng, không chọn trước.** Ba ứng viên của nhóm M-B được đánh giá ở thí nghiệm E2 và ứng viên nào cho macro-F1 linear probe cao nhất trên tập kiểm định sẽ được chọn. Ưu tiên các phương án **đóng băng** vì teacher khi đó **không cần huấn luyện** — chỉ chạy suy luận một lượt để lưu phân phối mềm, chi phí khoảng 20 phút GPU T4 cho toàn bộ tập K45. Chỉ khi cả ba mô hình nền đều không vượt student baseline 5 điểm thì mới chuyển sang tinh chỉnh một mạng cỡ vừa (ConvNeXt-Small hoặc Swin-Tiny) làm teacher, và khi đó chọn mạng nhỏ nhất đạt yêu cầu để giữ ngân sách GPU trong hạn mức Kaggle. Lưu ý: E2 chạy **sau** khi bảng hạng đã khóa, nên kết quả của nó chỉ dùng để chọn teacher và ước lượng trần trên, không quay ngược lại tác động tới thành phần lớp.

> * **Student:** một mạng nhẹ (MobileNetV2 hoặc EfficientNet-B0) — **giữ nguyên số tham số**, học đồng thời từ nhãn cứng và từ phân phối mềm của teacher.

> * **Cơ chế chưng cất:** dùng token chưng cất theo DeiT [5] khi student là ViT, và chưng cất trên logits có hệ số nhiệt độ khi student là CNN. Hàm mất mát tổng hợp: `L = α · L_CE(nhãn cứng) + (1−α) · T² · L_KL(phân phối mềm)`.

> * **Kết hợp thêm:** tăng cường dữ liệu nâng cao **CutMix và MixUp** (căn cứ: [7] cho thấy chiến lược tăng cường quan trọng hơn kích thước mô hình; [26] đạt 76,44% chỉ bằng chiến lược huấn luyện), cùng **bộ lấy mẫu có trọng số theo số hiệu dụng** (β = 0,999) để xử lý phân bố đuôi dài.

> * **Nghiên cứu loại trừ bắt buộc (ba mức):** ① student huấn luyện thuần → ② student + chưng cất → ③ student + chưng cất + tăng cường nâng cao. Chỉ có bảng ablation này mới tách bạch được đóng góp của từng thành phần.

> * **Cam kết trung thực:** đề tài **không cam kết một con số cải thiện cụ thể**. Bằng chứng gần nhất là chính bài DeiT: cùng kiến trúc DeiT-Tiny, thêm token chưng cất nâng top-1 ImageNet từ 72,2% lên 74,5%, tức khoảng 2,3 điểm [5]; các công trình chưng cất cho bài toán sâu bệnh hại ghi nhận mức tương tự [22]. Cam kết của nhóm là *báo cáo trung thực mức cải thiện đo được, dù lớn hay nhỏ, kèm ablation tách bạch*.

> * **Tiêu chí dừng đặt trước:** nếu linear probe của teacher không vượt student baseline ít nhất 5 điểm, chuyển sang dùng ensemble các kiến trúc nền mạnh nhất làm teacher, hoặc dừng nhánh này và báo cáo kết quả âm tính kèm phân tích nguyên nhân. Đặt tiêu chí dừng **trước khi bắt đầu** là cách rẻ nhất để tránh mất cả tháng cho một hướng không hiệu quả.

#### **6.5.5. Nhóm M-D — Nhánh mở rộng: PestID-CropGate (thực hiện nếu quỹ thời gian cho phép)**

**PestID-CropGate** — trong phiếu mô tả đề tài ban đầu ghi là *PestID-CTX*; cùng một nhánh, đổi tên để tên gọi mô tả đúng cơ chế mặt nạ logits theo cây trồng.

Bổ sung thông tin cây trồng làm điều kiện đầu vào: người dùng chọn cây trồng, mô hình cộng một vector mặt nạ vào logits trước softmax để loại các lớp không hợp lệ với cây đó. Chi phí cài đặt gần bằng không, và đây là ý tưởng được nêu trong phần hướng phát triển của [18] nhưng chưa ai thực hiện.

**Rủi ro phải kiểm soát:** nếu nhãn cây trồng được suy ra từ nhãn loài thì bài toán tự rò rỉ đáp án và độ chính xác tăng giả tạo — đây chính là điểm hội đồng sẽ chất vấn. Vì vậy nhóm phải chạy trước thí nghiệm kiểm chứng **E9** (khoảng 2 ngày): thống kê mỗi nhóm cây trồng còn lại bao nhiêu lớp và bao nhiêu phần trăm lớp thuộc nhiều nhóm cây; nếu trên 50% số lớp thuộc từ ba nhóm cây trở lên thì bỏ phần mặt nạ. Kết quả luôn báo cáo song song hai con số có và không có điều kiện cây trồng, kèm giải thích rõ nguồn gốc nhãn cây trồng.

#### **6.5.6. Nhóm M-E — Mô hình triển khai**

Lấy mô hình tốt nhất, áp lượng tử hóa số nguyên 8 bit (thử cả lượng tử hóa sau huấn luyện PTQ và huấn luyện nhận biết lượng tử hóa QAT), xuất sang **TensorFlow Lite** theo đường PyTorch → ONNX → TFLite. Căn cứ: Akhtar và cộng sự (2025) ghi nhận QAT giảm 75,59% kích thước mô hình và MobileNetV2 cho tỷ lệ hiệu năng/kích thước tốt nhất trong 18 mô hình khảo sát [14]. Chỉ tiêu: kích thước **≤ 15 MB**, độ trễ **≤ 500 ms/ảnh** trên điện thoại thật.

> **Kiểm chứng sớm bắt buộc.** Bước chuyển ONNX → TFLite phải được thử ngay ở tuần 6 với một mô hình bất kỳ, không để tới tuần 13. Lỗi toán tử không hỗ trợ là rủi ro đã biết của các kiến trúc có self-attention, và phát hiện muộn thì không còn thời gian đổi phương án.

#### **6.5.7. Phương pháp tối ưu huấn luyện (áp dụng chung cho mọi mô hình)**

| Hạng mục | Cấu hình | Lý do |
|---|---|---|
| Bộ tối ưu | AdamW, weight decay 0,05 | Chuẩn cho cả CNN lẫn Transformer trong các công bố tham chiếu [8][18] |
| Learning rate | Phân biệt theo tầng: đầu phân loại 1e-3, backbone 1e-4 (ViT-Tiny hạ thêm một bậc) | Backbone đã mang tri thức ImageNet, không nên phá bằng learning rate lớn |
| Lịch learning rate | Cosine annealing, warmup 3 epoch | Ổn định giai đoạn đầu khi đầu phân loại còn khởi tạo ngẫu nhiên |
| Hàm mất mát | CrossEntropy + label smoothing 0,1 | Giảm quá tự tin — đặc biệt cần với các cặp lớp chồng lấn |
| Xử lý mất cân bằng | `WeightedRandomSampler` theo số hiệu dụng, β = 0,999 | **Chỉ dùng một cơ chế.** Dùng đồng thời bộ lấy mẫu cân bằng và trọng số lớp trong hàm mất mát là sửa hai lần, khiến lớp đuôi bị khuếch đại quá mức |
| Điều kiện dừng | Early stopping theo macro-F1 trên tập kiểm định, patience 8, tối đa 30 epoch | Chọn checkpoint theo trung bình trượt 3 epoch thay vì theo một epoch tốt nhất, để giảm ảnh hưởng của nhiễu giữa các epoch |
| Gradient clipping | max_norm = 1,0 | Ổn định huấn luyện ViT-Tiny |
| Độ chính xác số | AMP fp16 | Rút ngắn thời gian trong hạn mức Kaggle. **Không dùng bf16** vì GPU T4 kiến trúc Turing không hỗ trợ |
| Khai thác hai GPU | Hai thí nghiệm độc lập chạy song song, mỗi GPU một thí nghiệm (`CUDA_VISIBLE_DEVICES`) | **Không dùng DistributedDataParallel**: mô hình nhỏ, batch 64 vừa một T4 16 GB; DDP chỉ cho ~1,7× tốc độ mà thêm phức tạp. Hạn mức tính theo thời gian phiên nên cách này gấp đôi thông lượng |
| Checkpoint | Lưu sau mỗi epoch kèm trạng thái bộ tối ưu và bộ lập lịch | Phiên Kaggle tối đa ~12 giờ và có thể ngắt sớm; phải chạy tiếp được từ điểm ngắt |
| Tính tái lập | Cố định hạt giống 42, ghi lại toàn bộ cấu hình cùng checkpoint và mã commit | Điều kiện để hội đồng và khóa sau tái lập được kết quả |

**Ràng buộc ngân sách tính toán.** Toàn bộ chương trình thực nghiệm chính (E1–E8) được ước tính khoảng **8 giờ GPU T4**, tương đương khoảng **4 giờ thực tế** khi chạy hai thí nghiệm song song trên hai GPU. Con số này chiếm khoảng một phần tư hạn mức tuần của một tài khoản Kaggle, nên toàn bộ chương trình thực nghiệm gói gọn trong một tuần hạn mức, còn dư nhiều cho việc chạy lại và thử nghiệm; nếu chia việc cho 4–5 thành viên trên các tài khoản khác nhau thì quỹ tính toán không phải là nút thắt của đề tài. Các phương án đòi hỏi thư viện cần biên dịch nhân CUDA riêng (ví dụ hướng State Space Model [21]) được đưa vào chương tổng quan nhưng **không triển khai**, vì thường lỗi trên môi trường notebook miễn phí.

### ***6.6. Phương pháp đánh giá***

Với tỷ lệ mất cân bằng còn khoảng 2,8 lần sau xử lý, Accuracy trên tập kiểm thử thường vẫn đọc được, nhưng để an toàn và để so sánh được với các công bố trên tập mất cân bằng nặng hơn, đề tài bắt buộc báo cáo đủ bộ chỉ số sau cho **mỗi** mô hình:

| Chỉ số | Đo trên tập | Vai trò |
|---|---|---|
| **macro-F1** | test | **Chỉ số chính để kết luận** — mỗi lớp đóng góp như nhau |
| **Balanced accuracy** | test | Đối chiếu chéo với macro-F1 |
| **Accuracy** | test_balanced | Trên tập cân bằng tuyệt đối (50 ảnh/lớp, tổng 1.000 ảnh cho K20), Accuracy bằng đúng macro-recall nên **đọc thẳng được đối chiếu với mục tiêu ≥ 0,85** |
| **Accuracy tách theo Head / Medium / Tail** | test | Theo cách làm của [18] — phần mà đa số nghiên cứu bỏ qua |
| **Recall từng lớp** | test | Phát hiện lớp bị bỏ rơi, đặc biệt các lớp sát sàn số ảnh. Với sàn 50 ảnh test, khoảng tin cậy 95% của một lớp là ± 11,1 điểm (Mục 6.3) nên chỉ số này đọc ở mức xu hướng, không dùng để tuyên bố chênh lệch giữa hai lớp |
| **Ma trận nhầm lẫn** | test_balanced | Soi riêng các cặp loài dễ nhầm đã lập danh sách ở bước EDA |
| **Số tham số, FLOPs** | — | Chi phí mô hình |
| **Độ trễ suy luận** | GPU T4 **và** điện thoại thật (ghi rõ model máy) | FLOPs thấp không đảm bảo chạy nhanh — bắt buộc đo thật |
| **Kích thước mô hình sau lượng tử hóa** | — | Đối chiếu chỉ tiêu ≤ 15 MB |

**Kiểm định ý nghĩa của chênh lệch.** Mọi chênh lệch macro-F1 giữa hai mô hình đều được báo cáo kèm **khoảng tin cậy 95% ước lượng bằng bootstrap 1.000 lần lấy mẫu lại trên tập kiểm thử**. Khi hai khoảng tin cậy chồng lấn, báo cáo kết luận *"không phân biệt được"* thay vì tuyên bố mô hình nào tốt hơn. Với cặp mô hình quan trọng nhất — student thuần so với PestID-KD — bổ sung **kiểm định McNemar** trên các dự đoán từng ảnh, vì hai mô hình được đánh giá trên cùng tập kiểm thử nên các mẫu không độc lập. Chi phí tính toán của cả hai phép này gần bằng không, vì chúng chạy trên tệp dự đoán đã lưu chứ không cần chạy lại mô hình.

**Bốn phép đánh giá bổ sung mang tính đóng góp riêng của đề tài:**

> 1. **Đo mức thổi phồng do rò rỉ dữ liệu.** Huấn luyện cùng một mô hình rồi đo độ chính xác trên hai phép chia: phép chia theo nhóm ảnh gần trùng của đề tài và phép chia gốc của IP102. **Hiệu số giữa hai con số chính là phần độ chính xác đến từ việc ghi nhớ ảnh chứ không phải học đặc trưng.** Đây là kết quả rẻ nhất mà đáng công bố nhất trong toàn bộ danh sách thí nghiệm.

> 2. **Đo khoảng cách miền giữa ảnh Internet và ảnh thực địa.** Đánh giá mô hình tốt nhất trên bộ 100–200 ảnh tự chụp tại Việt Nam và báo cáo mức sụt độ chính xác. Không đặt ngưỡng cam kết, vì chưa có nghiên cứu nào trong phạm vi rà soát công bố con số này để làm mốc.

> 3. **Đánh giá cơ chế từ chối trả lời.** Dùng bộ ảnh `ngoai_tap` (ảnh thực địa của loài **không** thuộc 20 lớp của K20) làm mẫu âm, đo hai đường cong: tỷ lệ từ chối đúng trên mẫu âm so với tỷ lệ từ chối sai trên mẫu dương, khi quét ngưỡng độ tin cậy τ từ 0,1 tới 0,9. Báo cáo diện tích dưới đường cong và giá trị τ được chọn cho ứng dụng. Đây là phép đo trực tiếp cho câu hỏi thực tế nhất — *khi người nông dân chụp một loài mà hệ thống chưa từng học, hệ thống có im lặng đúng lúc không?* — và nó gắn thẳng với giới hạn nền tảng đã nêu ở Mục 1.2 rằng sâu keo mùa thu và rầy chổng cánh không có trong IP102. Chi phí: một lượt suy luận, không cần huấn luyện lại.

> 4. **Đánh giá định tính bằng Grad-CAM.** Trực quan hóa vùng ảnh mà mô hình dựa vào để ra quyết định, cho ít nhất 5 cặp loài dễ nhầm, nhằm phân biệt trường hợp mô hình nhìn đúng bộ phận côn trùng với trường hợp mô hình bám vào nền cảnh hoặc watermark.

**Cách trình bày phân tích, không chỉ trình bày bảng.** Với mỗi bảng kết quả, báo cáo bắt buộc kèm ít nhất một đoạn phân tích trả lời đủ ba câu: *mô hình nào tốt hơn · tốt hơn ở chỉ số nào và bao nhiêu điểm · đánh đổi gì để có mức tốt hơn đó*. Ví dụ về dạng câu mong muốn:

> *"EfficientNet-B0 đạt macro-F1 0,84 so với 0,81 của MobileNetV2, tức cao hơn 3 điểm, nhưng số tham số tăng từ 3,4 lên 5,3 triệu và độ trễ trên điện thoại tăng từ 210 lên 340 ms/ảnh. Nếu tiêu chí là độ chính xác thì chọn EfficientNet-B0; nếu tiêu chí là trải nghiệm người dùng trên máy tầm thấp thì MobileNetV2 vẫn là lựa chọn hợp lý vì cả hai đều đạt ngưỡng 0,85 trên tập cân bằng, trong khi chênh lệch độ trễ là thứ người dùng cảm nhận được."*

Một bảng số không kèm đoạn phân tích như trên được coi là chưa hoàn thành.

**Nguyên tắc kỷ luật thực nghiệm:** tập kiểm thử được khóa lại và **chỉ chạy đúng một lần ở cuối**. Mọi quyết định về siêu tham số, kiến trúc và điểm dừng đều lấy trên khối phát triển. Riêng việc chọn lớp nằm ngoài vòng này hoàn toàn: bảng hạng được chấm bằng ba tiêu chí không đọc kết quả mô hình và được khóa phiên bản **trước** khi dựng dữ liệu, nên nó không thể rò rỉ thông tin từ bất kỳ tập nào. Mỗi lần nhìn vào tập kiểm thử là một lần rò rỉ thông tin qua chính người thực nghiệm.

Ngoài ra, khi so sánh kết quả của nhóm với các công bố quốc tế, báo cáo **bắt buộc ghi rõ** rằng tập dữ liệu của đề tài đã được chọn lọc theo bối cảnh Việt Nam, đã khử trùng lặp, đã chia lại theo nhóm ảnh gần trùng và đã cân bằng lại — **do đó không so sánh trực tiếp được** với các con số công bố trên IP102 đầy đủ 102 lớp. Riêng việc khử rò rỉ đã làm con số thấp đi một cách có hệ thống, và đó là con số trung thực hơn.

### ***6.7. Kiến trúc hệ thống và luồng hoạt động***

Hệ thống được thiết kế theo hướng **suy luận hoàn toàn trên thiết bị (on-device)**, không có máy chủ suy luận. Lựa chọn này xuất phát trực tiếp từ bối cảnh sử dụng: người dùng ở ngoài đồng ruộng, sóng di động không ổn định, và mô hình sau lượng tử hóa đã đủ nhỏ (chỉ tiêu ≤ 15 MB) để nhúng thẳng vào ứng dụng.

```
  ┌──────────────────────────────────────────────────────────┐
  │  ĐIỆN THOẠI NGƯỜI DÙNG                                   │
  │                                                          │
  │   Camera / Thư viện ảnh                                  │
  │            ↓                                             │
  │   Giao diện chụp – chọn cây trồng (tùy chọn)             │
  │            ↓                                             │
  │   Tiền xử lý: resize 256 → crop 224 → chuẩn hóa          │
  │            ↓                                             │
  │   Mô hình PestID đã lượng tử hóa INT8  (≤ 15 MB)         │
  │            ↓                                             │
  │   Hậu xử lý: softmax → top-3 → so ngưỡng τ               │
  │            ↓                                             │
  │   Màn hình kết quả: tên loài · độ tin cậy · ảnh mẫu      │
  │                     · dòng cảnh báo bắt buộc             │
  │            ↓                                             │
  │   Lưu lịch sử tra cứu cục bộ (không gửi lên máy chủ)     │
  └──────────────────────────────────────────────────────────┘
             ▲
             │  (một chiều, chỉ khi cập nhật phiên bản)
  ┌──────────┴───────────────────────────────────────────────┐
  │  QUY TRÌNH NGOẠI TUYẾN — Kaggle Notebook, GPU T4 × 2     │
  │  IP102 → làm sạch C1–C7 → IP102-VN → huấn luyện →        │
  │  chọn mô hình tốt nhất → lượng tử hóa INT8 → đóng gói    │
  └──────────────────────────────────────────────────────────┘
```

**Phân tách trách nhiệm.** Toàn bộ phần nghiên cứu (làm sạch dữ liệu, huấn luyện, đánh giá) chạy ngoại tuyến trên Kaggle và **không** là thành phần chạy lúc người dùng dùng ứng dụng. Sản phẩm chuyển giao giữa hai phần chỉ là một tệp mô hình đã lượng tử hóa kèm tệp bảng nhãn. Cách tách này giữ cho phần ứng dụng đơn giản, kiểm thử được độc lập, và cho phép thay mô hình mà không sửa mã ứng dụng.

**Yêu cầu chức năng.** Chụp ảnh hoặc chọn ảnh từ thư viện · chọn cây trồng (tùy chọn) · hiển thị kết quả top-1 và top-3 kèm độ tin cậy · hiển thị ảnh mẫu của loài được dự đoán để người dùng tự đối chiếu · hiển thị dòng cảnh báo bắt buộc · lưu và xem lại lịch sử tra cứu.

**Yêu cầu phi chức năng.** Độ trễ suy luận ≤ 500 ms/ảnh trên điện thoại tầm trung; kích thước gói cài đặt ≤ 50 MB; hoạt động hoàn toàn khi không có mạng; không thu thập hay gửi đi ảnh của người dùng.

**Kiểm thử hệ thống.** Kiểm thử chức năng cho từng luồng ở trên; kiểm thử hiệu năng đo độ trễ và mức tiêu thụ bộ nhớ trên điện thoại thật; kiểm thử phần trí tuệ nhân tạo đo độ chính xác, tỷ lệ dương tính giả và âm tính giả trên tập kiểm thử cân bằng và trên bộ ảnh thực địa.

### ***6.8. Công nghệ và môi trường thực nghiệm***

| Hạng mục | Lựa chọn | Ghi chú |
|---|---|---|
| Môi trường huấn luyện | **Kaggle Notebook bản miễn phí, GPU NVIDIA T4 × 2** (2 × 16 GB) | Hạn mức ~30 giờ GPU/tuần, phiên tối đa ~12 giờ; hai thí nghiệm chạy song song, mỗi GPU một thí nghiệm; mọi lượt huấn luyện có checkpoint theo epoch |
| Ngôn ngữ | Python 3.11 | Bản mặc định của môi trường Kaggle tại thời điểm thực hiện |
| Khung học sâu | PyTorch 2.x + `torchvision` | AMP fp16; không dùng bf16 (T4 kiến trúc Turing) |
| Thư viện mô hình | `timm` | Nguồn trọng số huấn luyện trước thống nhất cho mọi kiến trúc, để so sánh công bằng |
| Xử lý ảnh và dữ liệu | Pillow, OpenCV, NumPy, pandas | Quét ảnh, băm MD5/dHash, phương sai Laplacian, dựng bảng thống kê |
| Đánh giá và trực quan hóa | scikit-learn, matplotlib, seaborn | Ma trận nhầm lẫn, macro-F1, bootstrap, t-SNE/UMAP |
| Giải thích mô hình | `pytorch-grad-cam` | Bản đồ nhiệt Grad-CAM |
| Gán nhãn ảnh thực địa | `label-studio` | Mã nguồn mở, chạy cục bộ, hỗ trợ gán nhãn độc lập nhiều người |
| Xuất mô hình triển khai | PyTorch → ONNX → **TensorFlow Lite**, lượng tử hóa INT8 (thử cả PTQ và QAT) | Khớp sản phẩm SP5 của phiếu mô tả đề tài |
| Ứng dụng demo | Flutter (Android), gói `tflite_flutter` | Chọn Flutter vì suy luận trên thiết bị và một mã nguồn cho nhiều nền tảng |
| Thiết bị đo độ trễ | Điện thoại Android tầm trung của thành viên nhóm — **ghi rõ model máy, chip và bản Android trong báo cáo** | Không đo trên máy cao cấp để con số phản ánh đúng người dùng mục tiêu |
| Quản lý mã nguồn và kết quả | Git + GitHub; kết quả mỗi thí nghiệm lưu kèm cấu hình, nhật ký, checkpoint, seed và mã commit | Điều kiện để tái lập |

Mọi lựa chọn trên đều thuộc nhóm miễn phí hoặc mã nguồn mở, không phát sinh chi phí bản quyền và không yêu cầu phần cứng ngoài máy tính cá nhân của thành viên nhóm cùng Kaggle Notebook bản miễn phí.

## **7. Ý nghĩa khoa học và thực tiễn**

### ***7.1. Ý nghĩa khoa học***

Đề tài bổ sung bằng chứng thực nghiệm cho bài toán phân loại ảnh mịn trên bộ dữ liệu IP102 ở bốn khía cạnh mà tài liệu hiện có còn bỏ trống.

Thứ nhất, việc đo và công bố mức rò rỉ giữa tập huấn luyện và tập kiểm thử trong phép chia chuẩn của IP102, kèm con số định lượng phần độ chính xác bị thổi phồng, là thông tin có giá trị cho mọi nghiên cứu sau dùng bộ dữ liệu này.

Thứ hai, việc xây dựng tập con theo nguyên tắc **một bảng xếp hạng duy nhất, bốn điểm cắt** — chấm cả 102 lớp trên ba tiêu chí neo vào bối cảnh dịch hại của một quốc gia cụ thể, rồi cắt tiền tố để sinh bốn mốc quy mô lồng nhau — mở ra một cách tiếp cận cho việc thiết kế tập dữ liệu ứng dụng mà tài liệu hiện có chưa dùng. Giá trị của cách làm này nằm ở ba tính chất kiểm chứng được: thành phần của mọi tập K truy ngược trực tiếp về một bảng điểm duy nhất; **không tiêu chí nào trong bảng điểm đọc kết quả mô hình**, nên độ chính xác công bố không chứa phần thổi phồng do chọn lớp theo mức dễ nhận dạng; và vì bốn tập là bốn tiền tố của cùng một thứ tự, dùng chung một phép chia dữ liệu, khảo sát ảnh hưởng của số lớp ở Mục 3.6 cô lập được đúng một biến — điều mà cách dựng bốn tập độc lập không làm được.

Gắn với điểm này, đề tài còn báo cáo quan hệ giữa điểm bối cảnh Việt Nam và độ dễ tách của lớp trong không gian đặc trưng như một **phát hiện mô tả**: khảo sát sơ bộ trên 45 lớp ứng viên cho tương quan âm nhẹ, tức các lớp quan trọng nhất về mặt nông học có xu hướng là các lớp khó nhất về mặt thị giác máy tính. Con số này được đo lại trên bảng hạng đã khóa và **chỉ để báo cáo**, không dùng để điều chỉnh thứ hạng.

Thứ ba, việc hiện thực hóa và đo đạc mô hình chưng cất tri thức PestID-KD trả lời một câu hỏi đã được nêu tên trong tài liệu 2025 nhưng chưa ai kiểm chứng, đồng thời bổ sung ma trận nhầm lẫn chi tiết và phân tích Grad-CAM cho các cặp loài dễ nhầm — phần phân tích mà đa số công trình hiện có chỉ dừng ở mức báo cáo accuracy tổng.

Thứ tư, phép so sánh ba mô hình nền ở ba mức độ chuyên ngành khác nhau (tổng quát, sinh vật, côn trùng học) trên cùng một tập con cho một mốc tham chiếu mà các nghiên cứu sau có thể dùng lại.

### ***7.2. Ý nghĩa thực tiễn***

Sản phẩm của đề tài là một hệ thống nhận diện côn trùng gây hại chạy được trên điện thoại phổ thông, có thể trở thành công cụ tra cứu nhanh hỗ trợ nông dân và cán bộ khuyến nông trong bước định danh dịch hại — bước quyết định việc chọn đúng biện pháp phòng trừ. Việc rút ngắn thời gian từ lúc phát hiện dịch hại đến lúc có thông tin định danh góp phần giảm tình trạng phun thuốc sai đối tượng, qua đó giảm chi phí sản xuất và giảm tác động tới môi trường.

Bộ ảnh sâu hại thực địa do nhóm tự thu — dù chỉ vài trăm ảnh — là tài sản dữ liệu gốc đầu tiên của đơn vị cho bài toán này, sử dụng lại được cho các đồ án và nghiên cứu khoa học của các khóa sau. Toàn bộ mã nguồn, tập dữ liệu đã xử lý và tài liệu quy trình được đóng gói theo hướng tái lập được, để nhóm khác có thể tiếp tục mở rộng thay vì làm lại từ đầu.

Hệ thống được xác định rõ là **công cụ hỗ trợ tham khảo**, không thay thế chẩn đoán của cán bộ bảo vệ thực vật; dòng cảnh báo này là thành phần bắt buộc của giao diện ứng dụng. Cơ chế từ chối trả lời khi độ tin cậy thấp là biện pháp kỹ thuật đi kèm để lời cảnh báo đó không chỉ là một dòng chữ.

---

# **BỐ CỤC ĐỒ ÁN MÔN HỌC (DỰ KIẾN)**

## **Chương 1: Cơ sở lý thuyết và tổng quan nghiên cứu**

1.1. Tổng quan về học máy và học sâu: khái niệm, phân loại phương pháp, quy trình xây dựng mô hình.

1.2. Mạng nơ-ron tích chập (CNN): phép tích chập, nhân lọc, bản đồ đặc trưng, phép gộp, hàm kích hoạt, lớp kết nối đầy đủ; cơ chế trích xuất đặc trưng ảnh.

1.3. Các kiến trúc CNN hiệu quả: khối phần dư đảo ngược và nút cổ chai tuyến tính của MobileNetV2 [2]; nguyên lý mở rộng hợp thành cân bằng chiều sâu/chiều rộng/độ phân giải của EfficientNet [3].

1.4. Cơ chế Attention và kiến trúc Transformer cho ảnh: self-attention, multi-head attention, cách chia ảnh thành patch của Vision Transformer [4]; biến thể ViT-Tiny và công thức huấn luyện hiệu quả dữ liệu của DeiT [5].

1.5. Học chuyển giao và tinh chỉnh: nguyên lý, các chiến lược đóng băng backbone / tinh chỉnh theo tầng / tinh chỉnh toàn phần; vì sao học chuyển giao là bắt buộc với tập dữ liệu vài nghìn đến vài chục nghìn ảnh.

1.6. Chưng cất tri thức và các kỹ thuật làm nhẹ mô hình: chưng cất trên logits, token chưng cất [5], lượng tử hóa sau huấn luyện và huấn luyện nhận biết lượng tử hóa [14].

1.7. Bài toán phân loại ảnh mịn và phân bố đuôi dài: đặc điểm, các kỹ thuật tăng cường dữ liệu (CutMix, MixUp) và lấy mẫu có trọng số theo số hiệu dụng [7][16][17].

1.8. Mô hình nền và học tự giám sát: nguyên lý, linear probe như một phép ước lượng trần trên của dữ liệu, ba mức độ chuyên ngành của mô hình nền [31][32][40].

1.9. Các chỉ số đánh giá mô hình phân loại: Accuracy, Precision, Recall, F1-score, macro-F1, balanced accuracy, ma trận nhầm lẫn; ý nghĩa của từng chỉ số trong điều kiện dữ liệu mất cân bằng; khoảng tin cậy bootstrap và kiểm định McNemar.

1.10. Tổng quan các nghiên cứu trong nước và quốc tế về nhận diện côn trùng gây hại [1][6]–[30]; bảng mốc kết quả đã xác minh trên IP102 và phân tích sáu khoảng trống nghiên cứu.

## **Chương 2: Phương pháp nghiên cứu và mô hình đề xuất**

2.1. Bộ dữ liệu IP102: cấu trúc, hai nửa phân loại và phát hiện, quy ước nhãn và bẫy lệch một đơn vị giữa bảng tên lớp với thư mục lớp.

2.2. Phân tích khám phá dữ liệu: phân bố lớp, phân bố theo nhóm cây trồng, chất lượng ảnh, hiệu chuẩn ngưỡng độ nét, đo trùng lặp và rò rỉ, phân tích không gian đặc trưng bằng embedding và linear probe.

2.3. Xây dựng tập con IP102-VN: nguyên tắc một bảng xếp hạng duy nhất và bốn điểm cắt — ba tiêu chí P/A/D với trọng số 40%–35%–25%, công thức điểm tổng và ba khóa phá hòa; quy trình bảy bước chấm và khóa bảng hạng; ranh giới giữa điều được phép và không được phép thay đổi thứ hạng; bốn tập con lồng nhau K = 15/20/35/45 và bảng 45 hạng đầu; bốn trường nhãn của manifest.

2.4. Quy trình làm sạch dữ liệu C1–C7 và bộ 12 phép kiểm tra chấp nhận; quy tắc chia một lần trên K45 theo nhóm ảnh gần trùng với tỷ lệ 60/15/25 rồi lọc xuống K35/K20/K15; các sàn và trần số ảnh; tập `test_balanced`.

2.5. Thành phần chung của các mô hình: đầu vào 224×224, đầu phân loại, hàm mất mát, bộ tối ưu, lịch learning rate, chiến lược tăng cường dữ liệu, cơ chế xử lý mất cân bằng.

2.6. Bảy kiến trúc nền: MobileNetV2, EfficientNet-B0, ViT-Tiny, MobileNetV4-Conv-S, TinyViT-5M, FastViT-T12, iFormer-M — cấu hình học chuyển giao và lý do lựa chọn cho từng kiến trúc.

2.7. Mốc trần trên bằng mô hình nền: so sánh ba mô hình nền ở ba mức chuyên ngành và cách đọc kết quả linear probe.

2.8. Mô hình đề xuất PestID-KD: kiến trúc teacher–student, tiêu chí chọn teacher, hàm mất mát chưng cất, kết hợp CutMix/MixUp, thiết kế nghiên cứu loại trừ ba mức và tiêu chí dừng.

2.9. Nhánh mở rộng PestID-CropGate: mặt nạ logits theo cây trồng, thí nghiệm kiểm chứng và cách kiểm soát rủi ro rò rỉ đáp án.

2.10. Kiến trúc hệ thống: luồng suy luận trên thiết bị, phân tách phần nghiên cứu và phần ứng dụng, yêu cầu chức năng và phi chức năng.

2.11. Quy trình thực nghiệm: môi trường Kaggle Notebook T4 × 2, cách khai thác hai GPU, danh mục thí nghiệm E1–E9, cách chia việc cho nhóm, quy ước lưu kết quả và bảo đảm tính tái lập.

## **Chương 3: Thực nghiệm, đánh giá kết quả và xây dựng hệ thống**

3.1. Môi trường thực nghiệm: cấu hình Kaggle T4 × 2, phiên bản thư viện, cấu hình siêu tham số.

3.2. Kết quả xây dựng tập dữ liệu: bảng xếp hạng 102 lớp đã khóa phiên bản kèm điểm thành phần, số ảnh trước/sau làm sạch, mức rò rỉ đo được theo lớp, tỷ lệ mất cân bằng trước/sau khi áp trần, quy mô thực tế của bốn tập K và kết quả 12 phép kiểm tra chấp nhận.

3.3. Kết quả linear probe trên ba mô hình nền: trần trên của dữ liệu và đối chiếu với mục tiêu 0,85.

3.4. Kết quả huấn luyện bảy kiến trúc nền: đường cong mất mát và độ chính xác, bảng so sánh đầy đủ theo tất cả chỉ số của Mục 6.6, kèm khoảng tin cậy bootstrap.

3.5. Kết quả mô hình đề xuất PestID-KD và bảng nghiên cứu loại trừ ba mức; so sánh với student huấn luyện thuần ở cùng số tham số, kèm kiểm định McNemar.

3.6. Khảo sát ảnh hưởng của số lớp K: so sánh cùng kiến trúc trên bốn mức K = 15/20/35/45, trên cùng một phép chia dữ liệu.

3.7. Đo mức thổi phồng độ chính xác do rò rỉ dữ liệu; so sánh phép chia theo nhóm gần trùng với phép chia gốc của IP102.

3.8. So sánh tập chính K20 với bộ đối chứng 20 lớp chọn theo số ảnh nhiều nhất: khác biệt về phân bố lớp, mức mất cân bằng và độ khó giữa cách chọn theo bối cảnh Việt Nam và thông lệ quốc tế.

3.9. Phân tích lỗi: độ chính xác tách theo Head/Medium/Tail, recall từng lớp, ma trận nhầm lẫn các cặp loài dễ nhầm, bản đồ nhiệt Grad-CAM, các trường hợp sai điển hình và nguyên nhân.

3.10. Kết quả trên bộ ảnh thực địa Việt Nam: mức sụt độ chính xác và phân tích nguyên nhân; đánh giá cơ chế từ chối trả lời trên bộ `ngoai_tap`.

3.11. Xây dựng hệ thống: lượng tử hóa và xuất mô hình TensorFlow Lite, đo kích thước và độ trễ trên điện thoại thật, thiết kế và cài đặt ứng dụng demo, kiểm thử chức năng, hiệu năng và phần trí tuệ nhân tạo.

3.12. So sánh kết quả với các nghiên cứu liên quan; thảo luận về đóng góp, hạn chế và bài học kinh nghiệm.

## **Phần kết luận**

- Tóm tắt bài toán nghiên cứu, phương pháp tiếp cận và kết quả đạt được, đối chiếu từng mục tiêu cụ thể và từng câu hỏi nghiên cứu của đề cương với mức độ hoàn thành thực tế.

- Đóng góp về mặt khoa học, thực tiễn và kỹ thuật của đồ án môn học.

- **Hạn chế của nghiên cứu:**
  - Tập lớp bị giới hạn bởi chính IP102: hai dịch hại trọng yếu của Việt Nam là sâu keo mùa thu *Spodoptera frugiperda* và rầy chổng cánh *Diaphorina citri* không có lớp trong bộ dữ liệu, nên hệ thống **không nhận diện được hai đối tượng này**. Người dùng phải được thông báo rõ điều đó trong ứng dụng, bên cạnh dòng cảnh báo chung.
  - Ba điểm `P`, `A`, `D` là **đánh giá đồng thuận của nhóm**, không phải số đo từ một công cụ độc lập. Đề tài kiểm soát điều này bằng cách công bố điểm thành phần của từng lớp cùng căn cứ đánh giá và khóa bảng hạng theo phiên bản, nhưng một nhóm khác chấm lại vẫn có thể ra một thứ tự hơi khác. Riêng tiêu chí `D` có tương quan một phần với số ảnh sạch, nên bảng hạng vẫn nghiêng nhẹ về các lớp mà IP102 tình cờ có nhiều ảnh.
  - Quy mô tập con, ràng buộc tài nguyên tính toán, số lượng ảnh thực địa còn ít, và phạm vi giới hạn ở bài toán phân loại đơn nhãn.

- **Hướng phát triển:** mở rộng số lớp và bổ sung ảnh cho các lớp thiếu dữ liệu, đặc biệt là thu ảnh trong nước cho sâu keo mùa thu và rầy chổng cánh để bổ sung hai lớp mà IP102 không có; tách nhãn theo giai đoạn vòng đời để xử lý nguyên nhân gốc của nhầm lẫn hình thái; gộp các cặp lớp mà nông dân xử lý giống nhau, dựa trên các cặp có thành phần biên gần 0 đã xác định ở Mục 6.4; mở rộng sang bài toán phát hiện và định vị; nâng cấp cơ chế từ chối trả lời thành phát hiện ngoài phân phối đầy đủ; và tích hợp gợi ý biện pháp phòng trừ theo hướng dẫn của cơ quan bảo vệ thực vật.

---

# **TIẾN ĐỘ THỰC HIỆN**

Đồ án môn học được thực hiện trong **14 tuần (khoảng 3,5 tháng)**, từ **01/9/2026 đến 07/12/2026**, chia thành 4 giai đoạn chính. Nhóm 4–5 sinh viên được phân thành ba mảng công việc chạy song song: **Dữ liệu & Tiền xử lý** — **Mô hình & Thực nghiệm** — **Ứng dụng & Báo cáo**.

## **GIAI ĐOẠN 1: NGHIÊN CỨU LÝ THUYẾT VÀ XÂY DỰNG TẬP DỮ LIỆU**

**Thời gian:** 01/9 – 28/9/2026 (Tuần 1–4)

> * Hoàn thiện đề cương chi tiết; thống nhất phân công và quy ước làm việc chung của nhóm.

> * Nghiên cứu tài liệu về học sâu, phân loại ảnh mịn, học chuyển giao và các công trình trên IP102; xây dựng Chương 1 và bảng mốc kết quả đã xác minh.

> * Tải và kiểm chứng bộ dữ liệu IP102; phân tích khám phá dữ liệu (phân bố lớp, phân bố theo cây trồng, chất lượng ảnh, trùng lặp, rò rỉ).

> * Hiệu chuẩn ngưỡng độ nét bằng buổi chấm mù 200 ảnh; tổng hợp số ảnh sạch, trạng thái nhãn và thông tin bối cảnh Việt Nam cho đủ 102 lớp làm nền cho buổi chấm điểm.

> * Tổ chức buổi chấm chung: thống nhất `P`, `A`, `D` cho cả 102 lớp, tính điểm tổng, xếp hạng, phá hòa và **khóa phiên bản bảng hạng** kèm biên bản; sinh K15/K20/K35/K45 bằng phép cắt tiền tố; dựng thêm bộ đối chứng 20 lớp chọn theo số ảnh nhiều nhất cho thí nghiệm E1.

> * Thực hiện quy trình làm sạch C1–C7, gồm buổi rà tay chung của cả nhóm cho các nhóm nhãn mâu thuẫn.

> * Chạy bộ 12 phép kiểm tra chấp nhận trên bản đã giải nén ở môi trường khác; đóng gói và chia sẻ tập dữ liệu cho cả nhóm; viết datasheet cho bộ dữ liệu.

## **GIAI ĐOẠN 2: XÂY DỰNG PIPELINE VÀ HUẤN LUYỆN BA KIẾN TRÚC NỀN**

**Thời gian:** 29/9 – 26/10/2026 (Tuần 5–8)

> * Thiết kế kiến trúc tổng thể hệ thống theo Mục 6.7; lựa chọn công cụ và thư viện (PyTorch, timm, torchvision).

> * Cài đặt pipeline huấn luyện dùng chung: bộ nạp dữ liệu, bộ tăng cường, bộ lấy mẫu có trọng số, vòng huấn luyện, mô-đun đánh giá và ghi kết quả, cơ chế checkpoint-resume.

> * Chạy **E2** — linear probe trên ba mô hình nền để xác định trần trên của tập dữ liệu; đối chiếu kết quả với mục tiêu 0,85 và chọn teacher cho PestID-KD.

> * Chạy **E1** — so sánh tập chính K20 với bộ đối chứng 20 lớp chọn theo số ảnh nhiều nhất.

> * Huấn luyện EfficientNet-B0 trên tập K20 để kiểm chứng toàn bộ đường ống trước khi mở rộng.

> * **Tuần 6 — kiểm chứng sớm đường xuất mô hình:** thử chuyển một mô hình bất kỳ theo đường PyTorch → ONNX → TensorFlow Lite để phát hiện sớm lỗi toán tử không hỗ trợ.

> * Chạy **E3** — huấn luyện đầy đủ bảy kiến trúc nền trên K20, mỗi phiên chạy hai kiến trúc song song trên hai GPU; và **E4** — khảo sát ảnh hưởng số lớp với ba kiến trúc đại diện trên cả bốn mức K. Chia việc giữa các thành viên; tổng hợp bảng so sánh kèm khoảng tin cậy bootstrap.

## **GIAI ĐOẠN 3: MÔ HÌNH ĐỀ XUẤT, PHÂN TÍCH LỖI VÀ KIỂM CHỨNG THỰC ĐỊA**

**Thời gian:** 27/10 – 23/11/2026 (Tuần 9–12)

> * Chạy **E5** — cài đặt và huấn luyện mô hình đề xuất PestID-KD; chạy nghiên cứu loại trừ ba mức; kiểm định McNemar so với student thuần.

> * Chạy **E9** — thí nghiệm kiểm chứng cho nhánh PestID-CropGate; quyết định triển khai hoặc dừng theo tiêu chí đã đặt trước.

> * Chạy **E6** — đo mức thổi phồng độ chính xác do rò rỉ dữ liệu.

> * Phân tích lỗi: độ chính xác theo Head/Medium/Tail, recall từng lớp, ma trận nhầm lẫn các cặp loài dễ nhầm, bản đồ nhiệt Grad-CAM.

> * Thu và gán nhãn 100–200 ảnh sâu hại thực địa tại Việt Nam theo quy trình bốn bước ở Mục 6.2.5; chạy **E8** và đánh giá cơ chế từ chối trả lời trên bộ `ngoai_tap`.

> * Viết Chương 2 và Chương 3 của báo cáo song song với quá trình thực nghiệm.

## **GIAI ĐOẠN 4: TRIỂN KHAI, HOÀN THIỆN BÁO CÁO VÀ BẢO VỆ**

**Thời gian:** 24/11 – 07/12/2026 (Tuần 13–14)

> * Chạy **E7** — lượng tử hóa PTQ và QAT, xuất mô hình TensorFlow Lite; đo kích thước và độ trễ trên điện thoại thật.

> * Hoàn thiện ứng dụng demo: chọn cây trồng, chụp/tải ảnh, hiển thị kết quả kèm độ tin cậy, cờ không chắc chắn và dòng cảnh báo bắt buộc; kiểm thử chức năng, hiệu năng và phần trí tuệ nhân tạo.

> * Hoàn thiện báo cáo 60–70 trang; viết model card cho mô hình triển khai; chỉnh sửa theo góp ý của giảng viên hướng dẫn.

> * Chuẩn bị slide thuyết trình, demo hệ thống và bảo vệ thử; nộp báo cáo cuối kỳ.

## **Tiến độ chi tiết theo tuần**

| Tuần | Thời gian | Nội dung công việc chính |
| :---: | :---: | ----- |
| Tuần 1–2 | 01/9 – 14/9/2026 | Hoàn thiện đề cương; nghiên cứu tài liệu nền về học sâu, CNN, Vision Transformer, học chuyển giao; tải và kiểm chứng IP102. |
| Tuần 3–4 | 15/9 – 28/9/2026 | Phân tích khám phá dữ liệu; hiệu chuẩn ngưỡng độ nét; buổi chấm chung `P`/`A`/`D` cho 102 lớp và **khóa phiên bản bảng hạng**; cắt tiền tố sinh bốn tập K và dựng bộ đối chứng; làm sạch C1–C7; chia một lần trên K45 rồi lọc xuống; chạy 12 phép kiểm tra chấp nhận; viết datasheet; hoàn chỉnh Chương 1. |
| Tuần 5–6 | 29/9 – 12/10/2026 | Cài đặt pipeline huấn luyện dùng chung; chạy E1 và E2; huấn luyện EfficientNet-B0 trên K20 để kiểm chứng đường ống; **kiểm chứng sớm đường xuất TensorFlow Lite**. |
| Tuần 7–8 | 13/10 – 26/10/2026 | Chạy E3 và E4 — huấn luyện đầy đủ bảy kiến trúc nền trên K20 và ba kiến trúc đại diện trên bốn mức K; tổng hợp bảng so sánh kèm khoảng tin cậy; khảo sát ảnh hưởng của K. |
| Tuần 9–10 | 27/10 – 09/11/2026 | Chạy E5 (PestID-KD + ablation ba mức + McNemar), E9 (kiểm chứng CropGate) và E6 (mức thổi phồng do rò rỉ). |
| Tuần 11–12 | 10/11 – 23/11/2026 | Phân tích lỗi (Head/Medium/Tail, ma trận nhầm lẫn, Grad-CAM); thu và gán nhãn ảnh thực địa; chạy E8 và đánh giá cơ chế từ chối trả lời; viết Chương 2 và Chương 3. |
| Tuần 13–14 | 24/11 – 07/12/2026 | Chạy E7 — lượng tử hóa và xuất mô hình TensorFlow Lite; hoàn thiện ứng dụng demo; hoàn thiện báo cáo 60–70 trang và model card; chuẩn bị slide và bảo vệ thử; nộp báo cáo cuối kỳ. |

## **Rủi ro và phương án dự phòng**

| Rủi ro | Dấu hiệu nhận biết | Thời điểm kiểm | Phương án |
|---|---|:---:|---|
| Trần trên của dữ liệu thấp hơn mục tiêu | Linear probe (E2) dưới 70% | Tuần 5 | Quay lại làm sạch nhãn; nếu vẫn dưới 75%, đề nghị giảng viên hướng dẫn cho điều chỉnh mục tiêu từ 0,85 xuống mức có căn cứ thực nghiệm, kèm số liệu chứng minh |
| Chưng cất tri thức không hiệu quả | Teacher không vượt student baseline ≥ 5 điểm | Tuần 9 | Chuyển teacher sang ensemble các kiến trúc nền mạnh nhất; nếu vẫn không đạt, dừng nhánh và **báo cáo kết quả âm tính kèm phân tích nguyên nhân** — đây vẫn là một đóng góp hợp lệ |
| Không thu đủ ảnh thực địa | Dưới 60 ảnh vào cuối tuần 11 | Tuần 11 | Giảm chỉ tiêu xuống 60–80 ảnh nhưng giữ đủ ít nhất 5 lớp; ghi rõ quy mô nhỏ là hạn chế trong báo cáo |
| Xuất TensorFlow Lite lỗi toán tử | Bước chuyển ONNX → TFLite báo lỗi op không hỗ trợ | **Tuần 6** | Đổi sang kiến trúc thuần tích chập lúc suy luận; hoặc chuyển demo sang chạy suy luận phía máy chủ và ghi rõ thay đổi phạm vi |
| Hết hạn mức GPU hoặc không được cấp GPU | Không chạy được phiên có GPU trong 2 ngày liên tiếp | Bất kỳ lúc nào | Luân phiên tài khoản giữa 4–5 thành viên (mỗi tài khoản ~30 giờ GPU/tuần); mọi lượt huấn luyện lưu checkpoint theo epoch |
| Nhóm chưa thống nhất được bảng điểm đúng hạn | Hết tuần 3 mà buổi chấm chung chưa chốt đủ `P`, `A`, `D` cho 102 lớp | Tuần 3 | Chốt bằng điểm trung bình của các thành viên đã chấm, ghi rõ số người chấm và mức phân tán vào biên bản, rồi khóa phiên bản để không chặn tiến độ dựng dữ liệu; ghi nhận đây là phiên bản có thể được thay bằng bản chấm đầy đủ hơn |
| Một lớp trong 45 hạng đầu không đạt sàn dữ liệu | Sau C1–C7 lớp có dưới 200 ảnh sạch, hoặc sau chia không đạt sàn 100 / 30 / 50 | Tuần 4 | Chấm lại tiêu chí `D` cho lớp đó hoặc xử lý lại dữ liệu, rồi **xếp lại toàn bảng và tạo phiên bản bảng hạng mới** — không thay lớp riêng lẻ ở một vị trí (Mục 6.2.2) |

**Phạm vi tối thiểu (MVP).** Nếu tiến độ trượt, thứ tự cắt bỏ đã được thống nhất trước — cắt từ dưới lên: ① nhánh PestID-CropGate (E9) → ② khảo sát K = 15, 35 và 45 (E4, giữ lại K20) → ③ so sánh PTQ với QAT (giữ một phương án lượng tử hóa). **Không cắt** trong mọi trường hợp: bộ dữ liệu IP102-VN đã kiểm chứng, bảy kiến trúc nền trên K20, phân tích lỗi, và bộ ảnh thực địa — bốn phần này là đóng góp khó thay thế nhất của đề tài.

## **Sản phẩm đầu ra của từng giai đoạn**

### ***Giai đoạn 1:***

> * Đề cương chi tiết hoàn chỉnh và danh mục tài liệu tham khảo đầy đủ.

> * Cơ sở lý thuyết (Chương 1) và bảng mốc kết quả đã xác minh trên IP102.

> * **Bộ dữ liệu IP102-VN** — một manifest K45 duy nhất cùng ba tập K35/K20/K15 lọc từ đó và một bộ đối chứng, tự chứa, kèm bảng lớp ghi đủ điểm `P`/`A`/`D`, điểm tổng, hạng và bốn trường nhãn ở Mục 6.2.4, tài liệu tiêu chí chọn, danh sách ảnh bị loại có lý do và mã băm kiểm tra toàn vẹn.

> * **Datasheet cho bộ dữ liệu IP102-VN** theo cấu trúc chuẩn: động cơ xây dựng · thành phần · quy trình thu thập và tiền xử lý · mục đích sử dụng được khuyến nghị và không khuyến nghị · giới hạn đã biết · thông tin phân phối và bảo trì.

> * Báo cáo phân tích khám phá dữ liệu kèm bộ hình ảnh chứng minh các phát hiện (rò rỉ, trùng lặp, nhãn chồng lấn, mất cân bằng) và biểu đồ hiệu chuẩn ngưỡng độ nét.

> * **Bảng xếp hạng 102 lớp đã khóa phiên bản** kèm biên bản buổi chấm điểm, và báo cáo kết quả 12 phép kiểm tra chấp nhận.

### ***Giai đoạn 2:***

> * Mã nguồn pipeline huấn luyện dùng chung, có chú thích đầy đủ, cố định hạt giống, có checkpoint-resume, chạy được trên Kaggle Notebook T4 × 2.

> * Bảy mô hình nền đã huấn luyện, kèm trọng số và nhật ký huấn luyện.

> * Kết quả linear probe trên ba mô hình nền, xác định trần trên của tập dữ liệu và chọn teacher.

> * Bảng so sánh bảy kiến trúc theo đầy đủ chỉ số kèm khoảng tin cậy; bảng khảo sát ảnh hưởng của số lớp K.

> * Kết quả kiểm chứng sớm đường xuất TensorFlow Lite.

### ***Giai đoạn 3:***

> * **Mô hình đề xuất PestID-KD** — trọng số, mã nguồn và bảng nghiên cứu loại trừ ba mức kèm kiểm định McNemar.

> * Kết quả thí nghiệm kiểm chứng nhánh PestID-CropGate.

> * Số liệu mức thổi phồng độ chính xác do rò rỉ dữ liệu, và số liệu chênh lệch giữa tập chính với bộ đối chứng.

> * Báo cáo phân tích lỗi: bảng Head/Medium/Tail, recall từng lớp, ma trận nhầm lẫn, bản đồ nhiệt Grad-CAM cho ít nhất 5 cặp loài dễ nhầm.

> * **Bộ ảnh sâu hại thực địa Việt Nam** — 100–200 ảnh đã gán nhãn kèm siêu dữ liệu và chỉ số đồng thuận Cohen's κ, bộ `ngoai_tap`, và số liệu mức sụt độ chính xác.

### ***Giai đoạn 4:***

> * Mô hình triển khai TensorFlow Lite đã lượng tử hóa (chỉ tiêu ≤ 15 MB) kèm số liệu độ trễ đo trên điện thoại thật (chỉ tiêu ≤ 500 ms/ảnh), và **model card** ghi rõ dữ liệu huấn luyện, chỉ số theo nhóm lớp, điều kiện hoạt động tốt và kém, cùng các giới hạn đã biết.

> * **Ứng dụng demo** cho phép chọn cây trồng, chụp/tải ảnh và trả về kết quả nhận diện kèm độ tin cậy, có cờ không chắc chắn và dòng cảnh báo: *"Kết quả chỉ mang tính tham khảo, không thay thế chẩn đoán của cán bộ bảo vệ thực vật."*

> * Báo cáo đồ án môn học hoàn chỉnh 60–70 trang, trích dẫn tối thiểu 25 tài liệu trong đó ít nhất 10 tài liệu thuộc giai đoạn 2024–2026.

> * **Bản thảo bài báo nghiên cứu khoa học cấp Khoa** về nhánh PestID-CropGate — điều kiện hóa theo ngữ cảnh cây trồng cho bài toán phân loại côn trùng gây hại (chỉ thực hiện nếu thí nghiệm kiểm chứng E9 cho kết quả khả quan).

> * Slide thuyết trình và bản demo hệ thống.

---

# **TÀI LIỆU THAM KHẢO**

1. Wu, X., Zhan, C., Lai, Y.-K., Cheng, M.-M., & Yang, J. (2019). IP102: A large-scale benchmark dataset for insect pest recognition. In *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*, pp. 8787–8796.

2. Sandler, M., Howard, A., Zhu, M., Zhmoginov, A., & Chen, L.-C. (2018). MobileNetV2: Inverted residuals and linear bottlenecks. In *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*, pp. 4510–4520.

3. Tan, M., & Le, Q. V. (2019). EfficientNet: Rethinking model scaling for convolutional neural networks. In *Proceedings of the 36th International Conference on Machine Learning (ICML)*, PMLR 97, pp. 6105–6114.

4. Dosovitskiy, A., Beyer, L., Kolesnikov, A., Weissenborn, D., Zhai, X., Unterthiner, T., et al. (2021). An image is worth 16×16 words: Transformers for image recognition at scale. In *International Conference on Learning Representations (ICLR)*. arXiv:2010.11929.

5. Touvron, H., Cord, M., Douze, M., Massa, F., Sablayrolles, A., & Jégou, H. (2021). Training data-efficient image transformers & distillation through attention. In *Proceedings of the 38th International Conference on Machine Learning (ICML)*. arXiv:2012.12877.

6. Ung, H. T., Ung, H. Q., & Nguyen, B. T. (2021). *An efficient insect pest classification using multiple convolutional neural network based models*. arXiv:2107.12189.

7. Setiawan, A., Yudistira, N., & Wihandika, R. C. (2022). Large scale pest classification using efficient convolutional neural network with augmentation and regularizers. *Computers and Electronics in Agriculture*, 200, 107204.

8. Peng, H., Xu, H., Shen, G., Liu, H., Guan, X., & Li, M. (2024). A lightweight crop pest classification method based on improved MobileNet-V2 model. *Agronomy*, 14(6), 1334.

9. Wei, J., Gong, H., Li, S., You, M., Zhu, H., et al. (2024). Improving the accuracy of agricultural pest identification: Application of AEC-YOLOv8n to large-scale pest datasets. *Agronomy*, 14(8), 1640.

10. Zhang, L., Zhao, C., Feng, Y., & Li, D. (2023). Pests identification of IP102 by YOLOv5 embedded with the novel lightweight module. *Agronomy*, 13(6), 1583.

11. Zou, J., Yang, W., Li, C., & Feng, Z. (2025). AdaptPest-Net: A task-adaptive network with Graph–Mamba fusion for multi-scale agricultural pest recognition. *Entropy*, 27(12), 1211.

12. Li, Y., et al. (2022). *Rethinking vision transformers for MobileNet size and speed (EfficientFormerV2)*. arXiv:2212.08059.

13. Jeevan, P., & Sethi, A. (2024). *Which backbone to use: A resource-efficient domain specific comparison for computer vision*. arXiv:2406.05612.

14. Akhtar, M. H., Eksheir, I., & Shanableh, T. (2025). Edge-optimized deep learning architectures for classification of agricultural insects with mobile deployment. *Information*, 16(5), 348.

15. Struniawski, K., Machlanska, A., Marasek-Ciolakowska, A., & Konopka, A. (2026). Automated pollen classification via subinstance recognition: A comprehensive comparison of classical and deep learning architectures. *Applied Sciences*, 16(2), 720.

16. Chen, S., Gao, Q., & He, Y. (2024). A pest image recognition method for long-tail distribution problem. *Frontiers in Environmental Science*, 12, 1391770.

17. Wang, Y. (2025). Improving long-tailed pest classification using diffusion model-based data augmentation. *Computers and Electronics in Agriculture*, 234, 110244.

18. Qian, Y., Xiao, Z., & Deng, Z. (2025). Fine-grained crop pest classification based on multi-scale feature fusion and mixed attention mechanisms. *Frontiers in Plant Science*, 16, 1500571.

19. Wang, F., et al. (2024). Pest-ConFormer: A hybrid CNN-Transformer architecture for large-scale multi-class crop pest recognition. *Expert Systems with Applications*, 255, 124833.

20. Peng, Y., & Wang, Y. (2022). CNN and transformer framework for insect pest classification. *Ecological Informatics*, 72, 101846.

21. Wang, Q., et al. (2024). *InsectMamba: Insect pest classification with state space model*. arXiv:2404.03611.

22. Zhang, X., Liang, K., & Zhang, Y. (2024). Plant pest and disease lightweight identification model by fusing tensor features and knowledge distillation. *Frontiers in Plant Science*, 15, 1443815.

23. Kang, H., et al. (2023). A novel deep learning model for accurate pest detection and edge computing deployment. *Insects*, 14(7), 660.

24. Angelescu, N., et al. (2025). Ensemble of efficient vision transformers for insect classification. *Applied Sciences*, 15(13), 7610.

25. Chen, Y., Chen, M., Guo, M., Wang, J., & Zheng, N. (2023). Pest recognition based on multi-image feature localization and adaptive filtering fusion. *Frontiers in Plant Science*, 14, 1282212.

26. Nguyen, T., Nguyen, H., Ung, H. Q., Ung, H. T., & Nguyen, B. T. (2024). *Deep-Wide Learning Assistance for Insect Pest Classification (DeWi)*. arXiv:2409.10445.

27. Hechen, Z., Huang, W., Yin, L., Xie, W., & Zhao, Y. (2024). Dilated-windows-based vision transformer with efficient-suppressive-self-attention for insect pests classification. *Engineering Applications of Artificial Intelligence*, 127, 107228.

28. Utku, A., Kaya, M., & Canbay, Y. (2025). A new hybrid ConvViT model for dangerous farm insect detection. *Applied Sciences*, 15(5), 2518.

29. Ejaz, M. H., Bilal, M., Habib, U., & Attique, M. (2025). *Crop pest classification using deep learning techniques: A review*. arXiv:2507.01494.

30. Doan, T.-N. (2025). A novel real-time insect detection system on mobile smart devices. *Journal of Advances in Information Technology*, 16(5), 655–665.

31. Nguyen, H.-Q., Truong, T.-D., Nguyen, X.-B., Dowling, A., Li, X., & Luu, K. (2024). Insect-Foundation: A foundation model and large-scale 1M dataset for visual insect understanding. In *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*.

32. Oquab, M., Darcet, T., Moutakanni, T., Vo, H., Szafraniec, M., Khalidov, V., et al. (2024). DINOv2: Learning robust visual features without supervision. *Transactions on Machine Learning Research (TMLR)*. arXiv:2304.07193.

33. Bộ Nông nghiệp và Phát triển nông thôn. (2010). *QCVN 01-38:2010/BNNPTNT — Quy chuẩn kỹ thuật quốc gia về phương pháp điều tra phát hiện dịch hại cây trồng*.

34. Bộ Nông nghiệp và Phát triển nông thôn. (2023). *Thông tư 04/2023/TT-BNNPTNT ban hành Danh mục đối tượng kiểm dịch thực vật của nước Cộng hòa xã hội chủ nghĩa Việt Nam*.

35. Cục Trồng trọt và Bảo vệ thực vật. *Báo cáo tình hình sinh vật gây hại cây trồng* (các kỳ báo cáo định kỳ). Bộ Nông nghiệp và Môi trường. (Cơ quan được thành lập trên cơ sở hợp nhất Cục Trồng trọt và Cục Bảo vệ thực vật theo Quyết định 58/QĐ-BNNMT năm 2025; các báo cáo trước 2025 mang tên Cục Bảo vệ thực vật, Bộ Nông nghiệp và Phát triển nông thôn.)

36. Qin, D., Leichner, C., Delakis, M., Fornoni, M., Luo, S., Yang, F., et al. (2024). MobileNetV4: Universal models for the mobile ecosystem. In *European Conference on Computer Vision (ECCV)*. arXiv:2404.10518.

37. Wu, K., Zhang, J., Peng, H., Liu, M., Xiao, B., Fu, J., & Yuan, L. (2022). TinyViT: Fast pretraining distillation for small vision transformers. In *European Conference on Computer Vision (ECCV)*, pp. 68–85.

38. Vasu, P. K. A., Gabriel, J., Zhu, J., Tuzel, O., & Ranjan, A. (2023). FastViT: A fast hybrid vision transformer using structural reparameterization. In *Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)*, pp. 5785–5795. arXiv:2303.14189.

39. Zheng, C., et al. (2025). iFormer: Integrating ConvNet and Transformer for mobile application. In *International Conference on Learning Representations (ICLR)*. arXiv:2501.15369.

40. Gu, J., et al. (2025). BioCLIP 2: Emergent properties from scaling hierarchical contrastive learning. In *Advances in Neural Information Processing Systems (NeurIPS)*, Spotlight. arXiv:2505.23883.

### ***Ghi chú về danh mục tài liệu tham khảo***

Danh mục trên gồm **40 tài liệu**, trong đó **23 tài liệu thuộc giai đoạn 2024–2026**, vượt yêu cầu tối thiểu của phiếu mô tả đề tài (25 tài liệu, ít nhất 10 thuộc 2024–2026).

Ba mục đã bị **loại bỏ hoặc thay thế** trong quá trình rà soát vì không xác minh được nguồn gốc học thuật: một mục về Dual-Stream Vision Transformer trên IP102 (nguồn dẫn thực chất là một truy vấn tìm kiếm kho mã, các con số không truy vết được về công bố có phản biện) và một mục về phương pháp ADCGAN (không xác minh được tác giả lẫn tạp chí). Hai mục này được thay bằng [18] và [16]/[17].

Bốn mục [10], [13], [15] và [30] trong bản đề cương trước thiếu tên tác giả và nay đã được bổ sung đầy đủ từ nguồn gốc. Riêng [15] cũng được sửa lại năm công bố (2026, không phải 2025) và được đặt lại đúng vai trò: đây là một nghiên cứu về phân loại phấn hoa, dùng làm tham chiếu phương pháp luận cho việc so sánh kiến trúc trên ảnh sinh học mịn, **không** phải bằng chứng cho nhận định "khi dữ liệu ít thì CNN vượt ViT" — bằng chứng cho nhận định đó là [13].

Bốn lưu ý khi trích dẫn trong báo cáo:

> * Con số **87,62%** của [8] đo trên tập con Pest37, **không phải** IP102 đầy đủ — bắt buộc ghi kèm điều kiện này mỗi lần trích.

> * Con số **77,1%** của EfficientNet-B0 trong [3] là trên ImageNet, không phải IP102; phải tách rõ hai loại số liệu.

> * ViT-Tiny **không có** trong bài gốc Vision Transformer [4]; khi dùng ViT-Tiny phải dẫn DeiT [5] hoặc thư viện `timm`. Con số đúng của DeiT-Tiny chỉ với ImageNet-1K là **72,2%** không có token chưng cất và **74,5%** có token chưng cất, ở 300 epoch.

> * Con số **78,4%** của [11] là kết quả cao nhất đã xác minh trên IP102 đầy đủ; con số **84,25%** của [24] cao bất thường và nhiều khả năng dùng phép chia khác — **không đưa vào bảng mốc**.

**Số liệu chuẩn của IP102 dùng thống nhất trong toàn báo cáo:** 75.222 ảnh · 102 lớp · 45.095 train / 7.508 val / 22.619 test · lớp lớn nhất `Cicadellidae` 5.740 ảnh · lớp nhỏ nhất `Erythroneura apicalis` 71 ảnh · tỷ lệ mất cân bằng 80,8 lần · baseline gốc 2019 đạt 49,4% · kết quả cao nhất đã xác minh 78,4% (2025).
