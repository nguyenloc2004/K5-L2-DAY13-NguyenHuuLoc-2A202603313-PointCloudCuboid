# Báo cáo Day 13 — Nguyễn Hữu Lộc, 2A202603313

## 1. Kết luận trước
Mã số của em là 2A202603313, task `D13-2A202603313`, gồm 10 frame `s01f11`, `s01f29`, `s02f16`, `s02f55`, `s03f24`, `s03f41`, `s04f08`, `s04f27`, `s05f15`, `s05f30`. Em làm trên Windows 11, trình duyệt Brave, CVAT 2.84.0.

Em vẽ được 91 cuboid, trong đó 74 xe con, 11 xe hai bánh, 3 xe tải, 2 xe buýt và 1 người đi bộ. Frame ít nhất là `s03f41`, chỉ 5 hộp, vì cảnh này vắng xe, trong 30 m chỉ có 4 vật, vật còn lại đã ở tận 55,6 m. Chạy `selfcheck` thì còn 18/91 hộp bị cờ. Phần em tin nhất là xe con ở gần: xe cùng chiều đều ra yaw khoảng 264–275°, xe ngược chiều khoảng 86–93°, đáy hầu hết nằm sát mặt đường. Phần em thấy yếu nhất là xe hai bánh và người đi bộ, có mấy hộp em quên xoay nên yaw vẫn là 0°, cụm xe đạp ở `s03f24` thì hộp còn chồng lên nhau. Hai xe buýt em cũng vẽ hơi rộng so với bảng 4.3.

