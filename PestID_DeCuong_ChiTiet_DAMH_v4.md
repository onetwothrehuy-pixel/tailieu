**TRƯỜNG ĐẠI HỌC CÔNG NGHIỆP TP. HỒ CHÍ MINH**

**KHOA CÔNG NGHỆ THÔNG TIN**

**ĐỀ CƯƠNG ĐỒ ÁN MÔN HỌC**

**XÂY DỰNG HỆ THỐNG NHẬN DIỆN ĐỐI TƯỢNG GÂY HẠI NÔNG NGHIỆP BẰNG HỌC SÂU**

*(PestID: A Deep Learning-Based Agricultural Pest Classification System)*

**Ngành/Chuyên ngành:** KHOA HỌC MÁY TÍNH      Mã chuyên ngành: ……………

**GIẢNG VIÊN HƯỚNG DẪN:** Lê Thị Vĩnh Thanh — Khoa Công nghệ Thông tin, Trường Đại học Công nghiệp TP. Hồ Chí Minh

**SVTH – MSSV:** [Họ và tên sinh viên thực hiện – MSSV]

**LỚP:** […………] **KHÓA:** [……]

**THÀNH PHỐ HỒ CHÍ MINH, NĂM 2026**

---

# **MỤC LỤC**

> * PHẦN MỞ ĐẦU

> * BỐ CỤC ĐỒ ÁN MÔN HỌC (DỰ KIẾN)

> * TIẾN ĐỘ THỰC HIỆN

> * TÀI LIỆU THAM KHẢO

---

# **PHẦN MỞ ĐẦU**

## **1. Lý do chọn đề tài**

Nhận diện đúng đối tượng gây hại quyết định trực tiếp đến việc chọn biện pháp phòng trừ, hoạt chất và thời điểm can thiệp. Trong thực tế sản xuất tại Việt Nam, nông dân thường dựa vào kinh nghiệm cá nhân hoặc chờ cán bộ bảo vệ thực vật, trong khi điện thoại thông minh đã phổ biến tới tận hộ canh tác và hoàn toàn có thể trở thành công cụ tra cứu nhanh ngay ngoài đồng ruộng. Bài toán đặt ra cho đề tài là: cho một ảnh RGB chụp bằng điện thoại phổ thông, hệ thống trả về lớp dự đoán top-1, ba ứng viên top-3, độ tin cậy, cờ cảnh báo khi mô hình không đủ chắc chắn, và toàn bộ quá trình suy luận diễn ra trên thiết bị, không phụ thuộc máy chủ.

IP102 là bộ dữ liệu chuẩn của lĩnh vực này, gồm 75.222 ảnh thuộc 102 lớp trên tám nhóm cây trồng, chia sẵn 45.095 train / 7.508 validation / 22.619 test, với lớp lớn nhất *Cicadellidae* 5.740 ảnh và lớp nhỏ nhất *Erythroneura apicalis* 71 ảnh, tương ứng tỷ lệ mất cân bằng 80,8 lần [1]. Baseline ResNet-50 công bố cùng bộ dữ liệu chỉ đạt 49,4%; sau sáu năm, kết quả cao nhất mà nhóm xác minh được trên IP102 đầy đủ là 78,4% [11] (tính đến tháng 8/2026). Mức tăng khoảng bốn điểm trong bốn năm, dù độ phức tạp kiến trúc tăng nhiều lần, cho thấy cải tiến kiến trúc đơn thuần đã tới hạn: dữ liệu, giao thức huấn luyện và cách đánh giá phải được kiểm soát đồng thời.

Ba hạn chế của IP102 đặc biệt đáng chú ý. Thứ nhất, dữ liệu có phân bố đuôi dài nghiêm trọng và biến thiên rất lớn giữa các giai đoạn vòng đời trong cùng một lớp. Thứ hai, bộ dữ liệu chứa nhiều ảnh gần trùng; khảo sát sơ bộ của nhóm trên 45 lớp ứng viên ghi nhận khoảng 14% ảnh validation/test gần trùng với ảnh train theo phép chia gốc, nghĩa là phần lớn kết quả đã công bố có nguy cơ bị thổi phồng bởi rò rỉ dữ liệu. Thứ ba, nhãn không đồng nhất về cấp phân loại, một số nhãn ở cấp chi hoặc họ, nên đề cương dùng thuật ngữ "lớp đối tượng gây hại" thay vì khẳng định mọi nhãn đều là một loài côn trùng.

IP102 cũng không bao phủ đầy đủ bối cảnh Việt Nam. Hai đối tượng quan trọng là sâu keo mùa thu *Spodoptera frugiperda* và rầy chổng cánh *Diaphorina citri* không có lớp tương ứng. Trong khi đó, hệ thống văn bản quản lý nhà nước về bảo vệ thực vật lại khá đầy đủ và có thể khai thác làm căn cứ chọn lớp: quy chuẩn về phương pháp điều tra phát hiện dịch hại [33], Danh mục đối tượng kiểm dịch thực vật [34] và các báo cáo tình hình sinh vật gây hại định kỳ [35]. Đây là nguồn mà chưa nghiên cứu học sâu nào trên IP102 sử dụng.

Cuối cùng, các mô hình học sâu triển khai trên điện thoại thường chỉ được báo cáo theo Accuracy, hiếm khi kiểm tra mức tin cậy của confidence (calibration) hay khả năng từ chối trả lời khi gặp lớp chưa học. Với một công cụ mà kết quả sai có thể dẫn tới lựa chọn hoạt chất sai, đây là thiếu sót nghiêm trọng.

Xuất phát từ đó, đề tài xây dựng PestID theo bốn hướng: làm sạch và gom nhóm ảnh gần trùng trên toàn bộ IP102 trước khi tạo bất kỳ tập con nào; chọn tập lớp theo một bảng xếp hạng bối cảnh Việt Nam được khóa trước thực nghiệm, kèm hai tập đối chứng; sàng lọc tuần tự balancing, augmentation và kiến trúc để mỗi giai đoạn chỉ thay đổi một nhóm biến; và chưng cất mô hình mạnh sang mạng nhẹ, đánh giá trên ảnh thực địa cùng mẫu ngoài tập, lượng tử hóa rồi đo trên điện thoại thật. Đề tài vì vậy vừa có ý nghĩa khoa học ở chỗ tách bạch được nguồn gốc của cải thiện, vừa có giá trị thực tiễn ở một sản phẩm chạy ngoại tuyến ngay tại ruộng.

## **2. Mục tiêu nghiên cứu**

### ***2.1. Mục tiêu chính***

Xây dựng và đánh giá hệ thống PestID trên tập chính **IP102-VN-K20 (K20-VN)** gồm 20 lớp được chọn theo mức độ phù hợp với sản xuất nông nghiệp Việt Nam. Chỉ số cam kết chính là **Macro-F1 trên Natural Test**, đặt theo ba mức: mức đạt ≥ 0,78, mức tốt ≥ 0,83, mức xuất sắc ≥ 0,88. Accuracy trên tập kiểm thử cân bằng (50 ảnh thật mỗi lớp) được báo cáo kèm như chỉ tiêu tham chiếu với kỳ vọng 0,82–0,88. Ngưỡng này được đặt thấp hơn mốc 87,62% của [8] một cách có chủ ý: [8] đo trên tập con Pest37 theo phép chia gốc IP102, trong khi PestID loại bỏ ảnh gần trùng bằng group-aware split, nên hai con số không cùng giao thức và không so sánh trực tiếp được.

Phiên bản triển khai phải có kích thước mô hình không quá 15 MB, gói cài đặt không quá 50 MB, độ trễ suy luận trung vị không quá 200 ms mỗi ảnh và độ trễ đầu-cuối từ lúc bấm chụp không quá 500 ms trên hai điện thoại Android tầm trung (chip Snapdragon 6-series hoặc Helio G-series, RAM 4–6 GB, 4 luồng CPU, mô hình đã nạp sẵn), đồng thời hoạt động hoàn toàn khi không có kết nối mạng.

K20-FG chỉ là tập kiểm tra độ bền trong điều kiện các lớp giống nhau hơn về hình thái, không áp dụng cam kết Macro-F1 nêu trên. Các mốc K15, K35 và K45 dùng để khảo sát ảnh hưởng của số lượng lớp và không phải mục tiêu ứng dụng chính.

### ***2.2. Mục tiêu cụ thể***

Bảy câu hỏi nghiên cứu RQ1–RQ7 được trả lời bằng mười thí nghiệm E1–E10 mô tả tại Mục 5.4.

> 1. Xây dựng **Master Clean Pool** từ toàn bộ 102 lớp IP102: phát hiện ảnh hỏng, ảnh quá nhỏ, ảnh trùng byte, ảnh gần trùng và nhãn mâu thuẫn; khóa manifest cùng phép chia group-aware 60/15/25 trước khi huấn luyện; đo lượng rò rỉ của phép chia gốc để định lượng mức thổi phồng kết quả (RQ1).

> 2. Xây dựng ba nhánh dữ liệu có vai trò tách biệt: **K20-VN** định hướng ứng dụng, **K20-Count** gồm 20 lớp nhiều ảnh sạch nhất để đối chứng cách chọn lớp, và **K20-FG** để kiểm tra khả năng tổng quát hóa khi độ tương đồng hình thái tăng, với ràng buộc khớp tỷ lệ mất cân bằng và tổng ảnh train so với K20-VN. K15, K20, K35, K45 là bốn tập lồng nhau sinh từ một bảng xếp hạng bối cảnh Việt Nam duy nhất (RQ2).

> 3. Sàng lọc có kiểm soát bảy cấu hình xử lý mất cân bằng và ba mức tăng cường dữ liệu trên EfficientNet-B0, sau đó khóa **Training Protocol v1** trước khi so sánh kiến trúc (RQ3).

> 4. So sánh bảy kiến trúc nền gồm MobileNetV2 [2], EfficientNet-B0 [3], DeiT-Tiny [5], MobileNetV4-Conv-S [36], TinyViT-5M [37], FastViT-T12 [38] và iFormer-M [39] trên cùng dữ liệu, phép chia và giao thức đã khóa; kiểm tra độ bền của phương pháp trên K20-FG và khi số lớp tăng từ 15 lên 45 (RQ4).

