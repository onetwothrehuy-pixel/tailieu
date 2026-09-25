### Slide 38 · Chọn mô hình hình học
*5 nhịp · khoảng 3–4 phút nếu giải thích đầy đủ*

Trước khi đi từng mô hình, mình để ý một chuyện quan trọng. Ở mấy slide trước, SIFT hoặc ORB đã ghép cho mình một loạt cặp điểm giữa ảnh A và ảnh B. Nhưng descriptor giống nhau chưa có nghĩa là cặp đó chắc chắn đúng, vì những vùng có hoa văn giống nhau vẫn có thể bị ghép nhầm. Cho nên tới đây mình cần hỏi thêm một câu: **nếu hai ảnh thật sự có liên quan, những cặp điểm đúng có cùng tuân theo một quy luật hình học hay không?** Quy luật đó chính là mô hình hình học.

*[Nhịp 1/5: mô hình tương tự.]*

Đầu tiên là **mô hình tương tự**, hay similarity. Mình có thể hiểu rất đơn giản là lấy nguyên tấm ảnh rồi làm ba chuyện: xoay nó, phóng to hoặc thu nhỏ **đều theo hai chiều**, rồi kéo nó qua trái, phải, lên hoặc xuống. Hình dạng cơ bản không bị bóp méo; ví dụ hình vuông sau biến đổi vẫn là hình vuông, chỉ khác kích thước, góc xoay và vị trí.

Mô hình này có **4 bậc tự do**. Bốn con số cần tìm là một góc xoay, một hệ số tỉ lệ, một độ dịch theo trục x và một độ dịch theo trục y. Vì mỗi cặp điểm cho mình hai tọa độ x và y, về tối thiểu chỉ cần **2 cặp điểm** là có đủ ràng buộc để ước lượng mô hình.

Cái này rất hợp với bài toán ảnh chung nguồn của nhóm. Ví dụ mình lấy một ảnh bướm, rồi crop, xoay, resize hoặc dời vị trí. Nội dung của vật thể chưa bị biến dạng phối cảnh phức tạp, nên similarity thường đã mô tả đúng bản chất của phép biến đổi.

*[Bấm →, nhịp 2/5: affine.]*

Nếu mình cần linh hoạt hơn một chút thì có **Affine**. Affine làm được những gì similarity làm, nhưng cho phép thêm **co giãn không đều** và **shear**, tức là xô nghiêng hình.

Ví dụ với similarity, nếu phóng lên hai lần thì chiều ngang và chiều dọc cùng tăng theo một tỉ lệ. Còn affine có thể kéo ngang nhiều hơn kéo dọc, nên một hình vuông có thể thành hình chữ nhật hoặc hình bình hành. Tuy vậy, có một tính chất dễ nhớ là **đường song song vẫn giữ song song**.

Affine có **6 bậc tự do**, nên tối thiểu cần **3 cặp điểm**. Với ảnh chung nguồn mà bị resize méo nhẹ, thay đổi tỉ lệ hai chiều khác nhau hoặc có chút shear thì affine hợp lý hơn similarity.

*[Bấm →, nhịp 3/5: homography.]*

Mạnh hơn nữa là **Homography**. Cái này dùng để mô tả **phối cảnh của một mặt phẳng**. Mình hình dung có một tờ báo hình chữ nhật. Nếu chụp thẳng từ trên xuống thì nó nhìn gần như hình chữ nhật; nhưng nếu chụp xiên thì cạnh gần camera nhìn lớn hơn, cạnh xa nhìn nhỏ hơn, và tờ báo có thể thành hình thang. Similarity với affine không mô tả đầy đủ kiểu phối cảnh này, còn homography thì làm được.

Homography có **8 bậc tự do** và tối thiểu cần **4 cặp điểm**. Một đặc điểm dễ nhìn là các đường vốn song song trên mặt phẳng có thể nhìn như đang hội tụ trong ảnh, giống hai đường ray nhìn ra xa. Vì vậy ghép panorama hoặc định vị một trang báo, bìa sách hay mặt phẳng trong cảnh thường dùng homography.

