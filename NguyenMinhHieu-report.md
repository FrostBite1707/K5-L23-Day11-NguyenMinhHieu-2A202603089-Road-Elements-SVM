# Báo cáo cá nhân — Ngày 11
Họ tên: Nguyễn Minh Hiếu              Mã học viên: 2A202603089  
Phiên bản CVAT (Help → About): v2.82.0  
Guideline: Road v1.0    Lần nộp (`revision` trong manifest): 1  
Thời điểm export (ngày giờ trên CVAT): 2026-11-20 17:17 UTC   Số lần nộp lại sau Rework: 0  

## 1. Kiểm project
| Mục | Đúng như guideline Road v1.0? | Ghi chú |
| --- | --- | --- |
| 6 label, đúng kiểu | Đúng | `lane` (polyline), `road_curb` (polyline), `crosswalk` (polygon), `drivable` (polygon), `traffic_light` (rectangle), `traffic_sign` (rectangle) |
| Thuộc tính và giá trị | Đúng | Khớp JSON từ `PYTHONPATH=src python3 -m road_lab.taxonomy` (`configs/road_v1_labels.json`) |
| 4 tag ảnh | Đúng | `camera`, `timeofday`, `weather`, `source` (mỗi tag có 1 thuộc tính select tên `value`) |
Cách kết thúc polyline trên bản CVAT này: Bấm phím `N` sau khi click điểm cuối cùng ở xa.  
Cách đặt tag ảnh trên bản CVAT này: Chọn công cụ **Tag** ở thanh công cụ bên trái → chọn label tag (`camera`, `timeofday`, `weather`, `source`) → bấm **Tag**, sau đó mở **Details** trong bảng Objects để chọn giá trị thuộc tính `value`.  

## 2. Số lượng đã gán
| Task | Ảnh | lane | road_curb | crosswalk | drivable | traffic_light | traffic_sign |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| front | 12 | 33 | 11 | 4 | 22 | 28 | 32 |
| front-extra (đã làm 4 / 4) | 4 | 12 | 2 | 2 | 7 | 8 | 13 |
| fisheye | 8 | 10 | 7 | 8 | 3 | 6 | 6 |
| fisheye-extra (đã làm 4 / 4) | 4 | 3 | 3 | 4 | 1 | 1 | 1 |

## 3. Bảng ca khó
| Ảnh | Nhãn | Phân vân giữa | Đã chọn | Lý do |
| --- | --- | --- | --- | --- |
| A03 | `traffic_sign` @(216,168) | `group = other` hay `prohibitory` / `information` | `group = other`, `facing = back` | Biển hình vuông/chữ nhật trên đảo phân cách quay lưng hoàn toàn (`facing = back`), không thấy nội dung mặt trước để phân biệt nhóm chức năng nên chọn `other` theo mục 3 Guideline. |
| A04 | `drivable` | Có gán `alternative` cho nhánh đường rẽ trái qua vạch vàng đôi bị đứt đoạn hay không | Chỉ gán `direct` cho làn đang đi và `alternative` cho làn cùng chiều bên phải | Nhánh rẽ bên trái phải cắt qua làn ngược chiều (bên trái vạch vàng đôi `L1`), trong khi `alternative` theo Guideline chỉ dành cho làn cùng chiều bên cạnh. |
| A05 | `lane` (`L1`, `R1`) | Vẽ `L1`/`R1` giả định dọc hai hàng xe đỗ hay không vẽ `lane` | Không vẽ `L1`/`R1`; chỉ gán `drivable` (`direct`) và `road_curb` bên phải | Mặt đường phố hoàn toàn không có vạch sơn kẻ làn, hai bên là dải xe đỗ sát lề. Theo mục 6 Hướng dẫn CVAT và CHECKPOINTS, không vẽ vạch mà ảnh không có (ca miễn cảnh báo `QC1-02` của `lint`). |
| A06 | `drivable` & `lane` | Xử lý hàng cột cầu đường sắt trên cao chia đôi lòng đường và nửa dưới ảnh không có vạch sơn dọc | `drivable` (`direct`) chỉ bao phần đường đi thẳng bên phải hàng cột cầu; `L1`/`R1` bắt đầu từ sau `crosswalk` | Theo Guideline mục 3, `drivable` không gồm vùng phía sau cọc/trụ cầu; phần lòng đường giữa hai hàng cột có xe đi ngược chiều tới. Các vạch sơn dọc chỉ bắt đầu xuất hiện từ sau vạch qua đường. |
| A07 | `traffic_sign` @(530,186), @(582,216), @(44,202) | `group = warning` hay `other` khi biển quay mặt sau (`facing = back`) | `group = warning`, `facing = back` | Tuy quay mặt sau nhưng biển có hình thoi (diamond) đặc trưng của biển cảnh báo nguy hiểm tại Mỹ nên xác định được chức năng thuộc nhóm `warning`. |
| B11 | `lane` | Có gán các vạch chéo/vạch chia ô đỗ xe màu trắng và vàng hay không | Chỉ gán 1 polyline `lane` (`L1`) cho vạch dọc giới hạn phía trước ô đỗ và 1 `road_curb`; bỏ qua vạch chia ô đỗ | Theo Guideline mục 4 ("Vạch ô đỗ không gán lane") và trọng tâm slot B11, chỉ gán vạch dọc phân ranh giới, tránh lỗi nặng `QC1-03` (vẽ thừa lane). |

