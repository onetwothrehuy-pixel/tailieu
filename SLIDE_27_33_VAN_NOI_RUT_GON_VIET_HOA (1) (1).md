# Kịch bản văn nói rút gọn · Slide 27–33

> Theo thứ tự của bản trình chiếu 52 slide. Giữ các ý chính và số liệu đang hiển thị; câu nối được lồng vào từng nhịp và cuối mỗi slide. Phần chữ nghiêng trong ngoặc vuông là chỉ dẫn thao tác, không cần đọc thành lời.

---

## Slide 27 · SIFT dùng Euclid, ORB dùng Hamming

*5 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/5: hiệu thành phần.]*

Tới đây mình đã biết SIFT và ORB tạo bộ mô tả đặc trưng như thế nào. Bây giờ mình cần đo xem hai bộ mô tả giống nhau tới mức nào. SIFT dùng véc-tơ số thực nên mình xét khoảng cách Euclid, hay L2. Ví dụ `a = (1, 2)` và `b = (4, 6)` thì hiệu là `(3, 4)`.

*[Bấm →, nhịp 2/5: L2.]*

Từ hiệu vừa tính, mình bình phương từng thành phần, cộng lại rồi lấy căn: `√(3² + 4²) = 5`.Với kết quả  Khoảng cách càng nhỏ thì hai bộ mô tả SIFT càng gần nhau.

*[Bấm →, nhịp 3/5: XOR.]*

Còn ORB biểu diễn bằng chuỗi 256 bit nên dùng khoảng cách Hamming. Trước hết, mình XOR hai chuỗi: vị trí có bit giống nhau cho 0, khác nhau cho 1. Hình chỉ dùng 8 bit để dễ quan sát.

*[Bấm →, nhịp 4/5: Hamming.]*

Có kết quả XOR rồi thì mình chỉ cần đếm số bit 1. Trong ví dụ có 3 vị trí khác nhau, nên Hamming bằng 3. Số bit khác nhau càng ít thì hai bộ mô tả ORB càng giống nhau.

*[Bấm →, nhịp 5/5: thời gian thật.]*

Vậy khi đo thực tế thì sao? Với cặp ảnh 960 px, tổng thời gian các công đoạn trên hình là khoảng 225,9 ms với SIFT và 32,4 ms với ORB. Riêng bước so khớp chỉ là 12,5 ms và 10,7 ms. Chênh lệch lớn chủ yếu nằm ở khâu phát hiện và mô tả, chứ không chỉ ở cách tính khoảng cách.

**Lưu ý:** L2 và Hamming là hai cách đo khác nhau, không so trực tiếp trị số của chúng trên cùng một thang.

**Chuyển sang slide 28:** Từ Khoảng cách giúp mình tìm ứng viên gần nhất. Tiếp theo, mình xem ứng viên đó có nổi bật hơn các lựa chọn còn lại không.

---

## Slide 28 · Phép kiểm tra tỉ số loại các ghép nối mơ hồ

*4 nhịp · khoảng 1 phút*

*[Nhịp 1/4: hai láng giềng gần nhất.]*

Với mỗi điểm ở ảnh A, mình tìm hai ứng viên gần nhất ở ảnh B. Khoảng cách đến ứng viên thứ nhất là `d₁`, đến ứng viên thứ hai là `d₂`. Mình giữ cả hai để xem lựa chọn đứng đầu có thật sự rõ ràng không.

*[Bấm →, nhịp 2/4: khoảng cách.]*

Nhìn vào ví dụ đầu tiên, `d₁ = 144,7`, còn `d₂ = 354,9`. Ứng viên thứ nhất gần hơn khá nhiều, nên kết quả này ít mơ hồ hơn trường hợp hai khoảng cách gần bằng nhau.

*[Bấm →, nhịp 3/4: ngưỡng τ.]*