*[Bấm →, nhịp 4/5: fundamental.]*

Còn khi cảnh không còn là một mặt phẳng mà là **không gian 3D**, đồng thời camera thật sự di chuyển, mình cần một loại ràng buộc khác là **ma trận fundamental**.

Lúc này một điểm ở ảnh A không còn ánh xạ đơn giản sang đúng một điểm cố định ở ảnh B bằng một phép biến đổi phẳng. Thay vào đó, điểm ở A sẽ xác định một **đường epipolar** trên ảnh B; điểm tương ứng đúng phải nằm trên đường đó. Nói nôm na là mình chưa biết chính xác nó ở đâu trên ảnh B, nhưng mình thu hẹp vùng tìm kiếm từ cả ảnh xuống còn một đường.

Fundamental có **7 bậc tự do** và được dùng trong các bài toán nhiều góc nhìn như SLAM, nơi camera di chuyển trong môi trường 3D.

*[Bấm →, nhịp 5/5: chọn mô hình.]*

Ý quan trọng nhất của slide này là: **không phải mô hình càng nhiều bậc tự do thì càng tốt**. Mình nên chọn mô hình **vừa đủ** để giải thích loại biến đổi đang có.

Mô hình càng “dẻo” thì càng có khả năng uốn theo cả một số cặp ghép sai. Đồng thời mô hình phức tạp hơn thường cần nhiều cặp điểm hơn cho một mẫu tối thiểu. Qua slide kế tiếp mình sẽ thấy chuyện đó làm RANSAC phải thử nhiều vòng hơn, vì xác suất bốc trúng một mẫu mà tất cả điểm đều đúng sẽ thấp xuống.

Cho nên với bài toán của nhóm là tìm các phiên bản sinh ra từ cùng một ảnh nguồn, **mô hình tương tự hoặc affine thường là đủ**. Chỉ khi bài toán thật sự có phối cảnh mặt phẳng thì mình mới cần homography; còn cảnh 3D nhiều độ sâu và camera dịch chuyển thì mới nghĩ tới fundamental.

**Nếu được hỏi** vì sao homography cần 4 cặp: xem phụ lục slide 59. Có thể trả lời ngắn: homography có 8 bậc tự do; mỗi cặp điểm cho 2 phương trình, nên tối thiểu cần 4 cặp không suy biến.

### Slide 39 · RANSAC trên ví dụ đơn giản
*5 nhịp · khoảng 4–5 phút nếu giải thích đầy đủ*

Sau khi chọn được mô hình, mình vẫn còn một vấn đề: trong các cặp điểm ghép được luôn có thể lẫn **inlier** với **outlier**. Inlier là điểm phù hợp với cấu trúc hình học thật; outlier là điểm nhiễu hoặc ghép sai. Nếu đem tất cả điểm, kể cả outlier, vào fit mô hình một lần thì mấy điểm sai có thể kéo mô hình lệch. RANSAC giải quyết đúng chỗ này.

RANSAC là viết tắt của **Random Sample Consensus**. Mình có thể nhớ bằng một câu: **bốc một mẫu nhỏ → dựng giả thuyết → hỏi toàn bộ dữ liệu có bao nhiêu điểm đồng thuận → lặp lại → giữ giả thuyết tốt nhất**.

*[Nhịp 1/5: dữ liệu.]*

Để thấy cơ chế cho dễ, slide chưa dùng ảnh thật mà dùng bài toán đơn giản nhất là **khớp một đường thẳng**. Có **44 điểm**, phần lớn nằm quanh một đường, còn số còn lại là nhiễu.

Nếu nhìn bằng mắt thì mình thấy ngay có một xu hướng chính, nhưng máy ban đầu không biết điểm nào đúng, điểm nào sai. Mình chỉ cho máy một ngưỡng sai số: điểm nào nằm đủ gần mô hình thì tính là inlier; điểm nào nằm xa hơn ngưỡng thì xem là outlier.