Cách em dùng CVAT (task tạo tay trên giao diện, upload file zip từ `bundle`):
- Kéo chuột trái để xoay, chuột phải để dời, lăn chuột để zoom. Muốn nhìn từ trên xuống thì xoay tới khi nhìn thẳng xuống rồi thu nhỏ lại, nháy đúp chỗ trống để về góc mặc định.
- Chọn công cụ cuboid, chọn label, bấm Shape rồi nháy đúp lên cụm điểm. Em chỉnh vị trí, cạnh và yaw ở khung Top, còn Side và Front thì chỉ kéo đáy và chiều cao, không xoay ở đó.
- Bật Appearance → Cuboid orientation để thấy vạch đỏ chỉ đầu xe, sau đó so hộp trên ảnh camera trong CVAT và ảnh `preview` trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Ảnh: `t1-s01f11-3d`, `t1-s01f11-top`, `t1-s01f29-3d`, `t1-s01f29-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f11 | Phía trước (−y) ở cạnh dưới ảnh. +x (bên trái xe thu) chỉ sang phải ảnh, +y (phía sau) chỉ lên trên | Làn mình phẳng, thấp hơn LiDAR khoảng 1,7 m. Bên trái có dải phân cách trồng hoa, phía bên kia dải (x > 11 m) đáy các xe từ −1,40 đến −2,16 m, không đều | Xe buýt khoảng 38 m phía trước, xe con khoảng 36 m phía sau | Car, Bus, Pedestrian | Phía sau có 2 xe cùng chiều (~22 m, ~36 m). Bên trái, sau dải phân cách, có 5 xe ngược chiều (3 xe ở sau-trái, 2 xe ở trước-trái). Bên phải có một người đứng ở nhà chờ xe buýt, trước-phải ~14 m | Các mảng điểm dày dọc mép phải đường: nhà chờ, bảng quảng cáo, cây (đối chiếu cam07), em không gán |
| s01f29 | Giống `s01f11`: phía trước ở cạnh dưới, +x sang phải ảnh, +y lên trên | Phẳng, thấp hơn LiDAR khoảng 1,7 m (đáy xe từ −1,62 đến −1,80 m) | Xe con ~25 m ở sau-trái | Car, Truck | Phía sau-phải có 2 xe cùng chiều (~7 m, ~17 m). Bên trái là 2 xe con và 1 xe tải nhỏ đi ngược chiều, cách 13–25 m. Phía trước có 1 xe cùng chiều ~12 m | Một khối hình chữ "E" sáng ở sau-trái, xem cam05 thì là giá treo đèn tín hiệu, không phải xe |

Những thứ chỉ camera mới cho em biết: vật to ở 38 m phía trước `s01f11` là xe buýt (cam03 thấy rõ thân xe buýt), chứ trên point cloud ở khoảng cách đó chỉ còn 473 điểm, nhìn hình dạng không phân biệt được xe buýt hay xe tải. Người ở trước-phải đang đứng chờ ở nhà chờ xe buýt chứ không dắt xe. Ở `s01f29`, cam05 cho thấy trong dòng xe ngược chiều có một xe tải nhỏ thùng trắng, nên em gán Truck chứ không phải Car.

Còn LiDAR thì cho em khoảng cách bằng mét (xe 8 cách 6,36 m, xe buýt cách 38,0 m), cho biết mặt đường nằm thấp hơn cảm biến khoảng 1,7 m, và cho thấy cả những xe nằm gần ngang hông xe thu, chỗ camera không nhìn tới, ví dụ xe 7 ở `s01f11` (12,7 m) và xe 4 ở `s01f29` (13,1 m).

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f16 | Cạnh dưới | Có 3 xe hai bánh bên phải, cả phía trước lẫn phía sau |
| s02f55 | Cạnh dưới | Một xe hai bánh ở trước-trái ~14 m mà chỉ có 48 điểm, hộp thấp bất thường |
| s03f24 | Cạnh dưới | 5 xe hai bánh đứng sát nhau bên phải, khó tách từng chiếc |
| s03f41 | Cạnh dưới | Vắng xe, có một xe ở ~55,6 m phía sau chỉ còn 20 điểm |
| s04f08 | Cạnh dưới | Xe buýt và xe tải bên trái ~20 m sau hàng rào giữa đường, chỗ đó mặt đường thấp hơn làn mình (~−2,2 m) |
| s04f27 | Cạnh dưới | Đông xe (12 hộp), xe bên trái ~10 m bị xe gần hơn che mất một phần |
| s05f15 | Cạnh dưới | Có xe tải ~37 m phía sau, và một xe bên trái 8 m to bất thường |
| s05f30 | Cạnh dưới | Mấy xe phía sau 32–38 m có đáy cao hơn hẳn, chắc đường phía sau dốc lên |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f11, 8 | Car | Đúng, 270,6°, cùng chiều phía trước | Chạm, đáy −1,724 m | Rồi | Có, 4,52 × 1,72 × 1,44 m |
| (b) xe buýt hoặc xe tải | s01f29, 5 | Truck | Đúng, 91,5°, xe tải nhỏ đi ngược chiều ở sau-trái (cam05) | Chạm, đáy −2,006 m, lệch đường cục bộ −0,01 m | Rồi, cả Top/Side/Front và hộp chiếu trên cam05 | Dài 6,05 m, rộng 2,41 m hợp xe tải nhẹ, cao 2,34 m thấp hơn mức 2,40 m một chút |
| (c) người đi bộ hoặc xe hai bánh | s01f11, 1 | Pedestrian | Chưa chắc, người đứng yên ở nhà chờ nên khó biết hướng mặt, em để 0° mặc định | Gần chạm, đáy −1,455 m, cách đường cục bộ 0,29 m | Rồi | Có, 0,68 × 0,65 × 1,79 m |

Vòng hiệu chuẩn: Không.
Vật em bỏ qua: "không có".

## 4. Bốn hiện tượng chất lượng (Bước 4)
| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s04f08, xe buýt idx 8 / `t4-1-bong-che` / frame của em | Hai xe con idx 6, 7 nằm giữa LiDAR và xe buýt (cùng phía trái, gần hơn 4–8 m) nên chắn bớt tia | Điểm chỉ dày ở một đầu hộp, phần còn lại bên trong hộp gần như trống | Vẽ đủ chiều dài xe buýt theo camera (cam06 thấy cả thân xe), bật occluded |
| 2. Thưa ở xa | s03f41, xe idx 5 / `t4-2-thua-o-xa` / frame của em | Ở 55 m các tia LiDAR toả rộng ra, chỉ vài tia trúng xe | Chỉ còn lác đác vài chục điểm (`points_in` = 20), dồn ở một góc hộp, gần như không thấy hình xe | Em vẫn vẽ vì còn đoán được hướng xe. Nếu thấy dưới 15–20% thì phải bỏ và ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s01f11, xe buýt idx 3 cạnh xe con idx 2 / `t4-3-phan-xa-thap` / frame của em | Xe buýt nhiều cửa kính, tia laser xuyên qua hoặc phản xạ đi chỗ khác, cộng thêm xe ở xa (38 m) | Hộp xe buýt thưa và nhạt hơn hẳn xe con bên cạnh, dải giữa thân (chỗ cửa kính) gần như không có điểm | Không thu hộp theo chỗ có điểm, dựa vào camera và bảng 4.3 để vẽ đủ thân xe |
| 4. Điểm lạ | s02f35 / `t4-4-diem-la` / task chung `D13-HC485087` | Có thể do tia phản xạ nhiều lần hoặc nhiễu cảm biến | Một dải điểm mảnh, thẳng, nằm tách hẳn khỏi mặt đường và các vật xung quanh, phải xoay nghiêng mới thấy | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Số lấy từ `submission/D13-2A202603313-bangA.csv` và `submission/D13-2A202603313-tukiem.csv`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f11, 8, Car | −1,002 m | 1,444 m | −1,724 m | 6,36 m | 6,44 m | 270,6° | Chạm đường |
| s03f24, 7, Cyclist | −0,683 m | 1,152 m | −1,259 m | 15,76 m | 15,77 m | 174,1° | Lơ lửng, cao hơn −1,7 m khoảng 0,44 m |
| s04f08, 9, Truck | −0,522 m | 3,386 m | −2,215 m | 20,21 m | 20,21 m | 85,2° | Thấp hơn −1,7 m tới 0,52 m, nhưng so với mặt đường ngay chỗ đó thì vẫn chạm, do đường bên trái thấp hơn |
| s01f11, 9, Car | −0,822 m | 1,540 m | −1,592 m | 15,22 m | 15,24 m | 88,2° | So với −1,7 m thì ổn, nhưng `selfcheck` báo chìm 0,56 m, có thể do lẫn điểm của bồn hoa dải phân cách |

Hộp gần nhất là `s02f16` idx 3, dist_m 4,60 m, r 4,72 m. Xa nhất là `s03f41` idx 5, dist_m 55,55 m, r 55,56 m. Càng xa thì r với dist_m càng gần nhau, vì z chỉ khoảng 1 m.

Về kích thước, xe con `s01f11` idx 8 (4,52 × 1,72 × 1,44 m) nằm gọn trong khoảng của sedan. Xe hai bánh `s02f55` idx 3 thì dài 1,60 m, rộng 0,63 m vẫn hợp với xe đạp có người, nhưng cao chỉ 0,94 m, thấp hơn nhiều so với 1,6 m. Em nghĩ do chỉ có 48 điểm nên em chỉ bao được phần dưới của người và xe. Ngoài ra xe `s05f15` idx 6 rộng 2,53 m, cao 2,46 m, to quá so với xe con, có lẽ là xe van chở hàng và phải gán Truck mới đúng.

Về yaw, xe cùng chiều phía trước `s01f11` idx 8 là 270,6°, xe ngược chiều `s01f11` idx 9 là 88,2°, đều gần 270° và 90°.

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s05f15, 2, Car | 4,74 m | 5543 |
| s01f29, 1, Car | 12,34 m | 1025 |
| s04f08, 4, Car | 24,22 m | 259 |
| s05f15, 4, Car | 33,52 m | 91 |
| s03f41, 5, Car | 55,55 m | 20 |

Nhìn chung xa gấp đôi thì số điểm chỉ còn khoảng 1/4, ví dụ từ 12 m lên 24 m thì từ 1025 điểm xuống còn 259 điểm. So với slide thì khá khớp: 1025 điểm ở 12 m (slide 718 cho 10–20 m), 259 ở 24 m (slide 214), 91 ở 33,5 m (slide 72), 20 ở 55,6 m (slide 19 cho 60–80 m). Số của em nhỉnh hơn một chút, chắc vì mấy xe em chọn nằm ở nửa gần của mỗi khoảng.

Sau khi xem `preview` và `selfcheck`, em đã sửa: s03f24 idx 3: nâng đáy khoảng 0,4 m ở khung Side. Một số cờ em để nguyên. Cờ "điểm sát ngoài mép" ở `s02f16` idx 10 và `s03f24` idx 4, 5, 8 là do các xe hai bánh đứng sát nhau, nới hộp ra thì sẽ lấn sang xe bên cạnh. Các cờ "chìm" ở `s01f11` idx 5, 6, 7, 9 em giữ vì đó là xe bên kia dải phân cách, điểm đất cục bộ lẫn với bồn hoa.

Đổi kiểm: em không làm phần đổi kiểm với bạn nên không có kết quả đối chiếu.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model cần biết xe kia cách mình bao nhiêu mét, to cỡ nào, đang đi hướng nào để còn phanh hay đánh lái, nên nhãn phải đo bằng mét. Pixel thì thay đổi theo camera và khoảng cách, cùng một chiếc xe ở 6 m và ở 38 m trên ảnh to nhỏ khác hẳn nhau. Với vật thưa điểm, nếu không có quy tắc chung thì mỗi người đoán một kiểu, model sẽ học từ những nhãn mâu thuẫn nhau, hoặc học cách "thấy" vật ở chỗ không đủ dữ liệu.

(b) Có dùng AI:
- Công cụ: Claude Code trong VS Code.
- Em nhờ nó tổng hợp số từ bảng A và phiếu tự kiểm vào báo cáo (đếm hộp, tính r, chọn ví dụ), sau đó cho nó đọc các ảnh chụp màn hình trong báo cáo để điền phần mô tả frame và hiện tượng.
- Em tự đối chiếu lại các số với bảng A và xem lại từng mô tả với ảnh.

(c) Những chỗ em còn chưa chắc: 6 hộp người và xe hai bánh vẫn để yaw 0° (`s01f11` idx 1, `s02f16` idx 8–10, `s03f24` idx 4–5). Ở `s03f24` ba hộp 6, 7, 8 gần như trùng nhau, có thể em vẽ hai hộp cho cùng một xe. Hai xe buýt rộng hơn bảng 4.3, và xe buýt ở `s04f08` bị che nhưng em chưa bật occluded. Xe to ở `s05f15` idx 6 không biết nên là Car hay Truck. Còn xe ở 55,6 m thì đã vượt giới hạn 50 m của bài lab.