> 5. Xây dựng **PestID-KD** với teacher là một mô hình mạnh nằm ngoài nhóm kiến trúc triển khai và student là mô hình nhẹ tốt nhất; thực hiện ablation để tách đóng góp của chưng cất khỏi đóng góp của tăng cường dữ liệu (RQ5).

> 6. Đánh giá trên Natural Test, test_balanced, K20-FG, ảnh thực địa Việt Nam và bộ ngoài tập; báo cáo Macro-F1, balanced accuracy, Accuracy, macro precision, macro recall, Head/Medium/Tail recall, ECE, khoảng tin cậy bootstrap có phân tầng, ma trận nhầm lẫn và bản đồ nhiệt giải thích; đo khả năng từ chối dự đoán khi gặp lớp ngoài K20-VN (RQ6).

> 7. Chuyển mô hình sang TensorFlow Lite INT8, so sánh PTQ với QAT, hiệu chỉnh lại ngưỡng từ chối và ECE **trên chính bản INT8 sẽ triển khai**, đo kích thước và độ trễ trên điện thoại thật, sau đó tích hợp vào ứng dụng Flutter có top-3, cờ không chắc chắn và dòng cảnh báo bắt buộc (RQ7).

## **3. Đối tượng và phạm vi nghiên cứu**

### ***3.1. Đối tượng nghiên cứu***

Đối tượng nghiên cứu gồm ba phương diện: (i) ảnh của các đối tượng gây hại trong IP102 [1] cùng các đặc tính gây khó cho bài toán là phân bố đuôi dài, ảnh gần trùng, nhãn không đồng nhất cấp phân loại và biến thiên vòng đời; (ii) bảy kiến trúc học sâu đại diện cho mạng tích chập nhẹ, Vision Transformer [4] cỡ nhỏ và kiến trúc lai, cùng các kỹ thuật xử lý mất cân bằng, tăng cường dữ liệu, chưng cất tri thức và lượng tử hóa; (iii) độ tin cậy của mô hình khi triển khai, gồm calibration, khả năng từ chối dự đoán và độ suy giảm khi chuyển từ ảnh benchmark sang ảnh thực địa.

### ***3.2. Phạm vi không gian***

Dữ liệu chính lấy từ IP102, một bộ dữ liệu công khai. K20-VN được chọn theo bối cảnh sản xuất Việt Nam; K20-Count là tập đối chứng theo số lượng ảnh; K20-FG là tập stress test khóa bằng tiêu chí hình thái và phân loại học trước khi xem kết quả mô hình. Nhóm tự thu 120–200 ảnh thực địa bằng điện thoại phổ thông tại các vùng canh tác ở khu vực phía Nam, chỉ dùng để đánh giá, không đưa vào huấn luyện. Đề tài không thu dữ liệu thời gian thực từ các nền tảng có kiểm duyệt API chặt.

Huấn luyện thực hiện trên Kaggle Notebook với hai GPU NVIDIA T4 16 GB. Hai GPU chạy hai thí nghiệm độc lập song song thay vì DistributedDataParallel, nhờ đó nhân đôi thông lượng trên mỗi giờ hạn mức. Mọi lượt chạy lưu checkpoint theo epoch để chịu được giới hạn thời lượng một phiên, kèm cấu hình, seed và mã commit.

### ***3.3. Phạm vi thời gian***

Đồ án thực hiện trong 14 tuần, từ ngày 01/9/2026 đến 07/12/2026. Nghiên cứu sử dụng dữ liệu tĩnh, không theo dõi diễn biến dịch hại theo mùa vụ. Việc thu ảnh thực địa được bắt đầu ngay tuần 1 và chạy song song suốt kỳ, do sự xuất hiện của đối tượng gây hại phụ thuộc mùa vụ và không thể dồn vào một tuần.

### ***3.4. Phạm vi nội dung***

Đề tài tập trung vào **phân loại ảnh đơn nhãn**, không thực hiện phát hiện vị trí dù IP102 có nhánh dữ liệu phát hiện [9][10], không phân đoạn, không dự báo mật độ và không khuyến nghị thuốc bảo vệ thực vật. K20-VN là tập ứng dụng chính; K20-FG chỉ dùng kiểm tra độ bền. Nhánh ngữ cảnh cây trồng (PestID-CropGate) được chuyển sang hướng phát triển để tránh mở rộng quá mức khối lượng thực nghiệm.

Đầu vào là một ảnh RGB định dạng JPEG hoặc PNG với cạnh nhỏ nhất tối thiểu 112 pixel; ảnh được resize cạnh ngắn về 256, center crop 224 × 224 và chuẩn hóa theo ImageNet. Sàn 112 pixel được đặt cao hơn mức 64 pixel thông thường để tránh phóng ảnh quá 2,3 lần; phân bố kích thước ảnh theo nhóm Head/Medium/Tail được báo cáo để kiểm tra xem ảnh nhỏ có tập trung ở lớp hiếm hay không. Đầu ra gồm `model_class_index` (0–19), `ip102_id`, `display_name_vi`, `top1`, `top3` kèm độ tin cậy, cờ `khong_chac_chan` khi confidence dưới ngưỡng đã khóa, và dòng cảnh báo kết quả chỉ mang tính tham khảo.

Hệ thống không nhận diện được các lớp chưa học, bao gồm sâu keo mùa thu và rầy chổng cánh. Giới hạn này được xử lý bằng cơ chế từ chối dự đoán, không bằng cách gán ép một lớp K20-VN. Kết quả không thay thế chẩn đoán của cán bộ bảo vệ thực vật.

## **4. Tổng quan tình hình nghiên cứu liên quan**

Nhằm xác định khoảng trống nghiên cứu và làm rõ tính mới của đề tài, các công trình liên quan đến nhận diện đối tượng gây hại nông nghiệp bằng học sâu, đặc biệt trên IP102, được khảo sát theo hai nhóm: nghiên cứu quốc tế và nghiên cứu trong nước.

### ***4.1. Các nghiên cứu quốc tế***

Nghiên cứu quốc tế tập trung gần như tuyệt đối vào IP102 và đi theo bốn hướng: nâng cấp kiến trúc, xử lý phân bố đuôi dài, làm nhẹ mô hình để triển khai, và so sánh CNN với Transformer.

> * **Wu và cộng sự (2019)** giới thiệu IP102 với 75.222 ảnh, 102 lớp, phân bố đuôi dài tự nhiên và chia sẵn train/val/test. Các baseline ResNet-50, VGG, DenseNet chỉ đạt 49,4–67%, xác lập đây là bài toán rất khó và đặt nền cho mọi nghiên cứu sau [1].

> * **Ung và cộng sự (2021)** kết hợp nhiều CNN với attention và feature pyramid, đạt 74,13% — vượt kết quả tốt nhất lúc đó khoảng 7 điểm, nhưng chi phí tính toán của ensemble khiến hướng này khó triển khai trên di động [6].

> * **Setiawan và cộng sự (2022)** xây dựng khung huấn luyện tối ưu cho MobileNetV2 với learning rate động, CutMix, đóng băng theo tầng và điều chuẩn thưa, đạt khoảng 75,6% với chỉ 4,2 triệu tham số. Kết luận đáng chú ý: chiến lược tăng cường dữ liệu và điều chuẩn quan trọng hơn việc tăng kích thước mô hình [7].

> * **Peng và Wang (2022)** nối backbone CNN với đầu phân loại Transformer, đạt 74,897% ở 224px và 75,583% ở 480px. Chênh 0,7 điểm nhưng chi phí gấp khoảng 4,6 lần — căn cứ định lượng cho việc giữ độ phân giải 224px [20].

> * **Peng và cộng sự (2024)** cải tiến MobileNetV2 thành PestNet, đạt 87,62% accuracy và 86,90% F1 trên tập con **Pest37** (37 lớp trích từ IP102) trong khi giảm 14,1% tham số và 37,5% FLOPs. Đây là mốc tham chiếu quan trọng nhưng đo trên tập con theo phép chia gốc, nên không so sánh trực tiếp được với kết quả của đề tài [8].

> * **Qian và cộng sự (2025)** đạt 75,74% accuracy / 75,38% F1, và giá trị lớn nhất nằm ở phần phân tích: nhóm công bố độ chính xác tách theo Head 72,87% / Medium 77,29% / Tail 75,79%, tức nhóm lớp nhiều mẫu lại kém hơn nhóm ít mẫu do quá khớp, với nguyên nhân gốc là IP102 gộp bốn giai đoạn vòng đời vào một lớp. Nhóm tác giả đề xuất dùng mô hình của họ làm teacher để chưng cất sang MobileNet nhưng chưa thực hiện [18].

> * **Zou và cộng sự (2025)** với AdaptPest-Net đạt 78,4%, kết quả cao nhất đã xác minh trên IP102 đầy đủ. Cùng hướng kiến trúc phức tạp còn có Pest-ConFormer 77,81% [19], InsectMamba [21], DeWi 76,44% [26] và DWViT-ES 76,00% [27]. Điểm chung là mức tăng chỉ khoảng bốn điểm trong bốn năm [11].

> * **Cảnh báo về giao thức so sánh.** Angelescu và cộng sự (2025) công bố F1 84,25% bằng ensemble Vision Transformer, cao bất thường so với dải 74–78% của mọi công trình khác, nhiều khả năng do phép chia hoặc tập con khác [24]. Trường hợp này cho thấy bảng mốc kết quả chỉ được đưa vào các con số cùng giao thức đánh giá.

> * **Hướng xử lý đuôi dài** có Chen và cộng sự (2024) dùng tăng cường theo thể hiện kết hợp tinh chỉnh đặc trưng có ràng buộc, cải thiện 5,73 điểm so với baseline ViT [16]; Wang (2025) thay GAN bằng mô hình khuếch tán để sinh ảnh cho lớp hiếm [17]. Cả hai cải thiện lớp hiếm nhưng chưa giải quyết đồng thời hiện tượng quá khớp ở lớp nhiều mẫu. Ở tầng phương pháp tổng quát, Kang và cộng sự [41] khảo sát có hệ thống natural, class-balanced, square-root và progressively-balanced sampling và chỉ ra biểu diễn học từ phân bố tự nhiên vẫn mạnh, việc cân bằng có thể dời sang classifier; Cui và cộng sự [42] đề xuất số mẫu hiệu dụng; Ren và cộng sự [43] đề xuất Balanced Softmax; Menon và cộng sự [50] đề xuất logit adjustment hậu xử lý không tốn thêm huấn luyện.