Một đường thẳng tối thiểu chỉ cần **2 điểm**, nên mỗi vòng RANSAC sẽ bốc ngẫu nhiên 2 điểm để dựng một đường thử. Đường vừa dựng chưa phải kết quả cuối, nó chỉ là một **giả thuyết**.

*[Bấm →, nhịp 2/5: mẫu có điểm nhiễu.]*

Giả sử lần đầu mình bốc trúng **một điểm đúng với một điểm nhiễu**. Hai điểm nào cũng dựng được một đường thẳng, nên về mặt toán học vẫn có một đường. Nhưng đường này không đại diện cho cấu trúc chính của dữ liệu.

RANSAC sẽ đem đường đó đi kiểm tra lại trên toàn bộ 44 điểm. Kết quả ở đây chỉ có **2 điểm** nằm trong dải sai số cho phép. Nói nôm na là hai điểm mình vừa bốc tự đồng ý với nhau, còn gần như cả đám còn lại không ủng hộ. Consensus nhỏ như vậy thì giả thuyết này yếu và sẽ không được giữ.

*[Bấm →, nhịp 3/5: mẫu sạch.]*

Bây giờ thử lại một vòng khác. Lần này may mắn bốc được **hai điểm đều đúng**, tức một **mẫu sạch**. Hai điểm đó nằm theo cấu trúc thật nên đường dựng ra gần với xu hướng chính của dữ liệu.

Khi đem đường này đi hỏi lại toàn bộ 44 điểm thì có **31 điểm đồng thuận**. Mình thấy sự khác biệt rất rõ: mẫu xấu chỉ được 2 phiếu, còn mẫu sạch được 31 phiếu. RANSAC không cần biết trước điểm nào là đúng; nó dựa vào số lượng điểm cùng ủng hộ một giả thuyết để nhận ra cấu trúc chính.

*[Bấm →, nhịp 4/5: lặp. Chờ bộ đếm chạy hết.]*

Nhưng mình không thể bốc đúng một lần rồi tin ngay, vì lấy mẫu ngẫu nhiên có thể xui trúng outlier. Cho nên RANSAC **lặp nhiều vòng**. Trong demo này nó chạy **24 vòng**, mỗi vòng tạo một giả thuyết rồi đếm số điểm đồng thuận.

Sau cùng, RANSAC giữ giả thuyết có nhiều phiếu nhất, ở đây là **31 điểm**. Nhưng mình để ý là đường thắng ban đầu chỉ được dựng từ 2 điểm. Một khi đã biết 31 điểm nào là inlier, mình không cần dùng riêng 2 điểm cũ nữa; mình **khớp lại đường trên toàn bộ 31 inlier** để có mô hình ổn định hơn. Đường nét đứt tím trên slide là kết quả tinh chỉnh đó.

Có thể hiểu RANSAC giống như vòng tuyển người: đầu tiên thử tìm đúng “phe”, sau đó khi đã biết ai thuộc phe đúng thì dùng cả nhóm đó để tính mô hình cho chuẩn hơn.

*[Bấm →, nhịp 5/5: số vòng.]*

Câu tiếp theo là: **phải lặp bao nhiêu vòng mới đủ?** Công thức trên slide là:

N = log(1 − p) / log(1 − wˢ)

Trong đó `p` là mức tin cậy mình muốn, `w` là tỉ lệ inlier trong dữ liệu, còn `s` là số điểm tối thiểu của một mẫu.

Trực giác của công thức rất đơn giản. Nếu một điểm có xác suất `w` là điểm đúng, thì một mẫu gồm `s` điểm mà **tất cả đều đúng** có xác suất là `wˢ`. Mẫu càng lớn thì xác suất lấy trúng một mẫu sạch càng giảm.

Ví dụ slide đặt `w = 0,5`, tức khoảng một nửa dữ liệu là inlier, và muốn mức tin cậy **99%**. Nếu mô hình chỉ cần `s = 2` điểm thì cần khoảng **17 vòng**. Nhưng nếu mô hình cần `s = 4` điểm thì phải tăng lên khoảng **72 vòng**.