Để quyết định giữ hay bỏ, mình lấy `d₁/d₂ = 0,408`, rồi so với ngưỡng `τ`, đọc là “tau”. Vì `0,408 < 0,75` nên cặp này được giữ. Đây là phép kiểm tra tỉ số, hay ratio test.

*[Bấm →, nhịp 4/4: mơ hồ.]*

Bây giờ đổi sang ví dụ khó hơn: tỉ số là `0,970`, rất gần 1. Hai ứng viên gần như ngang nhau nên cặp này bị loại. Tỉ số chỉ thể hiện mức độ nổi bật của ứng viên gần nhất, không phải xác suất ghép đúng.

**Chuyển sang slide 29:** Mình vừa dùng ngưỡng 0,75. Nếu tăng hoặc giảm ngưỡng này thì kết quả sẽ thay đổi thế nào?

---

## Slide 29 · Chọn τ: đánh đổi số cặp và độ đúng

*4 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/4: cặp đúng.]*

Mình xem phân bố tỉ số trên cặp ảnh `graf 1 → graf 3`. Các cặp ghép đúng thường có `d₁/d₂` nhỏ, vì ứng viên gần nhất nổi bật hơn ứng viên thứ hai. Ở đây, cặp đúng được xác định bằng phép biến đổi tham chiếu của bộ dữ liệu.

*[Bấm →, nhịp 2/4: cặp sai.]*

Đặt thêm các cặp sai lên biểu đồ thì thấy chúng tập trung nhiều gần 1. Nghĩa là hai ứng viên gần như ngang nhau. Tuy vậy, hai phân bố vẫn chồng lấp, nên một ngưỡng tỉ số chưa thể loại hết cặp sai.

*[Bấm →, nhịp 3/4: kéo τ.]*

Giờ mình đặt ngưỡng để lọc. Với `τ = 0,75`, có 278 cặp được giữ, trong đó 237 cặp đúng, tương đương 85,3%. Có thể kéo thanh ngưỡng để thấy số cặp giữ lại thay đổi.

*[Bấm →, nhịp 4/4: đánh đổi.]*

Khi tăng lên `τ = 0,8`, mình giữ được 367 cặp, nhưng tỉ lệ đúng giảm còn 78,2%. Trong ví dụ này, ngưỡng nhỏ cho ít cặp hơn nhưng sạch hơn; ngưỡng lớn giữ nhiều cặp hơn và cũng lẫn sai nhiều hơn. Vì vậy, mình chọn ngưỡng theo yêu cầu của bài toán.

Tuy nhiên, với `τ = 0,75`, trong 278 cặp được giữ chỉ có 237 cặp đúng, tức **vẫn còn 41 cặp sai**. Ratio test chỉ xét độ giống của bộ mô tả: một cặp có thể trông rất giống nhưng lại sai vị trí.

**Chuyển sang slide 30:** Để nhận ra các cặp sai còn sót, mình sẽ kiểm tra xem các điểm có cùng tuân theo một quy luật biến đổi vị trí hay không.

---

## Slide 30 · Chọn mô hình hình học

*5 nhịp · khoảng 1,5 phút*

*[Nhịp 1/5: phép biến đổi tương tự.]*

Ratio test đã lọc theo độ giống của bộ mô tả, nhưng vẫn có cặp sai vị trí. Muốn kiểm tra vị trí, trước hết mình phải chọn **loại quy luật biến đổi** phù hợp với hai ảnh. Đơn giản nhất ở đây là phép biến đổi tương tự: gồm xoay, co giãn đều và dịch chuyển. Mô hình có 4 bậc tự do, tối thiểu cần 2 cặp điểm. Nó phù hợp với các phiên bản xoay hoặc đổi kích thước từ cùng một ảnh nguồn, kể cả khi chỉ giữ lại một vùng ảnh.

*[Bấm →, nhịp 2/5: phép biến đổi afin.]*

Nếu ảnh còn bị co giãn không đều hoặc xô nghiêng, mình dùng phép biến đổi afin. Mô hình này có 6 bậc tự do và cần tối thiểu 3 cặp điểm. Một tính chất dễ nhớ là các đường song song vẫn song song.