> * **Hướng làm nhẹ mô hình.** Zhang và cộng sự (2024) chứng minh chưng cất tri thức tạo được mô hình nhẹ cho bài toán sâu bệnh hại [22]; Akhtar và cộng sự (2025) so sánh 18 mô hình qua ba chế độ lượng tử hóa, ghi nhận QAT giảm 75,59% kích thước và MobileNetV2 cho tỷ lệ hiệu năng/kích thước tốt nhất [14]; Kang và cộng sự (2023) triển khai mạng chưng cất trên thiết bị biên [23]; Doan (2025) xây dựng hệ thống phát hiện côn trùng thời gian thực chạy trực tiếp trên điện thoại [30].

> * **Hướng so sánh CNN với Transformer.** Jeevan và Sethi (2024) kết luận các kiến trúc dựa trên attention thường kém hơn mạng tích chập khi tinh chỉnh với ít dữ liệu [13]; Struniawski và cộng sự (2026) trên bài toán phân loại phấn hoa ghi nhận khi tách được cá thể khỏi nền thì cả hai họ kiến trúc đều hội tụ về mức phân biệt gần như hoàn hảo, nghĩa là khoảng cách CNN–ViT phụ thuộc mạnh vào chất lượng dữ liệu [15]. Touvron và cộng sự (2021) cho thấy DeiT-Tiny 5,7 triệu tham số đạt 74,5% top-1 ImageNet nhờ token chưng cất, so với 72,2% khi không dùng — cặp số này là bằng chứng định lượng gần nhất cho giả thuyết PestID-KD, với lưu ý teacher của DeiT là một CNN lớn hơn nhiều chứ không cùng cỡ [5]. Ở chiều ngược lại, EfficientFormerV2 cho thấy ViT được tối ưu có thể vượt MobileNetV2 3,5–4 điểm với độ trễ tương đương [12].

> * **Hướng khai thác quy trình sử dụng.** Chen và cộng sự (2023) nhận dạng dựa trên nhiều ảnh của cùng một cá thể kết hợp lọc thích nghi, đạt 73,9%; cải thiện đến từ cách sử dụng chứ không từ độ lớn mô hình nên gần như miễn phí về kích thước triển khai [25]. Utku và cộng sự (2025) thử nghiệm kiến trúc lai CNN + ViT trên một tập 15 lớp côn trùng, là tham chiếu gần nhất về cách thiết kế thực nghiệm cho tập con cỡ hai chục lớp [28].

### ***4.2. Các nghiên cứu trong nước***

Nghiên cứu trong nước về bài toán này còn hạn chế cả về số lượng lẫn phạm vi.

> * **Ung, Ung và Nguyễn (2021)** là công trình trong nước nổi bật nhất về IP102, đạt 74,13% bằng ensemble CNN kết hợp attention và feature pyramid, sử dụng nguyên bộ 102 lớp và không điều chỉnh theo bối cảnh dịch hại trong nước [6].

> * **Nguyễn và cộng sự (2024)** tiếp nối với DeWi, đạt 76,44%. Đóng góp nằm ở chiến lược huấn luyện (triplet margin kết hợp Mixup) chứ không ở kiến trúc mới, nên chi phí cài đặt thấp, phù hợp điều kiện của một nhóm sinh viên [26].

> * **Doan (2025)** xây dựng hệ thống phát hiện côn trùng thời gian thực trên thiết bị di động — công trình trong nước gần nhất với phần sản phẩm của đề tài, cho thấy hướng triển khai trên điện thoại là khả thi [30].

> * Ngoài ba công trình trên, phần lớn nghiên cứu về sâu bệnh hại tại Việt Nam tập trung vào bệnh trên lá lúa, lá cây ăn quả hoặc dùng bộ dữ liệu nhỏ tự thu; chưa hình thành một bộ dữ liệu ảnh côn trùng gây hại của Việt Nam được công bố mở.

> * Ở khía cạnh cơ sở dữ liệu bối cảnh, hệ thống văn bản quản lý nhà nước về bảo vệ thực vật khá đầy đủ và có thể khai thác làm căn cứ chọn lớp [33][34][35]. Đây là nguồn chưa nghiên cứu học sâu nào về IP102 sử dụng.

### ***4.3. Nhận xét chung và khoảng trống nghiên cứu***

Trong phạm vi 50 tài liệu được khảo sát, đề tài xác định sáu khoảng trống liên quan trực tiếp đến thiết kế thực nghiệm.

> 1. IP102 có phân bố đuôi dài, nhãn không đồng nhất và nguy cơ ảnh gần trùng xuyên tập, nhưng chưa có quy trình công khai kết hợp làm sạch, gom nhóm và chia group-aware trên toàn bộ dữ liệu, cũng chưa có công bố định lượng mức rò rỉ của phép chia gốc.

> 2. Các tập con IP102 thường được chọn theo số lượng ảnh, chưa phản ánh nhu cầu nhận diện của một quốc gia cụ thể, và chưa có đối chứng giữa hai cách chọn lớp.

> 3. Nhiều nghiên cứu chọn trước một sampler, loss hoặc augmentation rồi mới so mô hình, nên khó biết cải thiện đến từ dữ liệu, giao thức huấn luyện hay kiến trúc. Các phương pháp long-tailed [41][42][43][50] chưa được đánh giá tuần tự trên một tập IP102 chọn theo bối cảnh Việt Nam.

> 4. IP102 thường được gọi chung là fine-grained, trong khi độ tương đồng hình thái giữa các tập lớp rất khác nhau. Chưa có kiểm tra tách biệt giữa tập định hướng ứng dụng và một tập stress test fine-grained được khóa trước và khớp về mức mất cân bằng.

> 5. Chưng cất tri thức đã được đề xuất cho mô hình sâu bệnh hại [22] và có nền tảng từ phương pháp teacher–student [49], nhưng chưa được kiểm chứng dưới một giao thức đã khóa, không rò rỉ, với cổng chênh lệch teacher–student đăng ký trước.

> 6. Thiếu đánh giá đồng thời trên ảnh thực địa, mẫu ngoài tập và thiết bị di động thật; confidence thường được báo cáo mà không kiểm tra calibration [48], và hầu như không nghiên cứu nào hiệu chỉnh lại ngưỡng sau khi lượng tử hóa.

Đề tài xử lý các khoảng trống này bằng Master Clean Pool kèm phép đo rò rỉ, ba nhánh K20 có vai trò tách biệt, giao thức sàng lọc tuần tự, K20-FG stress test khớp mức mất cân bằng, PestID-KD với teacher ngoài nhóm triển khai, đánh giá ECE và cơ chế từ chối, cùng phép đo trên ảnh thực địa và điện thoại thật.

## **5. Phương pháp nghiên cứu**

Phương pháp được tổ chức theo chuỗi Dữ liệu → Balancing → Augmentation → Kiến trúc → Stress test → Số lớp → Chưng cất → Final Test → Triển khai. Mỗi giai đoạn chỉ thay đổi một nhóm biến và mọi quyết định phát triển đều dựa trên train/validation.

### ***5.1. Phương pháp nghiên cứu lý thuyết***

Đề tài phân tích và tổng hợp các công trình về IP102, phân loại ảnh có thành phần fine-grained, học chuyển giao, nhận dạng dữ liệu đuôi dài, augmentation, calibration, chưng cất tri thức và triển khai di động, dùng bài tổng quan gần đây làm khung đối chiếu [29]. Các kiến trúc được đối chiếu theo Macro-F1, số tham số, FLOPs, độ trễ và khả năng chuyển đổi sang TensorFlow Lite. Phương pháp balancing dựa trên nguyên lý của square-root sampling, effective number, Balanced Softmax và logit adjustment [41][42][43][50]; augmentation dùng RandAugment, Random Erasing, MixUp và CutMix [44][45][46][47].

Chuỗi thực nghiệm tuần tự, cơ chế khóa tập stress test và các quy tắc dừng của PestID là **thiết kế thực nghiệm của đề tài**, xây dựng dựa trên nguyên lý đã công bố ở [41]–[50], không được trình bày như thuật toán trích nguyên từ một bài báo.

### ***5.2. Phương pháp thu thập và chọn lọc dữ liệu***

**Bảng xếp hạng bối cảnh Việt Nam.** Toàn bộ 102 lớp IP102 được chấm theo ba tiêu chí trên thang 0–3, có rubric neo vào văn bản quản lý nhà nước để giảm tính chủ quan:

| Tiêu chí | Trọng số | Rubric |
|---|:---:|---|
| **P** — Mức phổ biến | 40% | 3: xuất hiện ≥ 6 kỳ báo cáo sinh vật gây hại [35] trong 12 tháng gần nhất; 2: 3–5 kỳ; 1: 1–2 kỳ; 0: không xuất hiện |
| **A** — Mức tác động nông nghiệp | 35% | 3: có trong QCVN 01-38 [33] hoặc Danh mục kiểm dịch [34] và gây hại cây trồng chủ lực; 2: gây hại cây trồng chủ lực nhưng ngoài hai văn bản; 1: gây hại cây trồng thứ yếu; 0: chưa ghi nhận tại Việt Nam |
| **D** — Mức đủ dữ liệu | 25% | 3: ≥ 600 ảnh sạch; 2: 300–599; 1: 200–299; 0: < 200 (loại) |

Điểm tổng bằng `8P + 7A + 5D`. Khi hòa điểm, ưu tiên D cao hơn, rồi số ảnh sạch nhiều hơn, rồi IP102 ID nhỏ hơn. **Hai thành viên chấm độc lập toàn bộ 102 lớp**, báo cáo mức đồng thuận bằng Krippendorff's alpha (thang thứ bậc), rà chung các dòng bất đồng, sau đó khóa bảng kèm checksum. Bảng đầy đủ 102 dòng được đưa vào phụ lục báo cáo. Bốn tập lồng nhau K15 ⊂ K20-VN ⊂ K35 ⊂ K45 sinh ra từ bảng này.

