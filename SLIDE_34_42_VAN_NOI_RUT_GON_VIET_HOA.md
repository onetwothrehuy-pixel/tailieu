# Kịch bản văn nói rút gọn · Slide 34–42

> Bản này giữ đúng thứ tự nhịp bấm, số liệu chính và ý cốt lõi của bản chi tiết, nhưng rút gọn để dễ nói khi thuyết trình.

---

## Slide 34 · SIFT dùng Euclid, ORB dùng Hamming
*5 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/5: hiệu thành phần.]*

Tới đây mình đã có bộ mô tả đặc trưng rồi, nên bước tiếp theo là đo xem hai bộ mô tả đặc trưng giống nhau tới mức nào. SIFT là véc-tơ số thực nên dùng khoảng cách Euclid, hay L2. Ví dụ `a = (1,2)` và `b = (4,6)` thì hiệu là `(3,4)`.

*[Bấm →, nhịp 2/5: L2.]*

Khoảng cách L2 là căn của tổng bình phương sai khác: `sqrt(3² + 4²) = 5`. Khoảng cách càng nhỏ thì hai bộ mô tả SIFT càng giống nhau.

*[Bấm →, nhịp 3/5: XOR.]*

ORB thì bộ mô tả đặc trưng là chuỗi 256 bit nên dùng Hamming. Mình XOR hai chuỗi: bit giống nhau cho 0, bit khác nhau cho 1.

*[Bấm →, nhịp 4/5: Hamming.]*

Sau đó chỉ cần đếm số bit 1. Ví dụ có 3 bit khác nhau thì Hamming bằng 3. Càng ít bit khác nhau thì hai bộ mô tả ORB càng giống nhau.

*[Bấm →, nhịp 5/5: thời gian thật.]*

Trong phép đo của bài, với ảnh 960 px, toàn bộ quy trình SIFT mất 225,9 ms còn ORB 32,4 ms. Nhưng riêng bước so khớp chỉ là 12,5 ms so với 10,7 ms. Vậy ORB nhanh chủ yếu nhờ cả khâu phát hiện và mô tả nhẹ hơn, chứ không chỉ vì Hamming.

**Lưu ý:** không so trực tiếp trị số L2 với Hamming trên cùng một thang.

---

## Slide 35 · Phép kiểm tra tỉ số loại các ghép nối mơ hồ
*4 nhịp · khoảng 1 phút*

*[Nhịp 1/4: hai láng giềng gần nhất.]*

Với mỗi điểm ở ảnh A, mình tìm hai ứng viên gần nhất ở ảnh B, gọi khoảng cách là `d₁` và `d₂`. Mục tiêu không chỉ là tìm người gần nhất, mà xem người đó có nổi bật hơn người thứ hai hay không.

*[Bấm →, nhịp 2/4: khoảng cách.]*

Ví dụ đầu tiên có `d₁ = 144,7` và `d₂ = 354,9`. Ứng viên thứ nhất gần hơn rất rõ.

*[Bấm →, nhịp 3/4: ngưỡng τ.]*

Ta tính `d₁/d₂ = 0,408`. Vì nhỏ hơn `τ = 0,75`, cặp này được giữ. Tỉ số càng nhỏ thì ứng viên số một càng đáng tin.

*[Bấm →, nhịp 4/4: mơ hồ.]*

Ví dụ khác có tỉ số `0,970`. Hai ứng viên gần như ngang nhau nên bị loại. Phép kiểm tra tỉ số không cho xác suất đúng; nó chỉ đo mức độ nổi bật của ứng viên gần nhất.

---

## Slide 36 · Chọn τ: đánh đổi số cặp và độ đúng
*4 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/4: cặp đúng.]*

Trên cặp `graf 1 → graf 3`, các cặp so khớp đúng thường có `d₁/d₂` thấp, vì ứng viên gần nhất nổi bật rõ.

*[Bấm →, nhịp 2/4: cặp sai.]*

Các cặp so khớp sai lại dồn gần 1, nghĩa là ứng viên thứ nhất và thứ hai gần như ngang nhau.

*[Bấm →, nhịp 3/4: kéo τ.]*

Với `τ = 0,75`, giữ được 278 cặp, trong đó 237 cặp đúng, tức 85,3%.

*[Bấm →, nhịp 4/4: đánh đổi.]*

τ nhỏ thì ít cặp so khớp nhưng sạch hơn; τ lớn thì nhiều cặp so khớp hơn nhưng lẫn sai nhiều hơn. Ở `τ = 0,8` giữ 367 cặp nhưng độ đúng còn 78,2%. Vì vậy chọn τ theo mục tiêu của bài toán.

---

## Slide 37 · Qua phép kiểm tra tỉ số vẫn có thể ghép sai
*4 nhịp · khoảng 1 phút*

*[Nhịp 1/4: các match.]*