Đây là chỗ nối lại slide 38: mô hình cần càng nhiều điểm tối thiểu thì RANSAC thường càng phải thử nhiều lần. Vì vậy mình không chọn mô hình phức tạp hơn mức cần thiết.

*[Bấm → sang slide 40: RANSAC trên ảnh thật.]*

**Lưu ý:** dữ liệu ở slide này là dữ liệu tổng hợp, seed cố định. Chứng minh công thức số vòng ở phụ lục slide 58. Số vòng trong demo là 24; còn 17 và 72 ở nhịp cuối là ví dụ từ công thức với `w = 0,5`, `p = 0,99`.

### Slide 40 · RANSAC chọn mô hình hình học phù hợp
*5 nhịp · khoảng 3–4 phút nếu giải thích đầy đủ*

Ở slide 39 mình dùng đường thẳng để nhìn cho dễ. Slide này làm đúng y chang cơ chế đó nhưng chuyển sang **ảnh thật** và một mô hình phức tạp hơn. Cặp ảnh là `graf 1` và `graf 3`, cùng một bức tường graffiti được chụp từ hai góc khác nhau. Vì phần tường đang xét gần như một mặt phẳng, mô hình phù hợp ở đây là **homography**.

*[Nhịp 1/5: mẫu 4 cặp.]*

Homography tối thiểu cần **4 cặp điểm**, nên mỗi vòng RANSAC sẽ lấy ngẫu nhiên 4 cặp match không suy biến. Ở mẫu đầu tiên trên slide, bốn cặp được chọn là **135, 99, 276 và 79**.

Mình có thể hiểu bốn cặp này như bốn “mốc” tạm thời. RANSAC đang giả sử rằng cả bốn cặp đều đúng, rồi hỏi: “Nếu bốn cặp này thật sự cùng nằm trên một quan hệ phối cảnh, vậy homography suy ra từ chúng có giải thích được các cặp còn lại hay không?” Nếu trong bốn cặp có cặp ghép sai thì H tính ra thường sẽ lệch và rất ít điểm khác ủng hộ.

*[Bấm →, nhịp 2/5: ước lượng H.]*

Từ 4 cặp đó, hệ thống ước lượng được một **ma trận H kích thước 3 × 3**, đang hiện ở khung bên phải. H chính là phép biến đổi dùng để đưa một điểm trên ảnh A sang vị trí dự đoán của nó trên ảnh B theo mô hình phối cảnh.

Điểm quan trọng là: mình chưa khẳng định H này đúng. Đây mới chỉ là **một giả thuyết** được sinh ra từ một mẫu ngẫu nhiên, y chang đường thẳng ở slide 39.

*[Bấm →, nhịp 3/5: sai số.]*

Bây giờ mình dùng H đó để **chiếu tất cả các điểm từ ảnh A sang ảnh B**. Với mỗi cặp match, H cho ra một vị trí dự đoán; còn descriptor matching đã cho mình vị trí quan sát thật trên ảnh B. Mình đo khoảng cách giữa hai vị trí này.

Nếu sai số **không quá 4 pixel** thì cặp đó được xem là phù hợp với giả thuyết, tức là một inlier của H hiện tại. Nếu lệch xa hơn thì xem là outlier đối với giả thuyết đó.

Trên hình có thể hiểu dấu cộng là chỗ H dự đoán, còn chấm là chỗ mình quan sát được. Hai cái càng sát nhau thì mô hình càng giải thích tốt cặp điểm đó.

*[Bấm →, nhịp 4/5: đồng thuận. Có thể bấm “Mẫu kém”, “Mẫu 2”, “Mẫu tốt”.]*

Giờ mình so các giả thuyết. **Mẫu đầu tiên chỉ có 5 trong 278 cặp đồng thuận**. Nghĩa là H từ mẫu đó không giải thích được phần lớn dữ liệu, nên đây là một giả thuyết kém.

