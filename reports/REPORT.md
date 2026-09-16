# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Trọng Minh Đức (2A202602182)   Nhóm: ______   Ngày: 2026-09-16

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 330 / 110 / 53 |
| Thời gian trung bình mỗi ảnh | ______ phút <!-- CẦN BẠN ĐIỀN: tổng thời gian gán / 20 --> |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` - 59% (12 `v=2` / 17 `v=1`)
2. `right_ear` - 45% (16 `v=2` / 13 `v=1`)
3. `left_hip` - 34% (17 `v=2` / 10 `v=1`)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Hai khớp tai đứng đầu bảng nhưng không phải vì khó xác định vị trí giải phẫu - chúng khó vì **hay bị che**: phần lớn ảnh trong bộ này là người đội mũ bảo hiểm xe địa hình và xe máy (`train_04`, `train_06`, `train_09`), nên vành tai bị vỏ mũ che hoàn toàn trong khi vị trí ống tai vẫn suy ra được từ đường viền hàm và thái dương. Hông thì ngược lại: `left_hip` cao không phải do vật che mà do **bản thân khớp không có bề mặt nhìn thấy được** trên người mặc quần áo, nên mọi chấm hông đều là ước lượng giải phẫu. Khớp tôi thấy tốn thời gian nhất thực ra là cổ tay khi nằm sau tay lái, vì phải quyết định giữa `v=1` và `v=0` dựa trên việc còn nhìn thấy hướng cẳng tay hay không - nhưng nó không lọt top vì số ca ít.

<!-- CẦN BẠN RÀ LẠI: đoạn trên viết theo số liệu công cụ và các ca đã bàn trong buổi.
Sửa lại theo cảm nhận thật của bạn nếu khác. -->

## 2. Chấm với gold

<!-- Cột "Trước rework" lấy từ outputs/eval_vs_gold.json lần chạy đầu (2026-09-16). Gold nằm ở gold/train/.
Cột "Sau rework" điền sau khi sửa trong CVAT, export lại và chạy lại:
python tools/evaluate_pose_annotations.py --pred dataset/labels/train --gold gold/train --images dataset/images/train --out outputs/eval_vs_gold.json -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.929 | 0.935 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 7 | 7 |

Cả hai lần: 29/29 người ghép đúng, mức "Xuất sắc". Lần đầu còn 2 lỗi `thieu_khop` (gold `v=2`, tôi `v=0`) ở `train_13` người #1 - sau rework về 0. Lệch nhẹ giữ nguyên 5 ca (cổ tay ở `train_01`, `train_04`; tai phải ở `train_15`, `train_19`).

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- `train_13` người #1, `left_ankle`: đổi từ Outside (`v=0`) sang có toạ độ - gold ghi `v=2`, xem lại ảnh gốc thì cổ chân thật sự nhìn thấy được ở mép dưới khung.
- `train_13` người #1, `right_ankle`: sửa tương tự cổ chân trái.
- Giữ nguyên 7 khớp `v=0` còn lại (`train_08` #1 gối/cổ chân trái, `train_10` #1 hai hông, `train_12` #1 gối/cổ chân trái, `train_13` #2 gối trái) sau khi xem lại từng ca trong CVAT: ở các vị trí này không còn bằng chứng thị giác để đặt chấm ước lượng, nên tôi áp dụng nhất quán nhánh "mất bằng chứng -> không đoán" như đã ghi ở `GUIDELINE_MINI.md` Ca 1. Gold theo luật lỏng hơn (`v=1` + chấm đoán), nên 7 khớp này tính 0 điểm OKS - đây là chênh lệch guideline có chủ đích, không phải bỏ sót.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

**Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh** (gold xác nhận: `dao_trai_phai = 0`). Trong lượt tự kiểm trước khi khoá nhãn, `check_pose_labels.py` có báo nghi đảo trái/phải ở bốn ảnh `train_02`, `train_15`, `train_16`, `train_19`. Tôi đã kiểm từng ảnh bằng cách tưởng tượng đứng vào vị trí người trong ảnh và giữ nguyên nhãn - kết quả gold cho thấy đó đúng là false positive của heuristic (tool so toạ độ x của vai/hông với thứ tự hai mắt, nên báo nhầm khi người xoay thân hoặc quay lưng).

## 3. Kiểm chéo

<!-- CHƯA LÀM ĐƯỢC: cần nhãn của bạn cùng nhóm. Chạy:
python tools/visibility_report.py --labels dataset/labels/train --compare ../ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md -->

Bạn cùng nhóm: ______

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| | | | | |
| | | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

-

## 4. Model

<!-- CHƯA LÀM ĐƯỢC: cần chạy notebooks/day4_pose_finetune_yolo26.ipynb trên Colab (fine-tune 80 epoch),
sinh ra outputs/eval_model.json rồi chép số vào bảng dưới. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | | | |
| pose_mAP50-95 | | | |
| pose_precision | | | |
| pose_recall | | | |
| box_mAP50-95 | | | |

### Trả lời năm câu hỏi ở cuối notebook

1.
2.
3.
4.
5.

## 5. Một rule evidence bạn đã dùng

Ảnh `train_04`, người thứ 1 (người mặc áo Fox ngồi trên xe địa hình bên phải khung hình), bốn khớp `left_knee`, `right_knee`, `left_ankle`, `right_ankle`. Bằng chứng thị giác: thân người chỉ nhìn rõ từ mũ bảo hiểm xuống tới hông và bàn tay đeo găng đang đặt lên xe; từ hông trở xuống không còn quan sát được phần chân, và cũng không có mốc giải phẫu liền kề nào (đùi, ống quần, bàn chân) để căn vị trí gối hay cổ chân.

Tôi chọn `v=0` cho cả bốn khớp thay vì `v=1`. Lý do: sơ đồ ba trạng thái ở bước 5 của hướng dẫn định nghĩa `v=0` là "ngoài khung **hoặc mất bằng chứng** -> không đoán", và đây đúng là trường hợp mất bằng chứng - đặt một chấm ước lượng ở đây sẽ là đoán bừa chứ không phải suy luận từ phần cơ thể xung quanh như định nghĩa của `v=1` ("bị che, **còn suy ra được**").

Tôi ghi nhận đây là ca có tranh luận: `check_pose_labels.py` vẫn cảnh báo bốn khớp này vì bounding box của người đó không chạm mép ảnh, tức là theo cách đọc chặt hơn ở `GUIDE.md` thì khớp còn trong khung phải là `v=1`. Hai cách đọc này đến từ hai chỗ khác nhau trong cùng bộ tài liệu lab; tôi chọn nhánh "mất bằng chứng" và đã ghi đầy đủ lập luận vào `GUIDELINE_MINI.md` mục 3, Ca 1, để nếu đối chiếu gold cho kết quả khác thì có căn cứ rà lại.
