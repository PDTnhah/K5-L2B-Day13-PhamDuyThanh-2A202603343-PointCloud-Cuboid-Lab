# Báo cáo Day 13 — Phạm Duy Thanh, 2A202603343

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước

- MSHV: 2A202603343
- Task `D13-2A202603343`
- 10 frame: pointcloud/s01f23.pcd, pointcloud/s01f40.pcd, pointcloud/s02f18.pcd, pointcloud/s02f51.pcd, pointcloud/s03f11.pcd, pointcloud/s03f45.pcd, pointcloud/s04f10.pcd, pointcloud/s04f42.pcd, pointcloud/s05f08.pcd, pointcloud/s05f29.pcd
- Tổng: 62 cuboid
- Frame pointcloud/s04f42.pcd ít frame nhất do các xe ở xa ít point nên không gán khung kĩ được
- Phần yếu nhất: xác định chiều dài rộng cho các vật ở xa, ít point

Cách dùng CVAT:
- **Góc nhìn:** kéo chuột trái để xoay, chuột phải để dời, lăn chuột để phóng to/thu nhỏ; xoay tới khi nhìn thẳng xuống để có góc nhìn từ trên; nháy đúp chỗ trống để về góc mặc định.
- **Vẽ cuboid:** nút cuboid → chọn label → **Shape** → nháy đúp lên cụm điểm để đặt hộp; chỉnh tâm, dài, rộng, yaw ở **Top**; chỉnh đáy, chiều cao ở **Side/Front** (không xoay ở hai khung này); `Ctrl+S` để lưu.
- **Hướng và camera:** bật **Appearance → Cuboid orientation**, căn vạch đỏ ngắn (`+X`) về đầu xe theo ảnh camera; xem cuboid trên 7 ảnh camera bằng `python scripts/lab.py preview` (ảnh trong `work/xem/`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f23 (`t1-s01f23-3d.png`, `t1-s01f23-top.png`) | Phía trước ở cạnh phải; +x chỉ xuống dưới, +y chỉ sang phải | phẳng | 30 | car | car | |
| s01f40 (`t1-s01f40-3d.png`, `t1-s01f40-top.png`) | Phía trước ở cạnh phải; +x chỉ xuống dưới, +y chỉ sang phải | phẳng | 30 | car | car | |

3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên:

**Chỉ camera cho biết:**
- Màu sắc
- Trạng thái đèn hoặc biển báo
- Chi tiết để phân loại

**Chỉ LiDAR cho biết:**
- Khoảng cách thật bằng mét
- Vật nằm ngoài tầm camera
- Độ cao và độ dốc mặt đường

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f18 | Phía trước ở cạnh phải | |
| s02f51 | Phía trước ở cạnh phải | |
| s03f11 | Phía trước ở cạnh phải | |
| s03f45 | Phía trước ở cạnh phải | |
| s04f10 | Phía trước ở cạnh phải | |
| s04f42 | Phía trước ở cạnh phải | |
| s05f08 | Phía trước ở cạnh phải | |
| s05f29 | Phía trước ở cạnh phải | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f23 | car | Đúng | Có | Có |
| (b) xe buýt hoặc xe tải | s01f23 | bus | Đúng | Có | Có |
| (c) người đi bộ hoặc xe hai bánh | s01f23 | cyclist | Đúng | Có | Có |

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s01f23, `t4-1-bong-che.png`, frame của tôi | xe buýt cao, chắn tia LiDAR nên tia không tới được phía sau | Sau xe buýt có một vùng hình quạt gần như không có điểm, trong khi hai bên vẫn dày điểm | Vùng trống là chưa quan sát được, không phải đường trống. Vật bị che một phần thì bật **occluded** và vẽ cả phần suy luận được |
| 2. Thưa ở xa | s02f18, `t4-2-thua-o-xa.png`, frame của tôi | càng xa thì các tia LiDAR càng cách xa nhau, ít tia trúng xe | Ít điểm hơn, thành vài vạch ngang rời rạc | Vẫn nhận ra hình xe nên vẽ |
| 3. Phản xạ thấp / ít điểm bất thường | s03f11, `t4-3-phan-xa-thap.png`, frame của tôi | xe tối màu hoặc kính lớn nên phản xạ yếu | Ít điểm hơn một xe cùng tầm | vẫn vẽ đủ thân xe |
| 4. Điểm lạ | s05f08, `t4-4-diem-la.png`, frame của tôi | tia bị phản xạ nhiều lần hoặc cảm biến đo sai khoảng cách | Vài điểm lẻ loi nằm sâu dưới mặt đường, xoay nghiêng khung 3D mới thấy | Không gán thành vật, không dùng các điểm này làm mốc mặt đường |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f23, 1, Car | −1,016 | 1,502 | −1,767 | 4,16 | 4,29 | 273,1 | Chạm đường |
| s05f29, 4, Car | −0,680 | 1,726 | −1,543 | 27,36 | 27,36 | 266,6 | Chạm đường (cao hơn −1,7 m chưa tới 0,3 m) |
| s02f18, 8, Car | −1,512 | 1,571 | −2,298 | 35,16 | 35,19 | 87,5 | Thấp hơn −1,7 m khoảng 0,6 m, nhưng mặt đường ở xa cũng thấp hơn (phiếu tự kiểm: đáy chỉ hở 0,14 m) nên vẫn chạm đường |

- **(a)** Gần nhất: s01f40 idx 1 (Car), `dist_m` 3,20 m, r 3,36 m. Xa nhất: s02f18 idx 8 (Car), `dist_m` 35,16 m, r 35,19 m. r lớn hơn `dist_m` vì tính cả z; vật càng gần thì chênh càng rõ.
- **(b)** Người đi bộ s02f18 idx 6: 0,67 × 0,70 × 1,40 m. Dài, rộng trong khoảng; cao 1,40 m thấp hơn khoảng người lớn (1,50–1,95). Ở ~15 m ít điểm, phần chân sát mặt đường khó tách khỏi điểm mặt đường nên hộp bị hụt phía dưới (cũng có thể là người thấp). Xe con s01f40 idx 2: 4,44 × 1,99 × 1,46 m. Dài, cao trong khoảng sedan; rộng 1,99 m hơi vượt 1,90, do tôi kéo mép hộp hơi rộng hoặc tính cả gương; vượt khoảng 5%.
- **(c)** Xe cùng chiều phía trước s03f45 idx 1: yaw 270,5°, gần 270°. Xe ngược chiều phía trước bên trái s02f51 idx 4: yaw 89,7°, gần 90°. Cả hai đúng hướng.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s02f18, 1, Car | 4,47 | 4283 |
| s02f18, 2, Car | 11,08 | 1090 |
| s02f18, 5, Car | 23,13 | 287 |
| s02f18, 8, Car | 35,16 | 127 |

Quy luật: xa gấp đôi thì số điểm còn khoảng 1/3–1/4. Slide cũng giảm nhanh như vậy (1968 → 718 → 214 → 72). Số của tôi cao hơn slide một chút ở mỗi khoảng, có thể vì mấy xe này quay sườn về phía LiDAR và hộp hơi rộng.

Tự kiểm, đã sửa sau `preview` / `selfcheck`:
- s01f23 (Bus): rộng 3,28 m, quá khoảng 2,50–2,60 của bảng 4.3. Đã thu hẹp còn 2,67 m, sát hai thành xe.
- s01f40 (Car): dài 1,89 m vì chỉ thấy đuôi xe. Đã kéo dài thành 4,31 m theo bảng 4.3, về phía bị khuất.
- Sửa tương tự các xe con bị cờ dài dưới 3 m (s01f40 idx 4, s02f18 idx 4, s02f51 idx 3 và 5); thu hẹp s03f11 idx 2 (mép phía xa kéo quá ra) và idx 3.

Cảnh báo giữ nguyên: không có (chạy lại `sync`, 0/62 cuboid còn cảnh báo).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
**(a)** Model cần biết vật cách bao xa và to bao nhiêu để xe tránh va chạm, nên nhãn phải đo bằng mét; pixel đổi theo camera và khoảng cách nên không cho biết điều đó. Vật thưa điểm cần quy tắc riêng, vì nếu mỗi người đoán một kiểu thì model học kích thước sai hoặc học ra vật không có thật.

**(b)**
- Công cụ: Claude Code (VS Code, trên máy).
- Câu hỏi chính: đọc guide, guideline

**(c)**
- s03f11 idx 7 ít điểm: chưa chắc do màu xe hay kính.
- s02f18 idx 6 thấp: chưa chắc do hộp hụt chân hay người thấp thật.
- Chưa chắc nhãn chuẩn có tính gương chiếu hậu vào bề rộng xe hay không.