## 4. Phiếu QC 1 nhận được
Bạn kiểm lô của tôi: *(điền tên bạn ghép cặp sau khi đổi lô)*  Quyết định nhận được: *(Release / Hold / Rework)*  
| Mã lỗi | Số lỗi | Đồng ý? | Đã sửa? |
| --- | ---: | --- | --- |
| QC1-02 (A05) | 0 (miễn) | Đồng ý (ảnh A05 thực tế không có vạch sơn L1/R1) | Giữ nguyên theo quy tắc không vẽ vạch không tồn tại |

## 5. Kiểm file export
| File | Mở lại được? | Số shape ảnh 1 (job / file) | Số shape ảnh 2 (job / file) | Tag camera có trong XML? |
| --- | --- | --- | --- | --- |
| `NguyenMinhHieu-front.zip` | Có (`road_check lint` OK) | A01: 9 / 9 | A02: 6 / 6 | Có (`<tag label="camera"><attribute name="value">front</attribute></tag>`) |
| `NguyenMinhHieu-fisheye.zip` | Có (`road_check lint` OK) | B01: 4 / 4 | B02: 8 / 8 | Có (`<tag label="camera"><attribute name="value">fisheye_front</attribute></tag>`) |

## 6. Ảnh chụp màn hình
1. Tab Labels của project.
2. Một ảnh camera trước đã gán đủ (làn, vùng lái, đèn/biển).
3. Một ảnh fisheye đã gán, phóng to đoạn polyline cong.
4. Một đoạn XML có `<polyline label="lane">`:

```xml
<polyline label="lane" source="manual" occluded="0" z_order="0" points="120.00,700.00;195.00,650.00;266.00,600.00;313.00,560.00;336.00,540.00;355.00,520.00;370.00,503.00">
  <attribute name="position">L1</attribute>
  <attribute name="style">dashed</attribute>
  <attribute name="color">white</attribute>
  <attribute name="kind">single</attribute>
</polyline>
```

## 7. Tự đánh giá
Lỗi bạn mắc nhiều nhất hôm nay (theo mã QC1-xx), và cách tránh ở Ngày 20:  
- Các lỗi dễ mắc nhất khi thao tác nhanh trên CVAT là **QC1-06 / QC1-01** (quên đổi thuộc tính mặc định mà CVAT tự điền như `L1 / solid / white / single` hoặc `weather = clear`) và **QC1-11** (đặt quá ít điểm ở vòng ngoài ảnh fisheye hoặc quên tick `edge_zone` cho đèn/biển nằm ngoài 80% bán kính). Cách tránh ở Ngày 20: luôn mở bảng **Details** ngay sau khi bấm `N` kết thúc polyline để chỉnh thuộc tính, và chạy `road_check lint` để rà soát tự động toàn bộ cảnh báo chiều vẽ, số điểm cung cong và `edge_zone`.