Còn một mẫu tốt gồm các cặp **23, 193, 251 và 65** thì cho tới **251 cặp đồng thuận trên tổng 278 cặp**. Khi một H được hơn 250 cặp cùng ủng hộ, trong khi giả thuyết khác chỉ được vài cặp, mình có cơ sở rất mạnh để giữ H này.

Đó chính là tinh thần của RANSAC: không hỏi mẫu ban đầu có đẹp hay không, mà hỏi **cả tập dữ liệu có cùng đồng thuận với mô hình sinh ra từ mẫu đó hay không**.

*[Bấm →, nhịp 5/5: tinh chỉnh.]*

Cuối cùng, mình lại làm bước giống slide 39. H tốt nhất ban đầu chỉ được tính từ 4 cặp, nhưng bây giờ mình đã tìm ra **251 inlier**. Hệ thống dùng toàn bộ 251 cặp này để **tính lại H**, thay vì chỉ dựa trên 4 cặp ban đầu.

Sau khi tinh chỉnh, số cặp phù hợp tăng lên **252 trong 278**. Đây là mô hình cuối ổn định hơn.

Vậy nguyên quy trình có thể nhớ như vầy: **lấy 4 cặp → tính H → chiếu toàn bộ điểm → đo sai số → đếm inlier → lặp nhiều mẫu → giữ H tốt nhất → tính lại H bằng toàn bộ inlier**. Slide 39 với slide 40 thật ra cùng một thuật toán; slide 39 dùng đường thẳng cho dễ hiểu, còn slide này thay đường thẳng bằng homography trên ảnh thật.

**Lưu ý:** các giả thuyết trên slide được chọn trước để phục vụ giảng giải, không phải toàn bộ lịch sử của một lần chạy RANSAC. Ảnh graffiti được làm mờ để các điểm nổi lên.

### Slide 41 · So sánh SIFT và ORB
*4 nhịp · khoảng 3 phút nếu giải thích đầy đủ*

Sau khi đi hết từ phát hiện, mô tả, so khớp tới kiểm tra hình học, slide này đặt SIFT và ORB cạnh nhau để mình thấy rõ: **hai thuật toán cùng giải một mục tiêu nhưng chọn những phép tính rất khác nhau**, nên đổi lại tốc độ, bộ nhớ và độ bền cũng khác nhau.

*[Nhịp 1/4: phát hiện.]*

Đầu tiên là **phát hiện keypoint**. SIFT xây không gian tỉ lệ rồi tìm **cực trị DoG**, nên ngay trong quá trình phát hiện nó đã tìm vị trí và tỉ lệ phù hợp của chi tiết. Đây là lý do SIFT bền hơn khi ảnh thay đổi kích thước mạnh, nhưng đổi lại phải tính Gaussian, DoG và tìm cực trị qua nhiều mức nên tốn chi phí hơn.

ORB chọn hướng nhẹ hơn. Nó xây **pyramid**, dùng **FAST** để tìm góc nhanh ở từng mức, rồi dùng điểm **Harris** để xếp hạng và giữ các keypoint tốt hơn. Tức là cùng cần keypoint đa tỉ lệ, nhưng ORB thay chuỗi phép tính nặng của SIFT bằng những phép rẻ hơn.

*[Bấm →, nhịp 2/4: mô tả.]*

Tiếp theo là **gán hướng và descriptor**. SIFT xác định hướng bằng histogram gradient **36 ngăn**, rồi mô tả vùng quanh keypoint thành **128 số thực**. Khi so hai descriptor SIFT, mình dùng khoảng cách Euclid hay L2.

ORB thì tính hướng bằng **trọng tâm cường độ**, sau đó dùng rBRIEF tạo một descriptor **256 bit**. So hai descriptor ORB chỉ cần XOR rồi đếm số bit khác nhau, tức khoảng cách Hamming. Vì vậy phần biểu diễn và so sánh của ORB rất gọn, hợp với những hệ cần chạy nhanh hoặc lưu rất nhiều điểm.

