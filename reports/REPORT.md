# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Lê Việt Anh   Nhóm: Cá nhân   Ngày: 16/9/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 326 / 138 / 29 |
| Thời gian trung bình mỗi ảnh | Chưa có dữ liệu thời gian gán |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 62% (18/29)
2. `right_ear`: 45% (13/29)
3. `left_wrist`: 38% (11/29)

Có. Tai trái/phải dễ bị tóc, mũ hoặc góc quay đầu che; cổ tay trái thường bị thân người hoặc vật cầm che. Đây là tình trạng bị che trong khung nên cần ước lượng vị trí và gán `v=1`, khác với trường hợp đã ra ngoài mép ảnh (`v=0`).

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9537 | 0.9529 |
| OKS@0.50 | 1.0000 | 1.0000 |
| OKS@0.75 | 1.0000 | 1.0000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (theo thay đổi nhãn đã lưu trong Git):

- `train_01.jpg`, người thứ 1: bổ sung `left_ankle` và `right_ankle` từ `v=0` sang điểm có tọa độ, `v=2`; đồng thời cập nhật bounding box.
- `train_01.jpg`, người thứ 2: bổ sung `left_ankle` và `right_ankle` từ `v=0` sang điểm có tọa độ, `v=2`; đồng thời cập nhật bounding box.
- `train_16.jpg`, người thứ 1: điều chỉnh tọa độ `left_eye`, `right_eye` và `right_ear` để khớp với vị trí đầu.

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Gold cũng không ghi nhận lỗi nhầm người hoặc xóa khớp bị che. OKS trung bình giảm 0.0008 sau rework, còn OKS@0.50 và OKS@0.75 giữ 1.0.

## 3. Kiểm chéo

Bạn cùng nhóm: Chưa có dữ liệu (bài làm cá nhân).

`reports/visibility_compare.md` đang đối chiếu cùng `dataset/labels/train`, nên tất cả độ lệch đều bằng 0; không có hai bảng nhãn độc lập để lập bảng kiểm chéo.

| Khớp | Bạn | Họ | Lệch | Nguyên nhân |
| --- | ---: | ---: | ---: | --- |
| Không xác định | — | — | — | Chưa có nhãn của bạn cùng nhóm |

Luật evidence cần áp dụng khi kiểm chéo:

- Nếu vị trí khớp còn nằm trong khung và suy được từ phần cơ thể liền kề, trang phục hoặc vật che thì đặt điểm ước lượng với `v=1`; chỉ dùng `v=0` khi khớp thực sự ra ngoài mép ảnh.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

1. `pose_mAP50-95` tăng 0.0055 (0.6853 lên 0.6908), không giảm. Fine-tune cải thiện rất nhỏ trên tập test; 20 ảnh là quá ít để suy ra chất lượng sản phẩm.

2. Sau fine-tune, `box_mAP50-95` cao hơn `pose_mAP50-95` 0.1133 (0.8041 so với 0.6908). Model tìm người dễ hơn tìm chính xác 17 khớp, vì pose còn phải suy vị trí các khớp bị che và phân biệt trái/phải.

3. Không có ảnh test/prediction đã xuất thành file trong `outputs` để kiểm chứng bằng mắt một lỗi cụ thể; vì vậy không gán tên loại lỗi chỉ từ chỉ số mAP.

4. Trong bảng đối chiếu nhãn–model ở notebook, thấp nhất là `train_13`, OKS 0.560 (một người). Gold cho thấy trường hợp yếu nhất của nhãn là `train_13.jpg`, người gold thứ 2 / nhãn thứ 2, OKS 0.8283; cần mở ảnh để so trực quan trước khi kết luận model hay nhãn đúng.

5. Có: `train_13` vừa chứa nhãn có OKS gold thấp nhất (0.8283 ở người thứ 2), vừa là ảnh có bất đồng nhãn–model thấp nhất (0.560). Đây là ca khó, cần kiểm lại trực quan thay vì quy lỗi riêng cho model hoặc nhãn.

## 5. Một rule evidence bạn đã dùng

Ở `train_01.jpg`, với cả hai người, `left_ankle` và `right_ankle` đã được bổ sung sau rework với tọa độ và `v=2` thay cho `v=0`. Thay đổi này cho thấy mắt cá chân vẫn nhìn thấy trong khung ảnh, không phải đã bị cắt khỏi mép ảnh. Vì vậy cần đặt chấm vào vị trí mắt cá nhìn thấy và dùng `v=2`; không dùng `v=0` chỉ vì phần chân ở gần đáy ảnh. Rule áp dụng là: chỉ chọn `v=0` khi bằng chứng thị giác cho thấy khớp ra ngoài khung; khớp còn trong ảnh nhưng bị che thì dùng `v=1` và vẫn đặt điểm ước lượng.
