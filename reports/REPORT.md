# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Trọng Minh Đức (2A202602182)   Nhóm: làm solo   Ngày: 2026-09-16

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 330 / 110 / 53 |
| Thời gian trung bình mỗi ảnh | 3 phút (tổng ~1 tiếng / 20 ảnh) |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` - 59% (12 `v=2` / 17 `v=1`)
2. `right_ear` - 45% (16 `v=2` / 13 `v=1`)
3. `left_hip` - 34% (17 `v=2` / 10 `v=1`)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Hai khớp tai đứng đầu bảng nhưng không phải vì khó xác định vị trí giải phẫu - chúng khó vì **hay bị che**: phần lớn ảnh trong bộ này là người đội mũ bảo hiểm xe địa hình và xe máy (`train_04`, `train_06`, `train_09`), nên vành tai bị vỏ mũ che hoàn toàn trong khi vị trí ống tai vẫn suy ra được từ đường viền hàm và thái dương. Hông thì ngược lại: `left_hip` cao không phải do vật che mà do **bản thân khớp không có bề mặt nhìn thấy được** trên người mặc quần áo, nên mọi chấm hông đều là ước lượng giải phẫu. Khớp tôi thấy tốn thời gian nhất thực ra là cổ tay khi nằm sau tay lái, vì phải quyết định giữa `v=1` và `v=0` dựa trên việc còn nhìn thấy hướng cẳng tay hay không - nhưng nó không lọt top vì số ca ít.

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

Bạn cùng nhóm: **không có - làm solo** (được phép bỏ phần kiểm chéo theo thông báo của lớp).

Thay cho bảng so sánh với bạn cùng nhóm, tôi dùng **gold làm đối chiếu guideline**: chênh lệch có hệ thống giữa bảng đếm của tôi và gold nằm ở cách xử lý khớp bị che hoàn toàn. Gold ghi `v=1` + chấm ước lượng cho 7 khớp mà tôi để `v=0` (`train_08` #1 gối/cổ chân trái, `train_10` #1 hai hông, `train_12` #1 gối/cổ chân trái, `train_13` #2 gối trái). Đây là **bất đồng về guideline**, không phải gán sai vị trí: OKS@0.50 và OKS@0.75 đều đạt 1.000, 0 lỗi đảo trái/phải, 0 lỗi nhầm người. Ngoài ra có 40 khớp "cờ khác gold nhưng vị trí đúng" (đa số tôi ghi `v=1`, gold ghi `v=2` ở tai và vai bị mũ bảo hiểm che một phần) - cũng là lệch guideline về ngưỡng "nhìn thấy đủ rõ".

Luật đã ghi vào `GUIDELINE_MINI.md` sau khi đối chiếu gold:

- Khớp bị che nhưng còn mốc giải phẫu liền kề (đùi, ống quần, hướng cẳng tay) trong khung -> `v=1` + chấm ước lượng. Khớp bị che mà **không còn mốc nào** để suy luận -> `v=0`, không đoán. Gold của lớp lỏng hơn ở vế sau (vẫn đặt chấm), nên nếu mục tiêu là tối đa OKS với gold thì phải đổi 7 khớp trên sang `v=1`; tôi giữ `v=0` để nhất quán với sơ đồ bước 5 và ghi rõ chênh lệch ở đây.

## 4. Model

<!-- Số liệu chép từ outputs/eval_model.json, sinh bởi notebook Colab (yolo26n-pose, 80 epoch, T4) - đánh giá trên 10 ảnh test. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

(Thêm: `box_mAP50` giảm từ 0.9785 xuống 0.9600, chênh -0.0185.)

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng **+0.0055** (0.6853 -> 0.6908) - gần như không đổi, và với tập test chỉ 10 ảnh thì mức chênh này nằm trong nhiễu. `pose_mAP50` đứng yên ở 0.845 cho thấy model gốc vốn đã "nhận ra pose" tốt; 20 ảnh của tôi chỉ dịch được một chút ở ngưỡng chặt (0.75-0.95), tức khớp đặt sát tâm hơn - phù hợp với việc bộ ảnh của tôi nhiều người đội mũ bảo hiểm ngồi trên xe, một dạng mà COCO có nhưng không nhiều. Cái nó làm hỏng là **box**: `box_mAP50` giảm 0.0185 và `box_mAP50-95` giảm 0.0078. Fine-tune trên 20 ảnh với 29 người làm đầu box bị kéo theo phân bố hẹp của tôi (người lớn, gần camera, bị cắt ở hông) nên định vị người kém đi một chút so với model đã học từ hàng chục nghìn ảnh COCO.

2. Trên model gốc, `box_mAP50-95` = 0.8119 còn `pose_mAP50-95` = 0.6853 - chênh **0.127**; sau fine-tune vẫn chênh 0.113. Model tìm *người* dễ hơn tìm *khớp* rõ rệt. Lý do: box chỉ cần một hình chữ nhật bao đúng thân người, sai vài chục pixel vẫn đạt IoU cao; pose cần **17 điểm cùng lúc** mỗi điểm nằm trong bán kính dung sai OKS riêng (mắt, tai có sigma rất nhỏ), và mọi khớp `v=1` bị che đều phải đoán đúng vị trí ước lượng của người gán nhãn. Một người có box hoàn hảo nhưng lệch hai cổ tay là đã rớt khỏi ngưỡng 0.75.

3. Ảnh `test_03` - hai người đi mô tô địa hình trên đường đất. Model bắt được cả hai nhưng người bên trái chỉ có độ tin cậy **0.32** (người bên phải 0.62), thấp nhất trong toàn bộ 10 ảnh test cùng với box 0.31 ở `test_02`. Người này đang nghiêng xe vào cua, thân trên gập sát tay lái, hai chân co và bị thân xe che gần hết. Bộ xương model vẽ ra ở phần thân trên (vai, khuỷu, cổ tay trên tay lái) còn bám được người, nhưng phần hông - gối - cổ chân không có mốc nào trên ảnh để bám nên các điểm rơi vào thân xe và bánh sau thay vì cơ thể. Theo bốn loại của slide 43, đây là **trượt hẳn** ở nhóm khớp chi dưới: không phải lệch vài chục pixel, mà là đặt lên một vật thể khác. Đây cũng chính là loại khớp mà trong tập train tôi gắn `v=0` (người ngồi xe ở `train_04`, `train_06`, `train_09`), nên model không có mẫu nào để học vị trí ước lượng cho tư thế này - fine-tune 20 ảnh không sửa được, và độ tin cậy thấp là dấu hiệu model "tự biết" nó đang đoán.

4. OKS thấp nhất giữa tôi và model là **`train_15` = 0.56** (người thứ nhất; người còn lại trong ảnh đạt 0.869), kế đến `train_06` = 0.578, `train_13` = 0.635, `train_14` = 0.664. **Tôi đúng, model sai** - căn cứ là gold: trên chính `train_15`, OKS nhãn-của-tôi vs gold là 0.874 và 0.925, lỗi duy nhất là lệch nhẹ 36 px ở `right_ear`, không có lỗi đảo trái/phải hay nhầm người. Nếu tôi sai nặng đến mức 0.56 thì gold đã phải bắt được. Điều đáng chú ý là ba ảnh model bất đồng nhất (`train_15`, `train_06`, `train_09` = 0.751) đều là ảnh người **quay lưng hoặc xoay thân** - đúng nhóm ảnh mà `check_pose_labels.py` từng nghi tôi đảo trái/phải (và gold đã xác nhận tôi đúng). Với người quay lưng, model phải suy ra trái/phải từ hướng thân thay vì từ mặt; OKS rơi từ ~0.9 xuống 0.56 đúng với kịch bản model gán ngược một cụm vai-khuỷu-cổ tay sang phía đối diện (mỗi khớp lệch cả bề ngang thân người, không phải vài chục pixel). Đây là lỗi **đảo trái/phải** của model, không phải của tôi - và là loại lỗi mà augmentation `fliplr=0.5` trong cell train dễ khuếch đại nếu nhãn có lỗi; nhãn của tôi không có nên model gốc mới là nguồn.

5. Có một ảnh trùng: **`train_13`**. Với gold, đây là ảnh tôi gán tệ nhất (người #2: 0.787, lỗi `xoa_khop_bi_che` ở `left_knee`); với model, `train_13` cũng nằm trong bốn ảnh thấp nhất (một người chỉ 0.635). Bức ảnh này khó cho cả hai vì cùng một lý do: **ba người chồng lên nhau trên vỉa hè, ảnh chụp lia máy nên hai người phía sau bị nhoè chuyển động, và cả ba đều bị cắt từ đùi trở xuống**. Người phải quyết định `v=0` hay `v=1` cho gối khi không còn mốc; model thì không có đủ pixel sắc nét để tách người này khỏi người kia. Khi cả người lẫn máy cùng thấp ở một ảnh, vấn đề nằm ở **bằng chứng trong ảnh ít**, không phải ở kỹ năng gán hay ở model. Ngoài `train_13`, hai danh sách **không trùng**: tôi tệ nhất ở `train_10`, `train_12`, `train_08` (nửa dưới bị che - lỗi guideline `v=0`/`v=1`), còn model tệ nhất ở `train_15`, `train_06`, `train_14` (người quay lưng - lỗi định hướng trái/phải), nơi tôi đạt 0.87-0.98 với gold. Hai bên yếu ở hai chỗ khác nhau: người yếu ở **che khuất**, máy yếu ở **hướng nhìn**. Thêm một tín hiệu cùng chiều: model đếm thừa người ở `train_03` (4 so với 2 của tôi) và `train_10` (2 so với 1) - box giả ở nền, khớp với việc `box_mAP` giảm sau fine-tune ở mục 4.

## 5. Một rule evidence bạn đã dùng

Ảnh `train_04`, người thứ 1 (người mặc áo Fox ngồi trên xe địa hình bên phải khung hình), bốn khớp `left_knee`, `right_knee`, `left_ankle`, `right_ankle`. Bằng chứng thị giác: thân người chỉ nhìn rõ từ mũ bảo hiểm xuống tới hông và bàn tay đeo găng đang đặt lên xe; từ hông trở xuống không còn quan sát được phần chân, và cũng không có mốc giải phẫu liền kề nào (đùi, ống quần, bàn chân) để căn vị trí gối hay cổ chân.

Tôi chọn `v=0` cho cả bốn khớp thay vì `v=1`. Lý do: sơ đồ ba trạng thái ở bước 5 của hướng dẫn định nghĩa `v=0` là "ngoài khung **hoặc mất bằng chứng** -> không đoán", và đây đúng là trường hợp mất bằng chứng - đặt một chấm ước lượng ở đây sẽ là đoán bừa chứ không phải suy luận từ phần cơ thể xung quanh như định nghĩa của `v=1` ("bị che, **còn suy ra được**").

Tôi ghi nhận đây là ca có tranh luận: `check_pose_labels.py` vẫn cảnh báo bốn khớp này vì bounding box của người đó không chạm mép ảnh, tức là theo cách đọc chặt hơn ở `GUIDE.md` thì khớp còn trong khung phải là `v=1`. Hai cách đọc này đến từ hai chỗ khác nhau trong cùng bộ tài liệu lab; tôi chọn nhánh "mất bằng chứng" và đã ghi đầy đủ lập luận vào `GUIDELINE_MINI.md` mục 3, Ca 1, để nếu đối chiếu gold cho kết quả khác thì có căn cứ rà lại.