*[Bấm →, nhịp 3/4: dung lượng.]*

Về dung lượng thô, một descriptor SIFT có **128 số float32**. Mỗi số 4 byte nên tổng cộng là **512 byte cho một điểm**. Descriptor ORB có **256 bit**, tức **32 byte**.

Như vậy xét riêng phần descriptor thô thì ORB ít hơn **16 lần**. Điều này rất có ý nghĩa trong những ứng dụng như SLAM, nơi bản đồ có thể lưu rất nhiều keypoint và descriptor. Nhưng mình lưu ý: ít byte hơn 16 lần **không đồng nghĩa toàn bộ thuật toán sẽ chạy nhanh hơn đúng 16 lần**, vì thời gian còn nằm ở phát hiện keypoint, xây pyramid, matching, RANSAC và nhiều chi phí khác.

*[Bấm →, nhịp 4/4: kết quả.]*

Cuối cùng là số đo thực tế của chính bài này trên cặp **IP102/05382**, ảnh cạnh dài **512 px**. Thời gian trung vị cho toàn quy trình là **160,8 ms với SIFT** và **42,2 ms với ORB**. Nghĩa là ở phép đo này ORB nhanh hơn rõ rệt.

Nhưng đổi lại số inlier của SIFT là **422**, còn ORB là **331**. Tức là trên cặp ảnh cụ thể này, SIFT tốn thời gian hơn nhưng giữ được nhiều tương ứng hình học hơn. Mình không nên rút ra một tỉ lệ cố định kiểu “ORB lúc nào cũng nhanh hơn bao nhiêu lần” hay “SIFT lúc nào cũng tốt hơn bao nhiêu phần trăm”, vì kết quả còn phụ thuộc ảnh, cấu hình và phần cứng.

Thông điệp của slide là: **SIFT ưu tiên độ bền và độ chính xác của đặc trưng; ORB ưu tiên tốc độ và bộ nhớ**. Chọn cái nào phải dựa trên bài toán. Bây giờ mình đã có các điểm tương ứng đáng tin rồi, slide tiếp theo sẽ cho thấy chúng được dùng vào việc gì trong thực tế.

*[Bấm → sang slide 42.]*

**Lưu ý:** benchmark đã lưu, chạy 1 luồng CPU, 2 lần làm nóng và 9 lần đo; con số trên slide là trung vị của giao thức đó.

---

## Phần 5 · Ứng dụng (slide 42–43)

*[Nếu đổi người trình bày: “Dạ, em xin trình bày các ứng dụng.”]*

### Slide 42 · Ứng dụng của so khớp đặc trưng
*12 nhịp (3 ứng dụng × 4 bước) · khoảng 6–8 phút nếu giải thích đầy đủ*

Slide này gom ba ứng dụng để mình thấy một điều: SIFT hay ORB không phải đích cuối cùng. Hai thuật toán chỉ giúp mình tạo ra **các điểm tương ứng đáng tin**; từ những điểm đó, hệ thống mới suy ra phép biến đổi, vị trí vật thể hoặc chuyển động của camera.

*[Chọn tab Panorama. Dùng phím mũi tên để đi từng bước, hoặc bấm “Phát demo” cho tự chạy.]*

*[Panorama, bước 1/4.]*

Ứng dụng đầu tiên là **ghép ảnh toàn cảnh – panorama**. Trên màn hình có bốn ảnh chụp cùng một mặt báo. Mỗi ảnh chỉ thấy một phần, nhưng hai ảnh liền kề có một vùng bị chụp trùng nhau.

Cái mình cần tìm không phải là so toàn bộ hai ảnh pixel với pixel, vì vị trí các chi tiết đã thay đổi. Mình cần tìm những chi tiết xuất hiện ở cả hai ảnh, ví dụ đầu hươu cao cổ, chữ lớn hoặc các góc và hoa văn đặc trưng. Những vùng chồng lấp này chính là cầu nối để biết hai ảnh phải đặt tương đối với nhau như thế nào.