**Hai tập đối chứng.** K20-Count gồm 20 lớp nhiều ảnh sạch nhất, chỉ dùng đối chứng cách chọn lớp. K20-FG gồm 20 lớp đạt sàn dữ liệu và có mức tương đồng hình thái cao, chấm kép trên ba trục thang 0–3: mức gần nhau về phân loại học (cùng chi / cùng họ / cùng bộ / khác bộ), độ giống về hình dạng và màu sắc trên 10 ảnh mẫu ngẫu nhiên mỗi lớp, và mức chồng lấn giai đoạn vòng đời. K20-FG phải đồng thời thỏa **ràng buộc khớp**: tỷ lệ mất cân bằng nằm trong ±20% và tổng ảnh train trong ±15% so với K20-VN; nếu không đạt, tạo thêm bản `K20-FG-matched` bằng cách hạ mẫu cho khớp số ảnh mỗi lớp của K20-VN và báo cáo cả hai. Ràng buộc này bảo đảm chênh lệch quan sát được ở E6 quy về độ tương đồng hình thái chứ không về mức mất cân bằng. Danh sách và điểm được khóa trước E2.

**Ảnh thực địa và bộ ngoài tập.** Nhóm thu 120–200 ảnh bằng điện thoại phổ thông, bắt đầu từ tuần 1 và duy trì suốt kỳ; mỗi cá thể có tối thiểu một ảnh cận cảnh và một ảnh kèm cây ký chủ. Hai thành viên gán nhãn độc lập bằng Label Studio, báo cáo Cohen's kappa; ảnh còn bất đồng được đánh dấu không xác định và loại khỏi đánh giá phân loại. Phân tích riêng theo lớp chỉ thực hiện cho lớp có ≥ 10 ảnh; với cỡ mẫu này, kết quả thực địa được trình bày là **mô tả, không phải suy luận thống kê**. Ảnh thuộc lớp ngoài K20-VN được lưu vào bộ ngoài tập thực địa. Do bộ này quá nhỏ để hiệu chỉnh ngưỡng, **ngưỡng từ chối được khóa bằng một bộ ngoài tập lớn dựng từ 82 lớp IP102 không thuộc K20-VN** (có sẵn trong manifest, chi phí bằng không), còn bộ thực địa chỉ dùng để báo cáo cuối. Mọi phép chia hiệu chỉnh/đánh giá đều thực hiện theo cá thể, không theo từng ảnh.

**Manifest lớp** lưu ít nhất sáu trường: `model_class_index`, `ip102_id`, `display_name_vi`, `original_ip102_name`, `canonical_taxon`, `taxonomy_status`. Việc tách chỉ số mô hình khỏi ID gốc tránh nhầm giữa đầu ra 0–19 và mã lớp không liên tục của IP102.

### ***5.3. Phương pháp xử lý dữ liệu***

Quy trình **C1–C7** thực hiện một lần trên toàn bộ 75.222 ảnh. C1 kiểm tra khả năng giải mã, chế độ màu và kênh alpha (ảnh có alpha được đặt lên nền trắng trước khi chuyển RGB); C2 loại ảnh có cạnh nhỏ hơn 112 pixel; C3 phát hiện ảnh trùng byte bằng MD5/SHA-256; C4 gom ảnh gần trùng bằng dHash 64-bit và MSE trên ảnh xám 32 × 32; C5 kiểm tra nhóm gần trùng đi qua ranh giới tập; C6 rà tay các nhóm gần trùng mang nhãn khác nhau; C7 chuẩn hóa bảng nhãn và ánh xạ chỉ số.

Một cặp được xem là gần trùng khi Hamming ≤ 2, hoặc Hamming ≤ 5 đồng thời MSE < 300. Việc gom nhóm dùng thành phần liên thông nhưng **giới hạn kích thước cụm ở 30 ảnh**; cụm vượt ngưỡng được siết tiêu chí và rà tay, tránh hiện tượng bắc cầu làm cụm phình to và phá vỡ phân tầng. Tiêu chí này chạy **cả trong lớp lẫn liên lớp**, vì ảnh gần trùng mang hai nhãn khác nhau chính là dấu hiệu nhãn mâu thuẫn. Phân bố kích thước cụm được báo cáo. Phương sai Laplacian dùng để gắn cờ ảnh mờ; ảnh không bị xóa tự động chỉ vì độ nét thấp, các trường hợp dưới ngưỡng được rà thủ công và nếu loại phải lưu lý do.

Master Clean Pool của cả 102 lớp được chia **một lần** theo tỷ lệ 60/15/25, phân tầng theo lớp và lấy nhóm ảnh gần trùng làm đơn vị chia (thuật toán `StratifiedGroupKFold`, sau đó ghép fold). Mọi tập K, K20-Count và K20-FG đều tạo bằng cách lọc manifest này, nên một ảnh luôn giữ nguyên split, checksum, đường dẫn và nhãn ở mọi tập con. Mỗi lớp trong các tập nghiên cứu chính phải có tối thiểu 100 ảnh train, 30 ảnh validation và 50 ảnh test; với tỷ lệ 60/15/25, ràng buộc thực tế là **≥ 200 ảnh sạch mỗi lớp**. Số lớp đạt sàn được kiểm tra ngay tuần 2; nếu chưa đủ 45 lớp, K45 được hạ xuống mức khả thi (K40) và phạm vi được công bố rõ.

Pipeline chính giữ toàn bộ ảnh train sạch và độc nhất, không áp trần 800. Hard cap 800 chỉ xuất hiện ở baseline B4 để kiểm tra việc bỏ dữ liệu Head có thực sự có lợi. Để tránh so nhầm ngân sách huấn luyện với tác dụng của phương pháp, **toàn bộ cấu hình B0–B7 cố định cùng tổng số bước gradient** thay vì cùng số epoch, và `num_samples` của mọi `WeightedRandomSampler` được đặt bằng kích thước tập train gốc. Tỷ lệ mất cân bằng thô của train K20-VN dự kiến gần 24,9 lần và được báo cáo lại bằng số đo thực tế sau khi khóa manifest.

Ngoài Natural Test, mỗi tập K có `test_balanced` gồm đúng 50 ảnh thật mỗi lớp lấy từ Natural Test, không augmentation, không oversampling, không ảnh tổng hợp; K20-VN vì thế có 1.000 ảnh.

Bản dựng phải vượt **12 kiểm tra chấp nhận**: (1) bảng hạng duy nhất, có checksum, không sửa sau khi khóa; (2) K15 ⊂ K20-VN ⊂ K35 ⊂ K45 đúng thứ hạng; (3) mọi lớp đạt sàn 100/30/50; (4) không checksum nào xuất hiện ở hai split; (5) không nhóm gần trùng nào bị cắt qua hai split; (6) mỗi ảnh giữ nguyên split/checksum/đường dẫn/nhãn ở mọi tập K; (7) ánh xạ `model_class_index ↔ ip102_id ↔ display_name_vi` là song ánh trong mỗi tập K; (8) không ảnh nào mang hai nhãn khác nhau trong toàn manifest; (9) tỷ lệ split thực tế mỗi lớp lệch không quá ±3 điểm so với 60/15/25; (10) `test_balanced` đủ 50 ảnh/lớp, lấy hoàn toàn từ Natural Test và không trùng ảnh; (11) K20-FG đạt ràng buộc khớp IR và tổng ảnh train với K20-VN; (12) dựng lại manifest từ `build_info.json` cho ra checksum giống hệt. Tệp `build_info.json` lưu seed, phiên bản bảng hạng, phiên bản manifest và checksum nguồn.

Để tránh nút thắt vào/ra trên Kaggle, toàn bộ ảnh sạch được resize sẵn về cạnh ngắn 256 và đóng gói thành một Kaggle Dataset trước khi huấn luyện. Ảnh đầu vào chốt ở 224 × 224 cho toàn bộ thí nghiệm.

**Hiểu dữ liệu (EDA).** EDA mô tả phân bố lớp, kích thước ảnh, độ mờ, ảnh trùng, trạng thái phân loại học, nhóm cây trồng, hệ số Gini và đường cong Lorenz. Sau khi manifest đã khóa, mọi EDA dựa trên mô hình chỉ dùng train và validation; test không được dùng để trích embedding, chọn lớp, chọn ngưỡng hay chọn checkpoint. Với K20, bảy lớp nhiều ảnh nhất là Head, sáu lớp tiếp theo là Medium, bảy lớp ít ảnh nhất là Tail; định nghĩa khóa trước khi xem kết quả. DINOv2 [32], BioCLIP 2 [40] và Insect-Foundation [31] dùng cho linear probe trên train/validation. Do BioCLIP 2 và Insect-Foundation huấn luyện trên corpus sinh học quy mô lớn và **có khả năng đã chứa IP102**, thành phần dữ liệu tiền huấn luyện của cả ba được kiểm tra và công bố; nếu có chồng lấn, DINOv2 là mô hình dùng để kết luận, hai mô hình còn lại chỉ tham khảo. t-SNE/UMAP, k-NN và Confident Learning chỉ tạo danh sách ảnh cần rà; mọi quyết định xóa hoặc sửa nhãn phải có kiểm tra thủ công và ghi vào phiên bản dữ liệu mới.

### ***5.4. Phương pháp xây dựng mô hình và tổ chức thực nghiệm***

Chương trình thực nghiệm gồm mười thí nghiệm, tổng khoảng **36 lượt huấn luyện** cộng ba lượt linear probe:

| Mã | Nội dung | RQ | Lượt |
|---|---|:---:|:---:|
| E1 | Đo rò rỉ của phép chia gốc; đối chứng group-aware split | RQ1 | 2 |
| E2 | K20-VN so với K20-Count; linear probe ba mô hình nền | RQ2 | 3 (probe) |
| E3 | Balancing screening B0–B7 trên EfficientNet-B0 | RQ3 | 6 |
| E4 | Augmentation screening AUG-0 đến AUG-2; khóa Training Protocol v1 | RQ3 | 3 |
| E5 | So sánh bảy kiến trúc; xác nhận Top-2 bằng ba seed | RQ4 | 11 |
| E6 | K20-FG stress test (FG0–FG2 × Top-2) | RQ4 | 6 |
| E7 | Khảo sát K15/K20/K35/K45 | RQ4 | 3 |
| E8 | PestID-KD: teacher ngoài nhóm triển khai, KD0–KD1 | RQ5 | 3 |
| E9 | Ảnh thực địa, bộ ngoài tập, ngưỡng từ chối | RQ6 | 0 |
| E10 | PTQ, QAT, đo trên điện thoại, hiệu chỉnh lại ngưỡng trên INT8 | RQ7 | 2 |

