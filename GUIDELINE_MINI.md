# Mini guideline - nhóm: làm solo  |  người gán: Nguyễn Trọng Minh Đức (2A202602182)  |  ngày: 2026-09-16

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
| --- | --- | --- |
| Hông của người mặc quần áo dài | Luôn đặt chấm theo ước lượng giải phẫu (giữa thân dưới, ngang mào chậu), gắn `v=1` nếu không thấy đường viền khớp | Hông gần như không bao giờ lộ bề mặt khi mặc quần áo; nếu để `v=0` thì gần như cả bộ dữ liệu mất điểm hông. Số liệu thực tế: `left_hip` 10 `v=1` / `right_hip` 4 `v=1` |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Vẫn đặt chấm tại vị trí ống tai ước lượng, gắn `v=1` | Đây là khớp bị che nhiều nhất trong bài (`left_ear` 59% `v=1`, `right_ear` 45%), phần lớn do mũ bảo hiểm trong ảnh xe địa hình/xe máy. Vẫn suy ra được từ đường viền hàm và thái dương |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp nằm ngoài mép ảnh -> `v=0`, không đặt chấm | Đúng định nghĩa Outside: khớp không còn trong khung hình thì không có bằng chứng nào để đặt toạ độ |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu suy ra được hướng cẳng tay -> đặt chấm `v=1`; nếu mất hẳn bằng chứng -> `v=0` | Cổ tay là khớp cuối chuỗi tay nên sai số tích luỹ lớn; chỉ đoán khi còn hướng cẳng tay làm căn cứ |
| Hai người chồng lên nhau | Hoàn tất một người rồi mới sang người kế tiếp; khớp của người sau bị người trước che -> `v=1` | Tránh lỗi "nhầm người" (xương kéo sang cơ thể bên cạnh) - lỗi ưu tiên số 2 khi chấm với gold |
| Người nhỏ đến mức nào thì không gán nữa | Bộ 20 ảnh này không có ca nào quá nhỏ - gán tất cả người nhìn thấy | README của lab ghi rõ bộ ảnh đã được chọn sao cho mọi người trong ảnh đều đủ lớn để gán |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04`, người thứ `1` (áo Fox, bên phải), khớp `left_knee` / `right_knee` / `left_ankle` / `right_ankle`

- Mơ hồ ở chỗ nào: Người ngồi trên xe địa hình, toàn bộ phần chân không nhìn thấy được. Bản thân tài liệu lab mâu thuẫn: sơ đồ tóm tắt ở bước 5 của `lab-guide.html` ghi `v=0` là "ngoài khung **hoặc mất bằng chứng**", trong khi bảng chi tiết của `GUIDE.md` và code `check_pose_labels.py` lại theo luật chặt hơn - còn trong khung dù bị che 100% vẫn phải `v=1`.
- Bạn quyết thế nào: Chọn `v=0` cho cả bốn khớp chân.
- Vì sao: Xem trực tiếp trong CVAT ở độ phân giải gốc, vùng dưới hông không còn bằng chứng nào để suy ra vị trí gối và cổ chân. Áp dụng nhánh "mất bằng chứng -> không đoán" của sơ đồ bước 5, thay vì đoán bừa một toạ độ không có căn cứ thị giác.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu họ gắn `v=1` và đặt chấm đoán, model sẽ học rằng gối/cổ chân luôn nằm ở một vị trí cố định ngay dưới hông kể cả khi hoàn toàn không quan sát được - tức là học cách bịa ra khớp thay vì học cách nhận ra khớp. Ngược lại, chọn `v=0` khiến khớp đó bị loại khỏi phép tính OKS nên không được cũng không mất điểm.

### Ca 2 - ảnh `train_02` (và lặp lại ở `train_15`, `train_16`, `train_19`), người thứ `1`, khớp `left_shoulder`/`right_shoulder` và `left_hip`/`right_hip`

- Mơ hồ ở chỗ nào: `check_pose_labels.py` báo "nằm ngược chiều so với hai mắt - dấu hiệu đảo trái/phải" ở cả bốn ảnh này. Nhìn trên màn hình thì vai/hông đúng là nằm ngược so với thứ tự hai mắt.
- Bạn quyết thế nào: Giữ nguyên nhãn, không đổi trái/phải.
- Vì sao: Luật lớp quy định trái/phải tính theo **cơ thể người trong ảnh**, không theo bức ảnh. Khi người quay lưng hoặc xoay thân, trái/phải cơ thể đảo so với hình chiếu 2D nên heuristic của tool (so toạ độ x của vai/hông với thứ tự hai mắt) báo nhầm. Đã kiểm từng ảnh bằng cách tưởng tượng đứng vào vị trí người đó rồi giơ tay trái lên.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Đảo trái/phải là lỗi nguy hiểm nhất khi train, vì augmentation lật ảnh sẽ dạy model cái sai đó hai lần. Model sẽ học gán nhãn theo phía màn hình thay vì theo cơ thể, và sai vĩnh viễn ở mọi ảnh có người quay lưng.

### Ca 3 - ảnh `train_11`, người thứ `1`, khớp nhóm hông/gối/cổ chân

- Mơ hồ ở chỗ nào: Người ngồi sau bàn picnic, nửa dưới cơ thể bị mặt bàn, con mèo và hộp bánh che. Khác với Ca 1 ở chỗ các vật che này đều **nhìn thấy rõ trong khung ảnh**, nên vẫn còn căn cứ hình học để suy ra tư thế ngồi.
- Bạn quyết thế nào: Đổi một phần khớp từ `v=0` sang `v=1` và đặt chấm ước lượng (số khớp `v=0` giảm từ 8 xuống 5); giữ `v=0` cho các khớp xa nhất không còn căn cứ.
- Vì sao: Khi vật che nằm trong khung và biết được người đang ngồi, có thể suy ra hông nằm ngay dưới thân và ngang mặt bàn. Nhưng cổ chân thì không còn căn cứ nào vì bị che hoàn toàn bởi hộp bánh và mặt bàn.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gắn `v=0` cho tất cả, model mất toàn bộ tín hiệu về tư thế ngồi bị che một phần - vốn là ca rất phổ biến trong ảnh thật. Nếu gắn `v=1` cho tất cả kể cả cổ chân, model học đoán bừa vị trí chân dưới gầm bàn.

## 4. Sau khi so visibility report với bạn cùng nhóm

Làm solo - được phép bỏ phần kiểm chéo theo thông báo của lớp. Thay vào đó đối chiếu với **gold** (`outputs/eval_vs_gold.json`):

- Khớp lệch nhiều nhất: nhóm **gối / cổ chân / hông bị che hoàn toàn** - tôi `v=0`, gold `v=1` (7 khớp ở `train_08`, `train_10`, `train_12`, `train_13`). Ngoài ra tai/vai bị mũ che một phần: tôi `v=1`, gold `v=2` (40 khớp, không trừ điểm).
- Nguyên nhân là **guideline chưa rõ**: vị trí khớp đúng (OKS@0.75 = 1.000, 0 đảo trái/phải), chỉ khác cờ. Bản thân tài liệu lab có hai cách đọc `v=0` (xem Ca 1 mục 3).
- Luật bổ sung vào mục 2: khớp bị che **còn mốc giải phẫu liền kề** -> `v=1` + chấm; khớp bị che **mất hết mốc** -> `v=0`. Ngưỡng "nhìn thấy đủ rõ" cho `v=2`: thấy được đường viền khớp, không chỉ thấy vùng cơ thể quanh nó.