*[Bước 2/4.]*

Ở bước hai, **SIFT phát hiện và mô tả keypoint**, rồi ghép các descriptor giữa hai ảnh liền kề. Mỗi đường màu trên hình là một cặp điểm mà hệ thống cho rằng đang nhìn cùng một chi tiết.

Nhưng giống phần trước mình vừa học, match bằng descriptor vẫn có thể lẫn cặp sai. Vì vậy **RANSAC** được dùng để giữ những cặp cùng nhất quán với một mô hình hình học, thay vì tin toàn bộ các đường nối ngay từ đầu.

*[Bước 3/4.]*

Từ tập inlier đó, hệ thống ước lượng **homography** giữa hai ảnh. Homography cho phép mình biến đổi ảnh này về cùng hệ tọa độ với ảnh kia, rất phù hợp với bài toán mặt phẳng và panorama như ở đây.

Khi lần lượt đưa các ảnh về cùng một hệ, mình sẽ thấy những chi tiết chung bắt đầu chồng lên nhau. Ví dụ cùng một chữ hoặc cùng một vùng trên mặt báo sẽ tiến về gần đúng một vị trí. Tới đây về mặt hình học các ảnh đã được **căn chỉnh**.

*[Bước 4/4.]*

Nhưng căn đúng hình học chưa có nghĩa ảnh nhìn đã đẹp. Hai ảnh có thể chụp hơi khác sáng, và tại chỗ nối vẫn còn một đường biên rõ. Vì vậy bước cuối là **blending – phối trộn vùng chồng lấp**.

Trong vùng giao nhau, trọng số của ảnh này được giảm dần còn ảnh kia tăng dần, nhờ vậy đường nối mềm hơn. Vậy pipeline panorama ở đây là: **tìm điểm chung → lọc bằng RANSAC → ước lượng homography → warp về cùng hệ → blend vùng chồng lấp**.

*[Chuyển tab Định vị đối tượng, bước 1/4.]*

Ứng dụng thứ hai là **định vị một vật thể cụ thể trong cảnh**. Bên trái là ảnh mẫu của một hộp thực phẩm; bên phải là một cảnh có nhiều vật, và mặt hộp mình cần tìm đã bị thu nhỏ với nghiêng đi.

Nếu chỉ so template theo đúng kích thước và đúng hướng thì rất dễ thất bại. Nhưng đặc trưng cục bộ cho phép mình hỏi: “Những chữ, góc và hoạ tiết đặc trưng trên ảnh mẫu có xuất hiện ở đâu trong ảnh cảnh?”

*[Bước 2/4.]*

SIFT tìm keypoint trên ảnh mẫu và trên ảnh cảnh, rồi ghép chúng với **ratio 0,70**. Những cặp giữ lại cho biết nhiều chi tiết trên mặt hộp mẫu đang xuất hiện ở các vị trí tương ứng trong cảnh.

Điểm quan trọng là mình không cần toàn bộ vật thể phải có cùng kích thước hoặc đứng thẳng y chang ảnh mẫu. Chỉ cần đủ nhiều đặc trưng cục bộ còn nhìn thấy và ghép đúng thì mình đã có cơ sở để tìm hình học của mặt hộp.

*[Bước 3/4.]*

Sau đó RANSAC loại các cặp không nhất quán và ước lượng **homography** từ các cặp inlier. Một khi có H, mình lấy **bốn góc của ảnh mẫu** rồi chiếu qua H sang ảnh cảnh.

Kết quả bốn góc trở thành một **tứ giác** bao quanh vị trí mặt hộp trong cảnh. Như vậy homography không chỉ nói “vật này có xuất hiện”, mà còn cho mình biết nó đang nằm ở đâu và bị biến đổi phối cảnh như thế nào.

*[Bước 4/4.]*

Ở bước cuối, ảnh mẫu được biến đổi phối cảnh và chồng thử lên mặt hộp trong cảnh. Khi hai phần chồng khớp, mình trực quan thấy rằng các cặp keypoint và H đang mô tả đúng vị trí của vật thể.