**E1** dùng cùng kiến trúc và protocol cho hai lượt: một theo phép chia gốc IP102 giới hạn ở K20-VN, một theo group-aware split, với **số ảnh train được cân bằng bằng cách hạ mẫu** để chênh lệch không đến từ khối lượng dữ liệu. Bằng chứng chính cho RQ1 là phép so sánh **trong cùng một phép chia**: tách test gốc thành nhóm gần trùng với train và nhóm không gần trùng rồi đo chênh lệch; phép so sánh giữa hai split là bằng chứng phụ.

**E3** dùng bảy cấu hình, tất cả trên EfficientNet-B0 và cùng tổng số bước gradient:

> * **B0** — toàn bộ dữ liệu sạch, natural sampling, CrossEntropy (đối chứng).
> * **B1** — square-root sampler `w_c = 1/√n_c` [41] + CrossEntropy.
> * **B2** — effective-number sampler `E_c = (1-β^n_c)/(1-β)` với β = 0,999 [42] + CrossEntropy. Đây là cách áp dụng của đề tài; bài gốc dùng số hiệu dụng để điều chỉnh trọng số hàm mất mát.
> * **B3** — natural sampling + Balanced Softmax [43], trong đó `z'_j = z_j + log n_j` **chỉ áp dụng khi huấn luyện và gỡ bỏ khi suy luận**.
> * **B4** — hard cap 800, natural sampling, CrossEntropy; chỉ khác B0 đúng một biến.
> * **B5** — cRT: huấn luyện lại riêng classifier với backbone đóng băng bằng class-balanced sampler trên checkpoint B0 [41]; chi phí thấp.
> * **B6** — logit adjustment hậu xử lý trên checkpoint B0, τ chọn trên validation [50]; **không tốn lượt huấn luyện nào**.
> * **B7** (điều kiện) — best sampler kết hợp Balanced Softmax, chỉ chạy nếu sampler đã thắng ở vòng đầu, nhằm kiểm tra nguy cơ hiệu chỉnh mất cân bằng hai lần.

Trước khi so sánh, B0 được chạy ba seed để **ước lượng độ nhiễu σ của Macro-F1 validation**. Cấu hình thắng chọn bằng Macro-F1 validation; chênh lệch nhỏ hơn `max(0,005; 2σ)` được coi là hòa và giải quyết theo thứ tự ưu tiên: phương án đơn giản hơn, ECE thấp hơn, Tail exposure thấp hơn, Head recall không giảm quá 0,01. Giữ tối đa hai phương án đi tiếp.

**E4** giữ nguyên balancing đã chọn và so ba policy: AUG-0 gồm RandomResizedCrop bảo thủ, HorizontalFlip và ColorJitter nhẹ; AUG-1 thêm RandAugment mức nhẹ [44] và RandomErasing [45]; AUG-2 thêm MixUp [46] và CutMix [47], chỉ chạy trong bộ nhớ, không sinh ảnh ra đĩa. VerticalFlip không dùng mặc định vì tạo bối cảnh không tự nhiên.

**Training Protocol v1** được khóa sau E4 vào `training_protocol_v1.yaml` và ghi đủ: dataset, split, balancing, loss, augmentation, đầu vào 224 × 224, chuẩn hóa ImageNet, AdamW với weight decay 0,05, cosine annealing, warmup ba epoch, tối đa 30 epoch, early stopping patience 8 theo Macro-F1 validation, batch size 64 (32 kèm tích lũy gradient 2 cho mô hình vượt VRAM), mixed precision fp16, tắt TTA, `cudnn.benchmark = False`, phiên bản torch/timm ghim cứng, seed screening 42 và seed finalist 42/2026/3407. Learning rate: đầu phân loại 1e-3; backbone CNN và lai 1e-4; backbone có self-attention chiếm tỷ trọng lớn 1e-5, theo quy tắc gia đình kiến trúc đặt trước. Quy tắc learning rate cố định theo gia đình là một giả định chưa tinh chỉnh riêng cho từng mô hình và **được ghi rõ trong phần hạn chế** của báo cáo.

**E5** so bảy kiến trúc dưới cùng protocol. Vì bảy checkpoint có nguồn tiền huấn luyện khác nhau (một số trên ImageNet-1k, TinyViT được chưng cất từ teacher lớn trên ImageNet-21k), báo cáo kèm bảng nguồn trọng số, URL, checksum và top-1 ImageNet của từng mô hình, và phép so sánh được phát biểu là **so sánh checkpoint sẵn có dưới một giao thức cố định**, không phải so sánh kiến trúc thuần túy. Top-2 chọn trên validation theo Macro-F1, balanced accuracy, số tham số và độ trễ đo thực tế; khi Macro-F1 chênh dưới ngưỡng hòa, ưu tiên mô hình nhỏ và nhanh hơn. Ngay tuần 6, trước khi chạy E5, cả bảy kiến trúc được xuất thử sang TensorFlow Lite INT8 với trọng số ngẫu nhiên và đo độ trễ trên máy thật; kiến trúc không xuất được bị loại trước để không lãng phí lượt huấn luyện.

**E6** mở K20-FG sau khi protocol đã khóa: FG0 dùng natural sampling và CrossEntropy, FG1 dùng balancing thắng E3, FG2 dùng phương án xếp thứ hai, chạy trên Top 1–2 kiến trúc và chỉ kết luận bằng validation trong giai đoạn phát triển. Tối đa một cấu hình bổ sung được phép nếu đã đăng ký trước lý do. **E7** dùng kiến trúc tốt nhất trên K15, K20-VN, K35 và K45; kết quả K20-VN của E5 được tái sử dụng nên chỉ cần ba lượt bổ sung. Vì thêm lớp cũng làm thay đổi tổng dữ liệu và mức mất cân bằng, bảng kết quả E7 **bắt buộc kèm cột IR và tổng ảnh train** của từng K, và phần phân tích nêu rõ confound này.

**E8** đặt teacher là một mô hình mạnh **nằm ngoài nhóm bảy kiến trúc triển khai** (ConvNeXt-Small hoặc EfficientNetV2-S), vì chênh lệch Macro-F1 giữa các mô hình cùng cỡ di động khó vượt cổng 0,05 và nhánh chưng cất sẽ tự chặn chính nó. Student là mô hình nhẹ tốt nhất theo Macro-F1, kích thước và độ trễ. Teacher phải vượt student ít nhất 0,05 Macro-F1 trên validation, nếu không nhánh KD dừng hoặc chuyển sang ensemble đã đăng ký trước. KD0 là student với protocol đã khóa; KD1 thêm chưng cất logits với nhiệt độ T và hệ số trộn α khóa trước; KD2 chỉ thêm MixUp/CutMix nếu policy thắng E4 chưa chứa hai kỹ thuật này, ngược lại KD2 bị bỏ vì trùng cấu hình.

### ***5.5. Phương pháp đánh giá***

Train và validation là khối phát triển. Test của mọi tập K bị khóa cho đến khi dataset version, protocol, mô hình, checkpoint và quy tắc hậu xử lý đã cố định. Việc dùng test để chọn checkpoint hoặc cấu hình bị cấm tuyệt đối. **Danh sách các lượt được phép mở Test được đăng ký trước** trong `training_protocol_v1.yaml`, gồm đúng chín lượt: Top-1 kiến trúc trên K20-VN với ba seed, KD0 và KD1, Top-1 trên K20-FG, mỗi tập K15/K35/K45 một lượt, và hai bản INT8 (PTQ, QAT) của mô hình triển khai. Mọi lượt ngoài danh sách chỉ được báo cáo trên validation.

Macro-F1 validation là chỉ số chọn phương án. Final Test báo cáo:

| Chỉ số | Tập đo | Vai trò |
|---|---|---|
| Macro-F1 | Natural Test | Chỉ số kết luận chính |
| Balanced accuracy | Natural Test | Recall trung bình giữa các lớp |
| Accuracy | test_balanced | Chỉ tiêu tham chiếu của K20-VN |
| Macro precision, macro recall | Natural Test | Mô tả cân bằng lỗi |
| Head/Medium/Tail recall | Natural Test | Phân tích theo quy mô lớp |
| ECE | Natural Test, ảnh thực địa, **bản INT8** | Kiểm tra calibration [48] |
| Confusion matrix | test_balanced | Phân tích cặp lớp dễ nhầm |
| Tham số, FLOPs, MB, ms/ảnh | Mô hình triển khai | Đánh đổi hiệu năng–chi phí |

Khoảng tin cậy 95% ước lượng bằng bootstrap **2.000 lần có phân tầng theo lớp**, tránh trường hợp một lớp Tail vắng mặt làm Macro-F1 không xác định. Khi so hai mô hình, báo cáo khoảng tin cậy của **hiệu số** bằng bootstrap ghép cặp trên cùng bộ resample, không kết luận từ việc hai khoảng tin cậy có chồng lấn hay không. Student và PestID-KD được so thêm bằng McNemar trên cùng tập dự đoán, dùng checkpoint của seed có Macro-F1 trung vị (đăng ký trước). Với ba seed finalist, báo cáo trung bình, độ lệch chuẩn và kết quả từng seed; việc mở test cho cả ba seed diễn ra trong cùng một pha Final Test và ba seed được trình bày như một bộ, không dùng để chọn lại cấu hình.

**E9** đánh giá mô hình đã khóa trên ảnh thực địa. Ngưỡng từ chối được chọn bằng validation trong phân phối kết hợp bộ ngoài tập lớn dựng từ 82 lớp IP102 còn lại, chia 30% hiệu chỉnh / 70% đánh giá; sau đó báo cáo true rejection, false rejection, AUROC và ECE trên phần đánh giá chưa sử dụng. Bộ ngoài tập thực địa chỉ dùng để báo cáo cuối. Kết quả ảnh thực địa được báo cáo cả theo ảnh và theo cá thể.

**E10** đo tác động của lượng tử hóa. Tập hiệu chuẩn của PTQ lấy **từ train**, không lấy từ validation hay test. Vì lượng tử hóa làm thay đổi logits và do đó thay đổi cả calibration lẫn điểm hoạt động của ngưỡng, ngưỡng từ chối và ECE được **đo lại trên chính bản INT8 sẽ đóng gói vào ứng dụng**, dùng đúng phần hiệu chỉnh đã tách ở E9.