*[Bấm →, nhịp 3/5: phép biến đổi xạ ảnh.]*

Khi có thêm thay đổi phối cảnh của một mặt phẳng, mình dùng phép biến đổi xạ ảnh, hay homography, ký hiệu là `H`. Mô hình có 8 bậc tự do và cần tối thiểu 4 cặp điểm.  Một tính chất dễ nhớ là các đường song song lúc này có thể hội tụ về một điểm.

*[Bấm →, nhịp 4/5: ma trận cơ bản.]*

Còn với cảnh 3D và camera dịch chuyển, một phép xạ ảnh thường không mô tả được toàn bộ cảnh. Ma trận cơ bản, hay fundamental, ràng buộc điểm tương ứng ở ảnh B phải nằm trên một đường cực. Nó có 7 bậc tự do, cần 7–8 cặp điểm tùy cách ước lượng.

*[Bấm →, nhịp 5/5: chọn mô hình.]*

Qua các trường hợp vừa rồi, ý chính là chọn mô hình vừa đủ với biến đổi của ảnh. Mô hình quá linh hoạt có thể khớp cả các cặp ngẫu nhiên, đồng thời cần nhiều điểm trong mỗi mẫu. Với ảnh cùng nguồn chỉ xoay, đổi cỡ hoặc xô nghiêng, mô hình tương tự hoặc afin thường đã đủ. Ở slide này mình mới **chọn loại mô hình**, chưa tìm ra các tham số biến đổi cụ thể.

**Chuyển sang slide 31:** RANSAC sẽ thử ước lượng phép biến đổi cụ thể từ các cặp đã ghép, rồi tìm nhóm cặp cùng phù hợp với phép biến đổi đó.

---

## Slide 31 · RANSAC trên ví dụ đơn giản

*5 nhịp · khoảng 1,5–2 phút*

*[Nhịp 1/5: dữ liệu.]*

Các cặp qua ratio test vẫn có thể sai về vị trí. Với loại mô hình đã chọn, RANSAC tìm quy luật được nhiều cặp cùng ủng hộ và loại những cặp lệch khỏi quy luật. Để dễ hiểu cách làm, mình tạm dùng bài toán tìm một đường thẳng. Hình có 44 điểm: phần lớn nằm quanh một đường, số còn lại là nhiễu. Mục tiêu là tìm đường phù hợp với nhóm điểm chính mà không bị các điểm nhiễu kéo lệch.

*[Bấm →, nhịp 2/5: mẫu có nhiễu.]*

Một đường thẳng chỉ cần 2 điểm để xác định, nên mình thử chọn ngẫu nhiên 2 điểm. Nếu chọn một điểm đúng và một điểm nhiễu, đường tạo ra bị lệch. Trong ví dụ này, chỉ có 2 điểm nằm đủ gần đường để được tính là đồng thuận.

*[Bấm →, nhịp 3/5: mẫu sạch.]*

Thử lại với hai điểm thuộc nhóm chính thì kết quả tốt hơn. Đường đi gần cấu trúc thật và có 31 điểm đồng thuận. Những điểm phù hợp với mô hình trong một ngưỡng sai số được gọi là inlier.

*[Bấm →, nhịp 4/5: lặp và giữ tốt nhất.]*

Vì không biết trước mẫu nào tốt, RANSAC lặp nhiều lần rồi giữ giả thuyết có nhiều điểm đồng thuận nhất. Minh họa này chạy 24 vòng, giữ nhóm 31 điểm phù hợp, sau đó khớp lại đường bằng cả nhóm để kết quả ổn định hơn. Trên hai ảnh thật, các cặp không nhất quán với cùng phép biến đổi hình học sẽ bị xem là ngoại lai.

*[Bấm →, nhịp 5/5: số vòng cần.]*

