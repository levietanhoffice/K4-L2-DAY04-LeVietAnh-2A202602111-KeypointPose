# Mini guideline - nhóm: Cá Nhân  |  người gán: Lê Việt Anh  |  ngày: 16/9/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
|---|---|---|
| Hông của người mặc quần áo dài | Ước lượng vị trí giải phẫu tại mấu chuyển lớn xương đùi (ngang đáy háng/xương chậu, gióng theo trục đùi - thân). Gán `v = 1` (Occluded). Chỉ để `v = 2` khi mặc đồ bơi/đồ bó sát thấy rõ khớp hông chuyển động. [Ảnh mẫu khớp hông](dataset/images/train/train_08.jpg.png) | Khớp hông luôn bị quần áo che khuất, không có bề mặt nhìn thấy trực tiếp. Giữ cờ `v = 1` và chấm ước lượng giúp mô hình học liên tục chuỗi động học vai - hông - gối, tránh bị đứt gãy skeleton. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | - Thấy một phần vành/dái tai: chấm gốc tai, gán `v = 2` (nếu thấy rõ) hoặc `v = 1` (nếu bị che phần lớn).<br>- Tóc/mũ che kín hoàn toàn nhưng đầu trong ảnh: gióng ngang từ khóe mắt ra sau mép xương hàm dưới, chấm ước lượng và gán `v = 1`.<br>- Chỉ gán `v = 0` khi đầu bị cắt ra ngoài mép ảnh. [Ảnh mẫu khớp hông](dataset/images/train/train_02.jpg.png) | Tai là mốc giải phẫu then chốt xác định góc quay khuôn mặt (head yaw/pitch). Nếu xóa hoặc để `v = 0` khi đầu còn trong ảnh, mô hình sẽ mất tam giác mặt (mắt - mũi - tai) và dễ nhầm mặt trước với mặt sau. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp nằm ngoài đường biên bức ảnh (đùi, gối, cổ chân) dứt khoát gán cờ `v = 0` (Outside), không đặt chấm trong khung hình. Khớp nào còn bên trong mép ảnh dù sát viền vẫn gán `v = 2` hoặc `v = 1`. [Ảnh mẫu khớp hông](dataset/images/train/train_10.jpg.png) | Chuẩn COCO quy định `v = 0` cho phần cơ thể ra ngoài khung hình. Nếu cố đoán chấm khớp ngoài ảnh vào viền ảnh với `v = 1`, mô hình sẽ bị phạt OKS và học sai phân bố biên (edge artifacts/hallucination). |
| Cổ tay nằm sau tay lái / sau thân mình | - Bị ghi-đông, tay lái, găng tay che: lần theo trục cẳng tay và bàn tay nắm, chấm đúng vị trí khớp nối cổ tay, gán `v = 1`.<br>- Giấu hoàn toàn sau lưng/thân: lần theo trục khuỷu tay ước lượng vị trí áp sau lưng, gán `v = 1`. Tuyệt đối không để `v = 0`. [Ảnh mẫu khớp hông](dataset/images/train/train_01.jpg.png) | Cổ tay nằm ở đầu mút chi trên. Việc duy trì cờ `v = 1` kèm điểm ước lượng giữ trọn vẹn cử động của cánh tay, ngăn model bị mất khớp (missing keypoint) khi có vật thể che chắn phía trước. |
| Hai người chồng lên nhau | Gán dứt điểm từng người một (đủ 17 điểm người 1 mới sang người 2). Khớp người sau bị thân người trước che khuất: nội suy giải phẫu theo trục cơ thể người sau, chấm ước lượng và gán `v = 1`. Cấm "mượn" khớp người trước gán cho người sau. [Ảnh mẫu khớp hông](dataset/images/train/train_03.jpg.png) | Tránh lỗi tráo người / nhầm người (`nham_nguoi`) và lỗi nối xương chéo giữa 2 thân thể. Phân định ranh giới độc lập giúp mô hình nhận diện đúng từng instance trong cảnh đông đúc (crowded scene). |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả mọi người có thể phân biệt được tư thế và hướng cơ thể (bbox từ ~30×30 px hoặc chiều cao từ ~50 px trở lên, như người hậu cảnh ở `train_13.jpg`). Chỉ bỏ qua người mờ nhòe cực nhỏ ở vô cực (< 20 px) hoặc tranh ảnh tĩnh in trên tường/biển quảng cáo. [Ảnh mẫu khớp hông](dataset/images/train/train_13.jpg.png) | Vì mờ quá thì không thể phân biệt được |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `______`, người thứ `___`, khớp `______`