Giải thích mô hình dùng Grad-CAM cho các backbone tích chập và Attention Rollout hoặc Grad-CAM++ có reshape transform cho các backbone ViT/lai, vì Grad-CAM thuần không áp dụng trực tiếp được cho kiến trúc dựa trên attention. Kết quả chỉ so sánh trực tiếp với nghiên cứu khác khi giao thức dữ liệu tương thích; mọi bảng phải kèm phân tích mức cải thiện, độ không chắc chắn và chi phí đánh đổi.

### ***5.6. Kiến trúc hệ thống, công nghệ và ngân sách tính toán***

Hệ thống suy luận hoàn toàn trên thiết bị. Ảnh từ camera hoặc thư viện được resize, center crop 224 × 224 và chuẩn hóa; mô hình TensorFlow Lite INT8 trả về logits, softmax, top-3 và cờ không chắc chắn. Giao diện hiển thị `display_name_vi`, độ tin cậy, ảnh mẫu và dòng cảnh báo. Ứng dụng không gửi ảnh lên máy chủ, không yêu cầu chọn cây trồng và lưu lịch sử tra cứu cục bộ. Sản phẩm chuyển giao gồm tệp mô hình, bảng ánh xạ `model_class_index ↔ ip102_id ↔ display_name_vi`, cấu hình tiền xử lý và ngưỡng từ chối đã khóa trên bản INT8.

| Hạng mục | Lựa chọn |
|---|---|
| Huấn luyện | Kaggle Notebook, hai GPU NVIDIA T4 16 GB, hai thí nghiệm độc lập song song |
| Ngôn ngữ, framework | Python 3.11, PyTorch 2.x, torchvision, timm (ghi URL, phiên bản, checksum trọng số) |
| Xử lý dữ liệu | Pillow, OpenCV, NumPy, pandas |
| Balancing | WeightedRandomSampler, Balanced Softmax và logit adjustment tự triển khai có kiểm thử đơn vị |
| Đánh giá | scikit-learn, scipy, bootstrap phân tầng, ECE, McNemar |
| EDA và giải thích | matplotlib, seaborn, UMAP, cleanlab, pytorch-grad-cam |
| Gán nhãn | Label Studio chạy cục bộ |
| Triển khai | PyTorch → ONNX → TensorFlow Lite INT8 |
| Ứng dụng | Flutter Android, tflite_flutter |
| Quản lý | Git, GitHub, `build_info.json`, `training_protocol_v1.yaml`, model card |

Ngân sách tính toán được ước lượng trước để kiểm tra tính khả thi trong hạn mức GPU miễn phí của Kaggle (khoảng 30 giờ phiên mỗi tuần, mỗi phiên tối đa 12 giờ; cần kiểm tra lại hạn mức hiện hành tại thời điểm thực hiện):

| Thí nghiệm | Lượt | Giờ/lượt | Tổng giờ GPU |
|---|:---:|:---:|:---:|
| E1 | 2 | 1,5 | 3 |
| E2 (linear probe) | 3 | 0,7 | 2 |
| E3 | 6 | 1,5 | 9 |
| E4 | 3 | 1,7 | 5 |
| E5 (7 kiến trúc + 4 lượt seed) | 11 | 2,2 | 24 |
| E6 | 6 | 2,0 | 12 |
| E7 | 3 | 2,5 | 8 |
| E8 (teacher + KD0 + KD1) | 3 | 3,0 | 9 |
| E10 (QAT) | 2 | 2,0 | 4 |
| **Cộng** | **39** | | **76** |
| **Dự phòng 20%** | | | **91** |

Với hai GPU chạy song song, 91 giờ GPU tương đương khoảng 46 giờ phiên, trải trên chín tuần thực nghiệm (tuần 5–13) so với hạn mức khoảng 270 giờ phiên. Biên an toàn đủ rộng, và điều kiện cần là toàn bộ ảnh đã được resize và đóng gói trước để loại nút thắt vào/ra. Nếu một kiến trúc không chuyển được sang TensorFlow Lite, E10 chọn mô hình có hiệu năng validation tốt nhất trong nhóm chuyển đổi được; không chuyển sang suy luận máy chủ vì trái phạm vi on-device.

## **6. Ý nghĩa khoa học và thực tiễn**

### ***6.1. Ý nghĩa khoa học***

Đề tài có năm đóng góp:

> 1. **Quy trình dữ liệu truy vết được** từ IP102 thô đến Master Clean Pool, kèm phép đo định lượng mức rò rỉ của phép chia gốc và một phép chia group-aware công khai — thứ mà các công bố hiện có trên IP102 chưa cung cấp.

> 2. **Một cách chọn tập lớp theo bối cảnh quốc gia** dựa trên văn bản quản lý nhà nước về bảo vệ thực vật, có rubric, chấm kép và đo đồng thuận, kèm tập đối chứng K20-Count để tách ảnh hưởng của cách chọn lớp khỏi thông lệ chọn theo số lượng ảnh.

> 3. **Một chuỗi thực nghiệm tuần tự** trong đó mỗi giai đoạn chỉ thay đổi một nhóm biến và mọi quyết định được ghi kèm ngưỡng hòa dựa trên độ nhiễu đo được, nhờ đó kết luận quy được về thành phần cụ thể thay vì một tổ hợp kỹ thuật không giải thích được.

> 4. **Một phép kiểm tra độ bền fine-grained có kiểm soát**: K20-FG được khóa trước và khớp mức mất cân bằng với K20-VN, biến câu hỏi "phương pháp tốt trên tập ứng dụng có còn tốt khi các lớp giống nhau hơn không" thành một phép đo có thể diễn giải. Kết quả âm tính ở đây vẫn là kết quả nghiên cứu có giá trị.

> 5. **Một đánh giá triển khai đầy đủ vòng đời**: calibration, khả năng từ chối dự đoán, độ suy giảm trên ảnh thực địa, và việc hiệu chỉnh lại ngưỡng sau lượng tử hóa — bước cuối gần như không xuất hiện trong các công bố nhận diện sâu hại trên di động.

### ***6.2. Ý nghĩa thực tiễn***

Sản phẩm cuối là ứng dụng hỗ trợ nhận diện đối tượng gây hại trên điện thoại Android, hoạt động ngoại tuyến, có top-3, cờ không chắc chắn và cảnh báo bắt buộc. Bộ ảnh thực địa, manifest dữ liệu, datasheet, protocol huấn luyện và model card có thể tái sử dụng cho các khóa sau và cho việc mở rộng sang các lớp chưa có trong IP102. Hệ thống không thay thế chuyên gia và không đưa ra khuyến nghị thuốc; các lớp chưa học được xử lý bằng từ chối dự đoán.

### ***6.3. Đạo đức nghiên cứu và bản quyền dữ liệu***

IP102 là bộ dữ liệu học thuật công khai; đề tài sử dụng đúng điều kiện của tác giả, không tái phân phối ảnh gốc mà chỉ công bố manifest gồm checksum, đường dẫn tương đối và nhãn. Ảnh thực địa do nhóm tự chụp trên đồng ruộng sau khi xin phép chủ vườn hoặc chủ ruộng; ảnh có mặt người hoặc thông tin nhận dạng tài sản bị loại hoặc che trước khi lưu trữ. Bộ ảnh thực địa chỉ công bố kèm giấy phép cho mục đích nghiên cứu phi thương mại. Ứng dụng không thu thập ảnh người dùng và không gửi dữ liệu ra ngoài thiết bị.

---

# **BỐ CỤC ĐỒ ÁN MÔN HỌC (DỰ KIẾN)**

## **Chương 1: Cơ sở lý thuyết và tổng quan nghiên cứu**

1.1. Bài toán phân loại ảnh đối tượng gây hại và đặc điểm của IP102 [1].

1.2. Mạng tích chập, Vision Transformer và kiến trúc lai [2][3][4][5][36]–[39].

1.3. Học chuyển giao, mô hình nền và linear probe [31][32][40].

1.4. Dữ liệu đuôi dài: natural sampling, square-root sampling, effective number, Balanced Softmax, cRT và logit adjustment [41][42][43][50].

1.5. Tăng cường dữ liệu: RandAugment, Random Erasing, MixUp, CutMix [44]–[47].

1.6. Chưng cất tri thức và lượng tử hóa PTQ/QAT [49][14].

1.7. Chỉ số đánh giá, calibration, ECE, bootstrap phân tầng và McNemar [48].

1.8. Rò rỉ dữ liệu, ảnh gần trùng và group-aware split.

1.9. Tổng quan nghiên cứu trong nước, quốc tế và khoảng trống nghiên cứu.

## **Chương 2: Phương pháp nghiên cứu và mô hình đề xuất**

2.1. IP102, Master Clean Pool và quy trình làm sạch C1–C7.

2.2. Bảng xếp hạng P/A/D có rubric; K15, K20-VN, K35, K45, K20-Count và K20-FG.

2.3. Manifest 102 lớp, group-aware split 60/15/25 và 12 kiểm tra chấp nhận.

2.4. EDA, phân nhóm Head/Medium/Tail và linear probe trên ba mô hình nền.

2.5. Sàng lọc xử lý mất cân bằng B0–B7 và quy tắc chọn có ngưỡng hòa theo độ nhiễu.

2.6. Sàng lọc tăng cường dữ liệu AUG-0 đến AUG-2 và Training Protocol v1.

2.7. Bảy kiến trúc nền, quy tắc learning rate theo gia đình và quy tắc chọn Top-2.

2.8. K20-FG stress test có ràng buộc khớp và khảo sát số lượng lớp.

2.9. PestID-KD: teacher ngoài nhóm triển khai, cổng chênh lệch và ablation.

2.10. Final Test, ảnh thực địa, bộ ngoài tập và cơ chế từ chối dự đoán.

2.11. Kiến trúc ứng dụng và quy trình triển khai TensorFlow Lite INT8.

## **Chương 3: Thực nghiệm, đánh giá và xây dựng hệ thống**

3.1. Môi trường, phiên bản thư viện, ngân sách tính toán và khả năng tái lập.

3.2. Kết quả làm sạch, đo rò rỉ, bảng hạng và thống kê các tập dữ liệu.

3.3. E1–E2: audit rò rỉ, tập đối chứng và linear probe.

3.4. E3: kết quả sàng lọc xử lý mất cân bằng và ước lượng độ nhiễu.