Vậy cần thử bao nhiêu lần? Số vòng phụ thuộc tỉ lệ điểm đúng `w`, số điểm trong mỗi mẫu `s` và độ tin cậy mong muốn `p`. Với `w = 0,5`, `p = 0,99`, mẫu 2 điểm cần khoảng 17 vòng, còn mẫu 4 điểm cần khoảng 72 vòng. Mẫu càng lớn thì càng khó chọn trúng toàn điểm đúng.

**Chuyển sang slide 32:** Như vậy, mình đã đi qua cách mô tả, ghép điểm và kiểm tra hình học. Giờ mình đặt SIFT và ORB cạnh nhau để nhìn rõ sự khác biệt.

---

## Slide 32 · So sánh SIFT và ORB

*4 nhịp · khoảng 1–1,5 phút*

*[Nhịp 1/4: phát hiện.]*

Trước hết là cách tìm điểm đặc trưng. SIFT tìm cực trị DoG trong không gian tỉ lệ. ORB dùng FAST trên tháp ảnh rồi xếp hạng bằng Harris. SIFT xử lý tỉ lệ kỹ hơn, còn ORB hướng đến giảm chi phí tính toán.

*[Bấm →, nhịp 2/4: mô tả.]*

Sau khi tìm điểm, hai phương pháp cũng gán hướng và mô tả khác nhau. SIFT dùng biểu đồ 36 ngăn hướng gradient, tạo véc-tơ 128 số thực và so bằng L2. ORB dùng trọng tâm cường độ, tạo chuỗi 256 bit và so bằng Hamming.

*[Bấm →, nhịp 3/4: dung lượng.]*

Khác biệt cách biểu diễn dẫn đến khác biệt bộ nhớ. Một bộ mô tả SIFT dạng float32 chiếm 512 byte, còn ORB là 32 byte, ít hơn 16 lần. Đây là dung lượng thô của bộ mô tả, không có nghĩa ORB sẽ chạy nhanh hơn đúng 16 lần.

*[Bấm →, nhịp 4/4: kết quả.]*

Nhìn vào phép đo trên cặp `IP102/05382`, cạnh dài 512 px: SIFT mất 160,8 ms và có 422 cặp phù hợp theo mô hình `H`; ORB mất 42,2 ms và có 331 cặp. Trong phép đo này, SIFT giữ được nhiều cặp phù hợp hơn, còn ORB nhanh và gọn hơn. Các số này thuộc cặp ảnh và cấu hình đang xét, không đại diện cho mọi trường hợp.

**Chuyển sang slide 33:** Vậy những cặp điểm tương ứng này dùng để làm gì? Mình xem ba ứng dụng cụ thể ngay sau đây.

---

## Slide 33 · Ứng dụng của so khớp đặc trưng

*12 nhịp · 3 ứng dụng, mỗi ứng dụng 4 bước · khoảng 3 phút*

### Ghép ảnh toàn cảnh

*[Nhịp 1/12 · bước 1/4: ảnh chồng lấp.]*

Ứng dụng đầu tiên là ghép ảnh toàn cảnh. Trên hình là bốn ảnh chụp các phần của cùng một tờ báo, có vùng chồng lấp. Mình cần tìm các chi tiết xuất hiện ở cả hai ảnh liền kề.

*[Bấm →, nhịp 2/12 · bước 2/4: nối điểm chung.]*

Từ những vùng chung đó, SIFT tìm và ghép các điểm đặc trưng tương ứng. RANSAC giúp giữ lại những cặp phù hợp về hình học. Mỗi đường màu trên hình nối một cặp điểm tương ứng.

*[Bấm →, nhịp 3/12 · bước 3/4: căn chỉnh ảnh.]*

Có các cặp điểm phù hợp rồi, mình ước lượng phép biến đổi xạ ảnh `H` và đưa các ảnh về cùng hệ tọa độ. Lúc này, các chi tiết chung dần nằm trùng lên nhau.

*[Bấm →, nhịp 4/12 · bước 4/4: phối trộn.]*