- Mơ hồ ở chỗ nào: Người mẫu đứng quay mặt nghiêng/chếch về phía sau, lọn tóc dài che phủ hoàn toàn vành tai trái khiến không có bề mặt thị giác nhìn thấy trực tiếp. Rất dễ phân vân giữa việc xóa/bỏ qua khớp hoặc đánh v = 0 (Outside - giống như nhãn gốc của COCO) và việc định vị giải phẫu ước lượng với v = 1 (Occluded).
- Bạn quyết thế nào: Gióng vị trí giải phẫu của lỗ tai ngoài từ khóe mắt ngoài và góc xương hàm dưới ra phía sau, đặt chấm ước lượng và gán v = 1 (Occluded).
- Vì sao: Toàn bộ phần đầu và hộp sọ của người mẫu nằm trọn vẹn bên trong khung ảnh (không hề bị mép ảnh cắt). Theo guideline lớp học, bất kỳ khớp nào còn nằm trong khung hình nhưng bị che khuất thì bắt buộc phải giữ điểm và gán v = 1, không được gán v = 0 hay xóa điểm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu người khác gán v = 0 hoặc xóa khớp, model sẽ học rằng khi đầu quay nghiêng thì tai biến mất hoặc văng ra ngoài khung hình. Khi triển khai thực tế trên các góc quay nghiêng (profile/back view), model sẽ bị mất hoàn toàn keypoint tai (missing keypoint) hoặc dự đoán trượt hẳn vị trí hộp sọ.

### Ca 2 - ảnh `______`, người thứ `___`, khớp `______`

- Mơ hồ ở chỗ nào: Người mẫu mặc quần dài che kín toàn bộ phần hông và xương chậu. Khớp hông (mấu chuyển lớn xương đùi) chìm hoàn toàn dưới lớp vải, khó phân định giữa việc coi nếp gấp đáy quần là nhìn thấy (v = 2) hay coi quần áo là vật che phủ (v = 1).
- Bạn quyết thế nào: Xác định vị trí khớp hông giải phẫu tại giao điểm giữa trục sườn thân mình và trục đùi gióng thẳng xuống xương bánh chè (knee), đặt chấm ước lượng và gán v = 1 (Occluded).
- Vì sao: Về mặt giải phẫu và thị giác, mắt thường chỉ nhìn thấy vải quần chứ không nhìn thấy bề mặt khớp hông. Gán v = 1 thể hiện đúng trạng thái "bị che nhưng xác định được vị trí" (occluded but locatable).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gán v = 2 (coi như nhìn thấy rõ), model sẽ nhầm lẫn đặc trưng thị giác trực tiếp với điểm nội suy dưới vật cản. Khi gặp người mặc trang phục rộng, quần thụng hoặc váy xòe, model sẽ có xu hướng dự đoán khớp hông dạt ra rìa nếp vải thay vì nằm đúng trục xương bên trong cơ thể.

### Ca 3 - ảnh `______`, người thứ `___`, khớp `______`

- Mơ hồ ở chỗ nào: Ảnh chụp chân dung bán thân (chiều cao ảnh chỉ 375 px), mép dưới của bức ảnh cắt ngang qua đùi. Rất dễ phân vân giữa việc cố tình chấm điểm đầu gối ép sát vào viền đáy ảnh với cờ v = 1 (vì cảm giác khớp chỉ nằm ngay dưới mép một chút) hay loại bỏ khớp bằng cờ v = 0.
- Bạn quyết thế nào: Gán cờ v = 0 (Outside), không đặt tọa độ chấm trong khung hình cho cả hai khớp đầu gối và cổ chân.
- Vì sao: Vị trí thực tế của xương bánh chè đã nằm hoàn toàn ra ngoài đường biên bức ảnh. Theo quy tắc cốt lõi: "Khớp không còn trong khung hình → Chọn Outside (v = 0)".
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu cố tình chấm dồn điểm về viền đáy ảnh với v = 1, model sẽ học sai phân bố biên (edge hallucination/boundary artifact). Khi gặp người bị cắt cụt thân, model sẽ luôn ép các khớp chi dưới bám dính vào viền ảnh thay vì nhận biết chúng đã ra khỏi trường nhìn.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch %v=1 nhiều nhất: left_hip (bạn 34.5% [10/29 khớp] / họ 10.3% [3/29 khớp] → lệch 24.2%). (Hoặc left_ear: bạn 62.1% [18/29] / họ 31.0% [9/29] → lệch 31.1%).
- Nguyên nhân là guideline chưa rõ hay một trong hai bên gán sai: Nguyên nhân chủ yếu là do guideline ban đầu chưa làm rõ quy ước về trang phục và tóc: Phía bạn tuân thủ chặt chẽ định nghĩa giải phẫu: hông nằm dưới lớp quần áo hoặc tai bị tóc phủ qua thì mắt thường không thấy trực tiếp → chọn v = 1. Phía bạn cùng nhóm lại coi quần áo ôm và nếp gấp trang phục thông thường là "thấy người" nên gán nhầm thành v = 2 (Visible).
- Luật mới bổ sung vào mục 2 sau khi thống nhất: "Bất kỳ khớp nào bị che phủ bởi lớp vật chất ngoại cảnh (quần áo dày, nếp vải, tóc rủ, mũ bảo hiểm, tay lái) khiến không nhìn thấy trực tiếp bề mặt da/khớp giải phẫu, nhưng cơ thể vẫn nằm trong khung hình và ước lượng được theo trục xương liền kề → BẮT BUỘC gán v = 1 (Occluded) kèm chấm ước lượng. Chỉ gán v = 2 (Visible) khi nhìn thấy trực tiếp bề mặt da hoặc khớp lộ rõ chuyển động qua đồ bơi / trang phục bó sát."