Qua phép kiểm tra tỉ số rồi vẫn chưa chắc tất cả cặp so khớp đều đúng. Trên cặp IP102/05382 còn 438 cặp sau phép kiểm tra tỉ số.

*[Bấm →, nhịp 2/4: phóng cặp.]*

Một số vùng nhìn khá giống nhau nên bộ mô tả đặc trưng vẫn có thể ghép nhầm, nhất là khi ảnh có hoa văn hoặc chữ lặp.

*[Bấm →, nhịp 3/4: chiếu H.]*

Mình dùng phép biến đổi H để dự đoán vị trí điểm từ ảnh A sang ảnh B. Dấu cộng là vị trí dự đoán, chấm tròn là vị trí so khớp thực tế.

*[Bấm →, nhịp 4/4: sai số.]*

Ví dụ này lệch 74,15 px, trong khi ngưỡng chỉ 4 px, nên đây là điểm ngoại lai về hình học. Vì vậy sau phép kiểm tra tỉ số vẫn cần kiểm tra hình học.

---

## Slide 38 · Chọn mô hình hình học
*5 nhịp · khoảng 1,5 phút*

*[Nhịp 1/5: phép biến đổi tương tự.]*

Phép biến đổi tương tự gồm xoay, co giãn đều và dịch chuyển. Có 4 bậc tự do, tối thiểu cần 2 cặp điểm. Nó rất hợp với ảnh cùng nguồn bị cắt ảnh, xoay hoặc thay đổi kích thước.

*[Bấm →, nhịp 2/5: phép biến đổi afin.]*

Phép biến đổi afin linh hoạt hơn, cho phép co giãn không đều và xô nghiêng. Có 6 bậc tự do, cần 3 cặp; đường song song vẫn song song.

*[Bấm →, nhịp 3/5: phép biến đổi xạ ảnh.]*

Phép biến đổi xạ ảnh mô tả phối cảnh của một mặt phẳng. Có 8 bậc tự do, cần 4 cặp. Nó hay dùng cho ảnh toàn cảnh hoặc định vị một mặt phẳng như tờ báo, bìa sách.

*[Bấm →, nhịp 4/5: fundamental.]*

Với cảnh 3D và camera di chuyển, dùng ma trận cơ bản. Một điểm ở ảnh A sẽ giới hạn điểm tương ứng ở ảnh B trên một đường cực. Mô hình này dùng trong các bài như SLAM.

*[Bấm →, nhịp 5/5: chọn mô hình.]*

Ý chính là không chọn mô hình càng phức tạp càng tốt, mà chọn **vừa đủ**. Mô hình càng dẻo càng dễ khớp nhầm và RANSAC cũng phải thử nhiều hơn. Với ảnh chung nguồn, phép biến đổi tương tự hoặc phép biến đổi afin thường là đủ.

---

## Slide 39 · RANSAC trên ví dụ đơn giản
*5 nhịp · khoảng 1,5–2 phút*

*[Nhịp 1/5: dữ liệu.]*

RANSAC dùng để tìm mô hình khi dữ liệu có lẫn điểm phù hợp và điểm ngoại lai. Slide dùng 44 điểm quanh một đường thẳng để minh họa cho dễ nhìn.

*[Bấm →, nhịp 2/5: mẫu có nhiễu.]*

Một đường thẳng cần 2 điểm. Nếu bốc một điểm đúng và một điểm nhiễu thì đường tạo ra sai, chỉ có 2 điểm đồng thuận.

*[Bấm →, nhịp 3/5: mẫu sạch.]*

Nếu bốc trúng hai điểm đúng thì đường gần với cấu trúc thật và có 31 điểm đồng thuận.

*[Bấm →, nhịp 4/5: lặp.]*

RANSAC lặp nhiều lần, giữ giả thuyết có nhiều điểm phù hợp nhất. Phần minh họa chạy 24 vòng; sau đó khớp lại đường bằng toàn bộ 31 điểm phù hợp để ổn định hơn.

*[Bấm →, nhịp 5/5: số vòng.]*

Số vòng phụ thuộc tỉ lệ điểm phù hợp `w`, số điểm tối thiểu `s` và độ tin cậy `p`. Với `w = 0,5`, `p = 0,99`: `s = 2` cần khoảng 17 vòng, còn `s = 4` cần khoảng 72 vòng. Mẫu càng lớn thì càng khó bốc trúng toàn điểm đúng.

---

## Slide 40 · RANSAC trên ảnh thật
*5 nhịp · khoảng 1,5 phút*

*[Nhịp 1/5: mẫu 4 cặp.]*

Giờ áp dụng RANSAC lên cặp ảnh `graf 1` và `graf 3`. Vì bức tường gần phẳng nên dùng phép biến đổi xạ ảnh, mỗi mẫu tối thiểu cần 4 cặp điểm.

*[Bấm →, nhịp 2/5: ước lượng H.]*