3.5. E4: kết quả sàng lọc tăng cường dữ liệu và protocol đã khóa.

3.6. E5: so sánh bảy kiến trúc kèm bảng nguồn trọng số tiền huấn luyện.

3.7. E6: K20-FG stress test.

3.8. E7: ảnh hưởng số lớp K15/K20/K35/K45.

3.9. E8: PestID-KD và ablation.

3.10. Final Test, ECE, bootstrap ghép cặp, McNemar, ma trận nhầm lẫn và phân tích lỗi kèm bản đồ nhiệt.

3.11. E9: ảnh thực địa, bộ ngoài tập và ngưỡng từ chối.

3.12. E10: PTQ/QAT, hiệu chỉnh lại ngưỡng trên INT8, độ trễ và ứng dụng Flutter.

## **Phần kết luận**

- Đối chiếu từng câu hỏi nghiên cứu RQ1–RQ7 với kết quả thực nghiệm.

- Đóng góp về khoa học, thực tiễn và kỹ thuật.

- Hạn chế: quy mô ảnh thực địa, tính chủ quan của bảng P/A/D và K20-FG, quy tắc learning rate cố định theo gia đình kiến trúc, khác biệt nguồn tiền huấn luyện giữa bảy checkpoint, và giới hạn closed-set của tập lớp.

- Hướng phát triển: mở rộng lớp cho sâu keo mùa thu và rầy chổng cánh, phát hiện ngoài phân phối đầy đủ, ngữ cảnh cây trồng (PestID-CropGate), nhận dạng theo nhiều ảnh của cùng cá thể và bài toán định vị.

---

# **TIẾN ĐỘ THỰC HIỆN**

Đồ án thực hiện trong 14 tuần, từ 01/9/2026 đến 07/12/2026, chia thành 4 giai đoạn. Công việc chia theo ba mảng Dữ liệu, Mô hình và Ứng dụng–Báo cáo, nhưng mọi thay đổi giao thức phải ghi chung vào `training_protocol_v1.yaml`.

## **GIAI ĐOẠN 1: DỮ LIỆU VÀ THIẾT KẾ THỰC NGHIỆM**

**Thời gian:** 01/9 – 28/9/2026 (Tuần 1–4)

> * Hoàn thiện tổng quan nghiên cứu và Chương 1.

> * Xử lý toàn bộ IP102 bằng C1–C7; hiệu chuẩn ngưỡng ảnh gần trùng và độ mờ; kiểm tra số lớp đạt sàn 200 ảnh sạch.

> * Chấm kép và khóa bảng P/A/D; khóa K20-FG kèm ràng buộc khớp; tạo K15/K20-VN/K35/K45, K20-Count.

> * Tạo group-aware split trên Master Clean Pool; chạy 12 kiểm tra chấp nhận; đóng gói dataset đã resize; viết datasheet.

> * **Bắt đầu thu ảnh thực địa và duy trì song song đến hết tuần 11.**

## **GIAI ĐOẠN 2: KHÓA TRAINING PROTOCOL V1**

**Thời gian:** 29/9 – 12/10/2026 (Tuần 5–6)

> * Chạy E1 (audit rò rỉ) và E2 (đối chứng tập lớp, linear probe).

> * Chạy E3 với B0–B7, gồm ba seed của B0 để ước lượng độ nhiễu; chạy E4 với AUG-0 đến AUG-2.

> * Chọn balancing và augmentation bằng validation; khóa `training_protocol_v1.yaml` và danh sách lượt được phép mở Test.

> * Xuất thử cả bảy kiến trúc sang ONNX → TensorFlow Lite INT8 với trọng số ngẫu nhiên; đo độ trễ trên máy thật; loại kiến trúc không xuất được.

## **GIAI ĐOẠN 3: KIẾN TRÚC, STRESS TEST VÀ CHƯNG CẤT**

**Thời gian:** 13/10 – 16/11/2026 (Tuần 7–11)

> * Chạy E5 trên các kiến trúc còn lại; chọn Top-2 và xác nhận bằng ba seed.

> * Chạy E6 trên K20-FG và E7 trên K15/K35/K45.

> * Chạy E8: huấn luyện teacher ngoài nhóm triển khai, kiểm tra cổng 0,05, chạy KD0–KD1 và ablation.

> * Hoàn tất gán nhãn kép ảnh thực địa, tính Cohen's kappa, dựng bộ ngoài tập từ 82 lớp còn lại. Test vẫn đóng trong toàn bộ giai đoạn.

## **GIAI ĐOẠN 4: FINAL TEST, THỰC ĐỊA VÀ TRIỂN KHAI**

**Thời gian:** 17/11 – 07/12/2026 (Tuần 12–14)

> * Khóa dataset, protocol, checkpoint và quy tắc hậu xử lý; mở Final Test đúng theo danh sách đã đăng ký.

> * Chạy E9 trên ảnh thực địa và bộ ngoài tập; khóa ngưỡng từ chối.

> * Chạy E10 với PTQ/QAT; hiệu chỉnh lại ngưỡng và ECE trên bản INT8; đo kích thước và độ trễ trên hai điện thoại thật.

> * Tích hợp ứng dụng Flutter; hoàn thiện model card, báo cáo và slide; bảo vệ thử.

## **Tiến độ chi tiết theo tuần**

| Tuần | Thời gian | Nội dung công việc chính |
| :---: | :---: | ----- |
| Tuần 1 | 01/9 – 07/9 | Tổng quan; tải và kiểm tra IP102; chạy C1–C3; khởi động thu ảnh thực địa. |
| Tuần 2 | 08/9 – 14/9 | C4 hiệu chuẩn ngưỡng near-duplicate và độ mờ; kiểm tra số lớp đạt sàn 200 ảnh sạch. |
| Tuần 3 | 15/9 – 21/9 | C5–C7; chấm kép bảng P/A/D và K20-FG; tính Krippendorff's alpha. |
| Tuần 4 | 22/9 – 28/9 | Group-aware split; tạo các tập K; 12 kiểm tra chấp nhận; đóng gói dataset; datasheet. |
| Tuần 5 | 29/9 – 05/10 | E1, E2 và E3 (B0 ba seed, B1–B4). |
| Tuần 6 | 06/10 – 12/10 | B5–B7; E4 (AUG-0 đến AUG-2); khóa protocol; xuất thử TFLite bảy kiến trúc. |
| Tuần 7 | 13/10 – 19/10 | E5 phần 1: bốn kiến trúc đầu. |
| Tuần 8 | 20/10 – 26/10 | E5 phần 2: các kiến trúc còn lại; chọn Top-2. |
| Tuần 9 | 27/10 – 02/11 | Xác nhận Top-2 bằng ba seed; bắt đầu E6 trên K20-FG. |
| Tuần 10 | 03/11 – 09/11 | Hoàn tất E6; chạy E7 trên K15/K35/K45. |
| Tuần 11 | 10/11 – 16/11 | E8: teacher, KD0–KD1, ablation; hoàn tất gán nhãn ảnh thực địa và bộ ngoài tập. |
| Tuần 12 | 17/11 – 23/11 | Khóa toàn bộ; Final Test theo danh sách đăng ký; E9 và khóa ngưỡng từ chối. |
| Tuần 13 | 24/11 – 30/11 | E10: PTQ/QAT, hiệu chỉnh ngưỡng trên INT8, đo độ trễ; tích hợp Flutter. |
| Tuần 14 | 01/12 – 07/12 | Kiểm thử ứng dụng; hoàn thiện báo cáo, model card, slide; bảo vệ thử. |

## **Phân công thực hiện**

| Mảng | Thành viên | Hạng mục phụ trách |
|---|---|---|
| Dữ liệu và tiền xử lý | SV1, SV2 | C1–C7, bảng P/A/D, manifest, 12 kiểm tra, datasheet, E1, E2, thu và gán nhãn ảnh thực địa |
| Mô hình và thực nghiệm | SV3, SV4 | E3–E8, Training Protocol v1, Final Test, phân tích lỗi và bản đồ nhiệt |
| Ứng dụng và báo cáo | SV5 | E9, E10, chuyển đổi TFLite, ứng dụng Flutter, model card, báo cáo và slide |

## **Rủi ro và phương án dự phòng**

| Rủi ro | Phương án |
|---|---|
| Không đủ 45 lớp đạt sàn 200 ảnh sạch | Hạ K45 xuống mức khả thi (K40) và công bố rõ phạm vi |
| K20-FG không đạt ràng buộc khớp IR | Dùng bản `K20-FG-matched` hạ mẫu theo K20-VN và báo cáo cả hai |
| Không cấu hình balancing nào vượt B0 quá ngưỡng nhiễu | Giữ natural sampling + CE và báo cáo kết quả âm tính như một phát hiện |
| Teacher không hơn student 0,05 Macro-F1 | Dừng nhánh KD hoặc chuyển sang ensemble đã đăng ký trước |
| Hết hạn mức GPU | Cắt cấu hình FG bổ sung, khảo sát K mở rộng và QAT trước; giữ nguyên E5 trên K20-VN |
| Không đủ ảnh thực địa | Hạ xuống 60–80 ảnh, giữ gán nhãn kép, chỉ báo cáo mức tổng hợp và nêu hạn chế |
| ONNX/TFLite lỗi toán tử | Đã loại trước ở tuần 6; chọn mô hình tốt nhất trong nhóm chuyển đổi được |
| Ngưỡng từ chối lệch sau khi lượng tử hóa | Hiệu chỉnh lại trên bản INT8 bằng phần calibration đã tách; báo cáo cả hai điểm hoạt động |
| Không đạt mức Macro-F1 cam kết | Báo cáo trung thực kèm phân tích theo lớp, ma trận nhầm lẫn và Head/Medium/Tail; đối chiếu với mức rò rỉ đo được ở E1 |
| Test bị mở sớm | Hủy quyết định chịu ảnh hưởng, ghi biên bản và tái lập protocol nếu còn khả thi |

## **Sản phẩm đầu ra của từng giai đoạn**

### ***Giai đoạn 1:***

> * Master Clean Pool, manifest 102 lớp, các tập K, K20-Count, K20-FG và datasheet.

> * Bảng P/A/D đầy đủ 102 dòng kèm Krippendorff's alpha; báo cáo 12 kiểm tra chấp nhận.

### ***Giai đoạn 2:***