Nhãn của bạn đi vào model ở đâu? Chọn một lỗi đã sửa và nói nó làm model học sai thế nào:  
- Nhãn `lane` (polyline + `position`) và `drivable` đi trực tiếp vào làm ground-truth huấn luyện đầu ra cho mô hình phát hiện làn đường (như kiến trúc row-anchor / instance lane detection) và phân đoạn vùng lái cho hệ thống giữ làn (LKA). Nếu gán sai `position` (mã **QC1-03**, ví dụ nhầm `L2` thành `L1` hoặc gán nhầm vạch ô đỗ thành `lane`) hay vẽ `drivable` tràn lên vỉa hè/dải đỗ xe (**QC1-07**), hàm loss sẽ phạt các dự đoán đúng biên làn thật và ép model học lệch tâm làn xe đang đi, dẫn tới bộ điều khiển LKA đánh lái lệch sang làn bên cạnh hoặc lao vào dải đỗ xe.

## 8. (Tùy chọn, buffer) Từ polyline đến đích row-anchor
*(Tính trên ảnh `A01`, vạch `L1` `lane#1@(120,700)` và `R1` `lane#2@(650,675)` bằng `PYTHONPATH=src python3 -m road_lab.rowanchor NguyenMinhHieu-front.zip --image A01`)*

| y | 720 | 680 | 640 | 600 | 560 |
| --- | ---: | ---: | ---: | ---: | ---: |
| x của chúng tôi (`L1`) | không có làn | 150.0 | 209.2 | 266.0 | 313.0 |
| x của chúng tôi (`R1`) | không có làn | không có làn | 594.0 | 530.8 | 469.1 |
| x của bạn kia | *(điền sau khi so với bạn)* | | | | |

Trả lời 3 câu:

1. **Năm số x đó chính là đích mà một model row-anchor học cho vạch này. Nếu bạn dừng polyline sớm 40 px, hàng nào đổi thành "không có làn"?**
   - Nếu dừng polyline sớm 40 px ở đầu gần xe (ví dụ với `L1` dừng ở `y = 660` thay vì `y = 700`, hoặc với `R1` dừng ở `y = 635` thay vì `y = 675`), thì hàng neo gần xe nhất đang có nhãn (`y = 680` với `L1`, hoặc `y = 640` với `R1`) sẽ nằm ngoài phạm vi đoạn `[y_min, y_max]` của polyline và bị đổi thành **"không có làn"**.
2. **So với bạn được kiểm (cùng ảnh): ở hàng 720 và hàng 560, hai bạn lệch nhau bao nhiêu pixel? Lệch ở hàng nào ảnh hưởng LKA nhiều hơn (slide 25)?**
   - Lệch ở hàng gần xe (**`y = 720` hoặc `y = 680`**) ảnh hưởng tới hệ thống giữ làn (**LKA**) **nhiều hơn** so với ở hàng xa (`y = 560`). Lý do: ở đáy ảnh (gần đầu xe), độ phân giải không gian cao (1 mét ngoài đời ứng với rất nhiều pixel) và điểm này quyết định trực tiếp sai số độ lệch ngang tức thời (`lateral offset error`) của xe so với tâm làn; hơn nữa một sai số góc hoặc điểm đặt ở gần xe sẽ làm thay đổi ngay lập tức lệnh đánh lái của bộ điều khiển LKA.
3. **Nếu polyline vẽ từ xa về gần (ngược quy tắc 2), cách tính trên có đổi không? Script nào sẽ gặp khó?**
   - Với riêng phép nội suy tuyến tính theo tọa độ `y` trên từng đoạn thẳng của `rowanchor.py` (`x = x0 + (y - y0) * (x1 - x0) / (y1 - y0)`), kết quả nội suy `x` tại một hàng `y` không đổi khi đảo thứ tự danh sách điểm. Tuy nhiên, các script và model phụ thuộc vào **hướng tham số hóa theo chiều dài cung $s$ từ gần ra xa** — cụ thể là **`road_check.py`** (kiểm tra chiều vẽ `s1 < s0` và khoảng cách điểm đầu `pts[0]` với điểm gần xe `g.points[0]` ở mã `QC1-04`) cũng như các thuật toán nối vạch / dự đoán điểm khởi đầu làn ở đáy ảnh — sẽ báo lỗi hoặc ghép sai đầu vạch.