Từ 4 cặp đó, hệ thống tính một ma trận ma trận xạ ảnh H. Đây mới chỉ là một giả thuyết.

*[Bấm →, nhịp 3/5: sai số.]*

H được dùng để chiếu các điểm từ ảnh A sang B. Cặp nào lệch không quá 4 px thì được xem là điểm phù hợp của giả thuyết đó.

*[Bấm →, nhịp 4/5: đồng thuận.]*

Mẫu kém chỉ có 5/278 cặp đồng thuận, còn mẫu tốt có 251/278. RANSAC giữ mô hình có nhiều điểm phù hợp hơn.

*[Bấm →, nhịp 5/5: tinh chỉnh.]*

Cuối cùng tính lại H bằng toàn bộ 251 điểm phù hợp, kết quả tăng lên 252/278 cặp đồng thuận. Quy trình là: lấy mẫu → tính H → đo sai số → đếm điểm phù hợp → giữ mẫu tốt nhất → khớp lại.

---

## Slide 41 · So sánh SIFT và ORB
*4 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/4: phát hiện.]*

SIFT phát hiện điểm đặc trưng bằng cực trị DoG trong không gian tỉ lệ. ORB dùng FAST trên tháp ảnh rồi xếp hạng bằng Harris. SIFT nặng hơn nhưng bền hơn với thay đổi tỉ lệ.

*[Bấm →, nhịp 2/4: mô tả.]*

SIFT dùng hướng từ biểu đồ hướng gradient và bộ mô tả đặc trưng 128 số thực, so bằng L2. ORB dùng trọng tâm cường độ và bộ mô tả đặc trưng 256 bit, so bằng Hamming.

*[Bấm →, nhịp 3/4: dung lượng.]*

Một bộ mô tả SIFT khoảng 512 byte, ORB chỉ 32 byte, tức nhẹ hơn 16 lần về dung lượng thô.

*[Bấm →, nhịp 4/4: kết quả.]*

Trên cặp IP102/05382 ở 512 px, SIFT mất 160,8 ms và có 422 điểm phù hợp; ORB mất 42,2 ms và có 331 điểm phù hợp. Trong phép đo này, SIFT có nhiều điểm phù hợp hơn còn ORB nhanh và nhẹ hơn.

---

## Slide 42 · Ứng dụng của so khớp đặc trưng
*12 nhịp · khoảng 3 phút*

### Ghép ảnh toàn cảnh

*[Bước 1/4.]*

Ghép ảnh toàn cảnh bắt đầu từ nhiều ảnh có vùng chồng lấp. Mục tiêu là tìm các chi tiết xuất hiện ở cả hai ảnh.

*[Bước 2/4.]*

SIFT ghép các điểm đặc trưng tương ứng, rồi RANSAC loại các cặp sai.

*[Bước 3/4.]*

Từ các điểm phù hợp, hệ thống ước lượng phép biến đổi xạ ảnh và biến đổi và căn chỉnh các ảnh về cùng hệ tọa độ.

*[Bước 4/4.]*

Cuối cùng phối trộn vùng chồng lấp để giảm đường biên và chênh sáng.

### Định vị đối tượng

*[Bước 1/4.]*

Bên trái là ảnh mẫu, bên phải là cảnh có vật cần tìm nhưng bị thu nhỏ và nghiêng.

*[Bước 2/4.]*

SIFT ghép các chữ, góc và hoa văn trên ảnh mẫu với cảnh, ở đây dùng ngưỡng tỉ số 0,70.

*[Bước 3/4.]*

RANSAC ước lượng phép biến đổi xạ ảnh rồi chiếu bốn góc ảnh mẫu sang cảnh, tạo thành một tứ giác bao quanh vật thể.

*[Bước 4/4.]*

Nhờ vậy mình biết vật thể nằm ở đâu và mặt của nó bị biến đổi phối cảnh như thế nào.

### SLAM

*[Bước 1/4.]*

SLAM là vừa định vị camera vừa xây bản đồ. Hai khung hình liên tiếp nhìn cùng cảnh nhưng các vật đã đổi vị trí trên ảnh vì camera di chuyển.

*[Bước 2/4.]*

ORB ghép điểm đặc trưng giữa các khung hình, rồi dùng ràng buộc cực của ma trận cơ bản để loại các cặp không hợp lý.

*[Bước 3/4.]*

Các đặc trưng được theo dõi qua nhiều khung hình để hỗ trợ ước lượng chuyển động camera và tạo các mốc bản đồ.

*[Bước 4/4.]*

Khi hệ thống nhận ra một nơi đã đi qua, nó tạo ràng buộc khép vòng và tối ưu lại quỹ đạo để giảm sai số tích lũy. ORB chỉ là một thành phần trong toàn bộ hệ SLAM.

**Chốt slide:** Ảnh toàn cảnh, định vị đối tượng và SLAM khác nhau về mục tiêu, nhưng đều cần **các điểm tương ứng đáng tin**.
