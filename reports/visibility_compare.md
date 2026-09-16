# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.0 khớp có v > 0 mỗi người
- Tổng: v=2 326 | v=1 138 | v=0 29

So sánh với `dataset\labels\train` (29 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 0 | nose | 21% | 21% | 0 |
| 1 | left_eye | 34% | 34% | 0 |
| 2 | right_eye | 31% | 31% | 0 |
| 3 | left_ear | 62% | 62% | 0 |
| 4 | right_ear | 45% | 45% | 0 |
| 5 | left_shoulder | 17% | 17% | 0 |
| 6 | right_shoulder | 7% | 7% | 0 |
| 7 | left_elbow | 24% | 24% | 0 |
| 8 | right_elbow | 17% | 17% | 0 |
| 9 | left_wrist | 38% | 38% | 0 |
| 10 | right_wrist | 31% | 31% | 0 |
| 11 | left_hip | 34% | 34% | 0 |
| 12 | right_hip | 28% | 28% | 0 |
| 13 | left_knee | 21% | 21% | 0 |
| 14 | right_knee | 21% | 21% | 0 |
| 15 | left_ankle | 17% | 17% | 0 |
| 16 | right_ankle | 28% | 28% | 0 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