Vậy với định vị đối tượng, chuỗi xử lý là: **ảnh mẫu → SIFT matching → RANSAC → homography → chiếu bốn góc → xác định vị trí và hình dạng mặt vật thể trong cảnh**.

*[Chuyển tab SLAM, bước 1/4.]*

Ứng dụng thứ ba là **SLAM – vừa định vị vừa lập bản đồ**. Ở đây mình có hai khung hình liên tiếp từ video của bộ dữ liệu TUM. Vì camera di chuyển nên cùng một vật, ví dụ bàn phím, xuất hiện ở vị trí khác giữa hai frame.

Khác panorama, cảnh này là môi trường 3D có nhiều độ sâu. Vật gần và vật xa không dịch chuyển giống nhau trên ảnh, nên mình không thể đơn giản dùng một homography cho toàn bộ cảnh.

*[Bước 2/4.]*

ORB được dùng để phát hiện và ghép keypoint giữa các frame liên tiếp. Sau đó hệ thống kiểm tra các cặp bằng **hình học epipolar**, tức ràng buộc liên quan tới **ma trận fundamental** ở slide 38.

Mình nhớ lại ý lúc nãy: với cảnh 3D, một điểm ở ảnh A sẽ xác định một đường epipolar bên ảnh B; điểm tương ứng hợp lý phải nằm gần đường đó. Nhờ vậy các match không phù hợp với hình học hai camera có thể bị loại, còn các cặp tốt được dùng để hỗ trợ ước lượng chuyển động của camera.

*[Bước 3/4.]*

Khi video tiếp tục chạy, các điểm đặc trưng được **theo dõi qua nhiều khung hình**. Từ những quan sát lặp lại đó, hệ thống vừa cập nhật vị trí của camera, vừa thêm dần các mốc của môi trường vào bản đồ.

Sơ đồ bên phải đang minh hoạ trực giác này: camera đi từ vị trí này sang vị trí khác, còn các mốc đã nhìn thấy được giữ lại để liên kết các frame với nhau. Càng có nhiều quan sát nhất quán, hệ thống càng có thêm ràng buộc để duy trì quỹ đạo và bản đồ.

*[Bước 4/4.]*

Một vấn đề của SLAM là sai số nhỏ có thể **tích luỹ theo thời gian**. Đi càng lâu, quỹ đạo ước lượng có thể lệch dần. Khi hệ thống nhận ra mình đã quay lại một vùng từng đi qua, nó tạo một **ràng buộc khép vòng – loop closure**.

Ràng buộc mới này cho biết hai đoạn quỹ đạo tưởng ở xa nhau thực ra đang nhìn lại cùng một nơi. Hệ thống có thể tối ưu lại quỹ đạo và bản đồ để giảm sai lệch tích luỹ.

Mình lưu ý **ORB chỉ là một thành phần của SLAM**, chủ yếu cung cấp các đặc trưng để theo dõi và ghép giữa các frame. Một hệ SLAM đầy đủ còn có nhiều khâu khác.

Ba ứng dụng nhìn khác nhau nhưng cùng chung một lõi: **phải có các điểm tương ứng đáng tin**. Panorama dùng chúng để căn chỉnh ảnh; định vị đối tượng dùng chúng để tìm homography và vị trí vật thể; SLAM dùng chúng để liên kết các frame và suy ra chuyển động. Từ đây nhóm quay lại bài toán mở đầu: nếu có cả một bộ dữ liệu rất lớn thì làm sao tận dụng các bước này mà vẫn đủ nhanh?

*[Bấm → sang slide 43.]*

**Lưu ý:** điểm và đường nối trong demo được tính từ ảnh thật. Riêng quỹ đạo, mốc bản đồ và phần khép vòng ở tab SLAM là **mô phỏng cơ chế**, không phải kết quả chạy một hệ SLAM hoàn chỉnh trên video.
