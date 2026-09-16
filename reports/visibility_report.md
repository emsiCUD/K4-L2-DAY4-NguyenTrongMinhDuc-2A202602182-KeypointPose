# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 15.17 khớp có v > 0 mỗi người
- Tổng: v=2 330 | v=1 110 | v=0 53

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 24 | 5 | 0 | 17% |
| 1 | left_eye | 21 | 8 | 0 | 28% |
| 2 | right_eye | 22 | 7 | 0 | 24% |
| 3 | left_ear | 12 | 17 | 0 | 59% |
| 4 | right_ear | 16 | 13 | 0 | 45% |
| 5 | left_shoulder | 24 | 5 | 0 | 17% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 23 | 3 | 3 | 10% |
| 8 | right_elbow | 26 | 2 | 1 | 7% |
| 9 | left_wrist | 18 | 5 | 6 | 17% |
| 10 | right_wrist | 20 | 7 | 2 | 24% |
| 11 | left_hip | 17 | 10 | 2 | 34% |
| 12 | right_hip | 23 | 4 | 2 | 14% |
| 13 | left_knee | 17 | 3 | 9 | 10% |
| 14 | right_knee | 17 | 7 | 5 | 24% |
| 15 | left_ankle | 12 | 4 | 13 | 14% |
| 16 | right_ankle | 10 | 9 | 10 | 31% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