Nhưng căn đúng vị trí vẫn có thể còn đường nối hoặc chênh sáng. Vì vậy, bước cuối là phối trộn vùng chồng lấp để phần ghép nhìn liền mạch hơn.

**Nối sang ứng dụng tiếp theo:** Ngoài ghép nhiều ảnh thành một ảnh, mình còn có thể dùng các điểm tương ứng để tìm một vật thể trong cảnh.

### Định vị đối tượng

*[Bấm →, nhịp 5/12 · bước 1/4: ảnh mẫu và cảnh.]*

Ở đây, bên trái là ảnh mẫu của mặt hộp, bên phải là cảnh có nhiều vật thể. Mặt hộp trong cảnh nhỏ hơn và nhìn nghiêng hơn. Mục tiêu là tìm đúng vật thể đã có ảnh mẫu.

*[Bấm →, nhịp 6/12 · bước 2/4: ghép đặc trưng.]*

Để tìm lại nó, SIFT ghép các góc chữ và họa tiết từ ảnh mẫu sang ảnh cảnh. Ví dụ này dùng ngưỡng kiểm tra tỉ số 0,70; hình chỉ hiện một số cặp phù hợp để dễ theo dõi.

*[Bấm →, nhịp 7/12 · bước 3/4: ước lượng H.]*

Từ các cặp điểm đó, RANSAC ước lượng `H`. Mình chiếu bốn góc của ảnh mẫu sang ảnh cảnh, tạo thành một tứ giác bao quanh mặt hộp.

*[Bấm →, nhịp 8/12 · bước 4/4: định vị.]*

Khi đưa ảnh mẫu chồng lên cảnh, các chi tiết tương ứng khớp với nhau. Nhờ vậy, mình xác định được vị trí và hình dạng chiếu của mặt hộp trong ảnh.

**Nối sang ứng dụng tiếp theo:** Hai ví dụ vừa rồi làm việc với mặt phẳng. Với camera di chuyển trong cảnh 3D, các điểm tương ứng còn hỗ trợ định vị và lập bản đồ.

### SLAM · Vừa định vị, vừa lập bản đồ

*[Bấm →, nhịp 9/12 · bước 1/4: khung hình thật.]*

SLAM là vừa xác định vị trí camera vừa xây dựng bản đồ. Hai khung hình trên slide lấy từ video thật: cùng những chi tiết như màn hình và bàn phím, nhưng vị trí trên ảnh thay đổi khi camera di chuyển.

*[Bấm →, nhịp 10/12 · bước 2/4: theo dõi điểm.]*

Để nối thông tin giữa các khung hình, ORB phát hiện và ghép các điểm đặc trưng. Sau đó, ràng buộc hình học cực giúp loại những cặp không phù hợp, tạo cơ sở cho việc ước lượng chuyển động camera.

*[Bấm →, nhịp 11/12 · bước 3/4: camera và bản đồ.]*

Khi theo dõi qua nhiều khung hình, các điểm này hỗ trợ ước lượng vị trí camera và xây dựng các mốc bản đồ. Sơ đồ quỹ đạo và các mốc bên phải chỉ minh họa cơ chế, không phải kết quả chạy một hệ SLAM hoàn chỉnh trên video này.

*[Bấm →, nhịp 12/12 · bước 4/4: khép vòng.]*

Nếu nhận ra một nơi đã đi qua, hệ thống có thể thêm ràng buộc khép vòng rồi tối ưu lại quỹ đạo và bản đồ để giảm sai số tích lũy. Phần khép vòng trên hình cũng là mô phỏng; ORB chỉ là một thành phần trong toàn bộ hệ SLAM.

**Chốt slide:** Ba ứng dụng có mục tiêu khác nhau, nhưng đều cần các điểm tương ứng đáng tin để xử lý các bước tiếp theo.

**Chuyển sang slide 34:** Sau các ví dụ ứng dụng, mình chuyển sang thực nghiệm để xem SIFT và ORB tìm lại chi tiết ra sao khi ảnh bị biến đổi.