> * Kết quả E1–E4, ước lượng độ nhiễu Macro-F1 và bảng so sánh B0–B7, AUG-0–AUG-2.

> * `training_protocol_v1.yaml` đã khóa, kèm danh sách lượt được phép mở Test.

> * Báo cáo khả năng chuyển đổi TensorFlow Lite của bảy kiến trúc.

### ***Giai đoạn 3:***

> * Bảng so sánh bảy kiến trúc kèm nguồn trọng số tiền huấn luyện; kết quả K20-FG, khảo sát số lớp và ablation chưng cất.

> * Mã nguồn E1–E8, log, checkpoint, seed và mã commit.

> * Bộ ảnh thực địa đã gán nhãn kép kèm Cohen's kappa; bộ ngoài tập.

### ***Giai đoạn 4:***

> * Kết quả Final Test: Macro-F1, ECE, bootstrap ghép cặp, McNemar, ma trận nhầm lẫn, bản đồ nhiệt giải thích và báo cáo từ chối dự đoán.

> * Mô hình TensorFlow Lite INT8, model card, ứng dụng Flutter Android.

> * Báo cáo đồ án 60–70 trang và slide thuyết trình.

---

# **TÀI LIỆU THAM KHẢO**

1. Wu, X., Zhan, C., Lai, Y.-K., Cheng, M.-M., & Yang, J. (2019). IP102: A large-scale benchmark dataset for insect pest recognition. In *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*, pp. 8787–8796.

2. Sandler, M., Howard, A., Zhu, M., Zhmoginov, A., & Chen, L.-C. (2018). MobileNetV2: Inverted residuals and linear bottlenecks. In *CVPR*, pp. 4510–4520.

3. Tan, M., & Le, Q. V. (2019). EfficientNet: Rethinking model scaling for convolutional neural networks. In *ICML*, PMLR 97, pp. 6105–6114.

4. Dosovitskiy, A., Beyer, L., Kolesnikov, A., Weissenborn, D., Zhai, X., Unterthiner, T., et al. (2021). An image is worth 16×16 words: Transformers for image recognition at scale. In *ICLR*. arXiv:2010.11929.

5. Touvron, H., Cord, M., Douze, M., Massa, F., Sablayrolles, A., & Jégou, H. (2021). Training data-efficient image transformers & distillation through attention. In *ICML*. arXiv:2012.12877.

6. Ung, H. T., Ung, H. Q., & Nguyen, B. T. (2021). *An efficient insect pest classification using multiple convolutional neural network based models*. arXiv:2107.12189.

7. Setiawan, A., Yudistira, N., & Wihandika, R. C. (2022). Large scale pest classification using efficient convolutional neural network with augmentation and regularizers. *Computers and Electronics in Agriculture*, 200, 107204.

8. Peng, H., Xu, H., Shen, G., Liu, H., Guan, X., & Li, M. (2024). A lightweight crop pest classification method based on improved MobileNet-V2 model. *Agronomy*, 14(6), 1334.

9. Wei, J., Gong, H., Li, S., You, M., Zhu, H., et al. (2024). Improving the accuracy of agricultural pest identification: Application of AEC-YOLOv8n to large-scale pest datasets. *Agronomy*, 14(8), 1640.

10. Zhang, L., Zhao, C., Feng, Y., & Li, D. (2023). Pests identification of IP102 by YOLOv5 embedded with the novel lightweight module. *Agronomy*, 13(6), 1583.

11. Zou, J., Yang, W., Li, C., & Feng, Z. (2025). AdaptPest-Net: A task-adaptive network with Graph–Mamba fusion for multi-scale agricultural pest recognition. *Entropy*, 27(12), 1211.

12. Li, Y., Hu, J., Wen, Y., Evangelidis, G., Salahi, K., Wang, Y., Tulyakov, S., & Ren, J. (2023). Rethinking vision transformers for MobileNet size and speed with strong training strategies (EfficientFormerV2). In *Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)*, pp. 16889–16900. arXiv:2212.08059.

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

31. Nguyen, H.-Q., Truong, T.-D., Nguyen, X.-B., Dowling, A., Li, X., & Luu, K. (2024). Insect-Foundation: A foundation model and large-scale 1M dataset for visual insect understanding. In *CVPR*.

32. Oquab, M., Darcet, T., Moutakanni, T., Vo, H., Szafraniec, M., Khalidov, V., et al. (2024). DINOv2: Learning robust visual features without supervision. *Transactions on Machine Learning Research (TMLR)*. arXiv:2304.07193.

33. Bộ Nông nghiệp và Phát triển nông thôn. (2010). *QCVN 01-38:2010/BNNPTNT — Quy chuẩn kỹ thuật quốc gia về phương pháp điều tra phát hiện dịch hại cây trồng*.

34. Bộ Nông nghiệp và Phát triển nông thôn. (2023). *Thông tư 04/2023/TT-BNNPTNT ban hành Danh mục đối tượng kiểm dịch thực vật của nước Cộng hòa xã hội chủ nghĩa Việt Nam*.

35. Cục Trồng trọt và Bảo vệ thực vật. *Báo cáo tình hình sinh vật gây hại cây trồng* (các kỳ báo cáo định kỳ). Bộ Nông nghiệp và Môi trường. (Cơ quan được thành lập trên cơ sở hợp nhất Cục Trồng trọt và Cục Bảo vệ thực vật theo Quyết định 58/QĐ-BNNMT năm 2025; các báo cáo trước 2025 mang tên Cục Bảo vệ thực vật, Bộ Nông nghiệp và Phát triển nông thôn.)

36. Qin, D., Leichner, C., Delakis, M., Fornoni, M., Luo, S., Yang, F., et al. (2024). MobileNetV4: Universal models for the mobile ecosystem. In *European Conference on Computer Vision (ECCV)*. arXiv:2404.10518.

37. Wu, K., Zhang, J., Peng, H., Liu, M., Xiao, B., Fu, J., & Yuan, L. (2022). TinyViT: Fast pretraining distillation for small vision transformers. In *ECCV*, pp. 68–85.

38. Vasu, P. K. A., Gabriel, J., Zhu, J., Tuzel, O., & Ranjan, A. (2023). FastViT: A fast hybrid vision transformer using structural reparameterization. In *ICCV*, pp. 5785–5795. arXiv:2303.14189.

39. Zheng, C., et al. (2025). iFormer: Integrating ConvNet and Transformer for mobile application. In *ICLR*. arXiv:2501.15369. *(Lưu ý phân biệt với "iFormer: Inception Transformer", NeurIPS 2022 — một công trình khác cùng tên viết tắt.)*

40. Gu, J., et al. (2025). BioCLIP 2: Emergent properties from scaling hierarchical contrastive learning. In *NeurIPS*, Spotlight. arXiv:2505.23883.

41. Kang, B., Xie, S., Rohrbach, M., Yan, Z., Gordo, A., Feng, J., & Kalantidis, Y. (2020). Decoupling representation and classifier for long-tailed recognition. In *ICLR*.

42. Cui, Y., Jia, M., Lin, T.-Y., Song, Y., & Belongie, S. (2019). Class-balanced loss based on effective number of samples. In *CVPR*, pp. 9268–9277.

43. Ren, J., Yu, C., Sheng, S., Ma, X., Zhao, H., Yi, S., & Li, H. (2020). Balanced Meta-Softmax for long-tailed visual recognition. In *NeurIPS*.

44. Cubuk, E. D., Zoph, B., Shlens, J., & Le, Q. V. (2020). RandAugment: Practical automated data augmentation with a reduced search space. In *CVPR Workshops*.

45. Zhong, Z., Zheng, L., Kang, G., Li, S., & Yang, Y. (2020). Random Erasing data augmentation. In *Proceedings of the AAAI Conference on Artificial Intelligence*, 34(07), 13001–13008.

46. Zhang, H., Cisse, M., Dauphin, Y. N., & Lopez-Paz, D. (2018). mixup: Beyond empirical risk minimization. In *ICLR*.

47. Yun, S., Han, D., Oh, S. J., Chun, S., Choe, J., & Yoo, Y. (2019). CutMix: Regularization strategy to train strong classifiers with localizable features. In *ICCV*, pp. 6023–6032.

48. Guo, C., Pleiss, G., Sun, Y., & Weinberger, K. Q. (2017). On calibration of modern neural networks. In *ICML*, PMLR 70, pp. 1321–1330.

49. Hinton, G., Vinyals, O., & Dean, J. (2015). Distilling the knowledge in a neural network. *NeurIPS Deep Learning Workshop*. arXiv:1503.02531.

50. Menon, A. K., Jayasumana, S., Rawat, A. S., Jain, H., Veit, A., & Kumar, S. (2021). Long-tail learning via logit adjustment. In *ICLR*. arXiv:2007.07314.

### ***Ghi chú về danh mục tài liệu tham khảo***

Danh mục gồm 50 tài liệu, trong đó 24 tài liệu thuộc giai đoạn 2024–2026. Các tài liệu [41]–[50] cung cấp nguồn gốc cho balancing, augmentation, calibration và chưng cất tri thức; chuỗi thực nghiệm tuần tự, cơ chế khóa K20-FG và các quy tắc dừng của PestID là thiết kế riêng của đề tài, không được trình bày như thuật toán trích nguyên từ một bài báo.

Khi trích dẫn cần giữ đúng phạm vi: 87,62% của [8] đo trên Pest37 theo phép chia gốc; 77,1% của EfficientNet-B0 trong [3] là ImageNet; DeiT-Tiny phải dẫn [5]; 78,4% của [11] là mốc đã xác minh trên IP102 đầy đủ tính đến tháng 8/2026; 84,25% của [24] không cùng giao thức nên chỉ nêu như cảnh báo về cách so sánh. Kết quả trên K20-VN, K20-Count hoặc K20-FG không được so sánh trực tiếp với IP102 102 lớp.

Số liệu IP102 dùng thống nhất trong báo cáo: 75.222 ảnh, 102 lớp, phép chia gốc 45.095 train / 7.508 validation / 22.619 test, lớp lớn nhất *Cicadellidae* 5.740 ảnh, lớp nhỏ nhất *Erythroneura apicalis* 71 ảnh, tỷ lệ mất cân bằng 80,8 lần, baseline 2019 đạt 49,4%, kết quả cao nhất đã xác minh đạt 78,4%.
