# Danh sách lớp v0 — Nguyễn Quốc Thắng · G01

Mỗi lớp trả lời: **model cần nhận ra cái này để làm gì?** Cột cuối ghi lớp đó còn nhìn thấy được trên ảnh IR hay không, dựa trên các ảnh đã gán (D## là IR thật, S## là IR-sim).

## DMS (8 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `eye_left`, `eye_right` (trạng thái từng mắt) | open / closed / unknown | Phát hiện buồn ngủ: mắt nhắm kéo dài, PERCLOS, ngủ gật vài giây | Có, trừ khi đốm phản xạ LED hoặc tròng tối che mắt (D02, D05) thì phải `unknown` |
| 2 | `face13` (13 điểm mặt + cờ occluded/outside) | toạ độ, cờ cho từng điểm | Tính độ mở mắt (EAR), độ mở miệng (ngáp), tư thế đầu; làm đầu vào cho lớp 1 và lớp 3 | Có khi mặt rộng ≥ 60 px và quay về camera; mặt nghiêng (D04) thì điểm bên khuất là `outside` |
| 3 | `gaze_zone` | road, mirror_l/r/c, cluster, center, lap, other | Phát hiện mất tập trung: mắt rời đường quá ngưỡng thời gian (ví dụ > 2 s) thì cảnh báo | Một phần: hướng đầu thấy được, nhưng con ngươi hay bị phản xạ kính che nên nhiều khung phải ghi `other` |
| 4 | `head_pose` (yaw / pitch / roll), đề xuất thêm | góc độ, hoặc mức: thẳng / trái / phải / lên / xuống | Dự phòng cho lớp 3 khi không thấy mắt (đeo kính râm); phát hiện cúi đầu do ngủ gật | Có, vì dựa vào hình dạng đầu và vị trí mũi, không cần màu |
| 5 | `glasses` | none / clear / sun / unknown | Biết khi nào kết quả trạng thái mắt kém tin cậy; chọn chế độ phơi sáng hoặc đèn LED phù hợp | Thấy gọng; phân biệt clear/sun trên NIR khó (kính râm có thể trong suốt), nên không chắc thì ghi `unknown` |
| 6 | `phone` + `state` | held / mounted / lap | Phát hiện dùng điện thoại khi lái (cầm áp tai, nhắn tin trên đùi) | Có theo hình dạng (cạnh, góc); màn hình có thể sáng hoặc tối; không thấy cạnh hay góc thì không vẽ (D05) |
| 7 | `hand` + `side` + `on_wheel` | left / right; yes / no / unknown | Kiểm tra tay trên vô lăng (sẵn sàng nhận lại quyền lái từ chế độ tự lái), phát hiện tay đang cầm vật | Có; tay quá gần camera bị cháy sáng (D06) nên mép box chỉ ước lượng |
| 8 | `camera_blocked` (tag) | yes / no | Phát hiện camera bị che hoặc phá: hệ thống DMS phải báo lỗi chứ không im lặng | Có (ảnh tối hoặc trắng toàn khung) |

## OMS (8 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` + `seat` | driver … rear_right | Đếm người theo từng ghế: nhắc thắt dây ghế sau, điều khiển túi khí theo ghế có người | Có (hình dạng người rõ trên IR-sim, kể cả khi RGB tối: S04, S05) |
| 2 | `seatbelt` (theo từng ghế) | worn_correct / worn_incorrect / not_worn / unknown | Nhắc thắt dây cho đúng ghế (quy định Euro NCAP cho ghế sau) | Một phần: dây tối trên áo tối dễ mất; SVIRO không có ai thắt dây nên chưa dạy được `worn_*` |
| 3 | `infant_seat` + `facing` | rear / front | Tắt túi khí ghế phụ khi có ghế em bé quay về sau; nhắc không để quên em bé | Có theo hình vỏ ghế; nhưng hướng khó xác định khi ghế trống (S01, S06) |
| 4 | `child_seat` + `occupied` | no / unknown (đề xuất thêm yes) | Phát hiện trẻ bị bỏ quên trong xe (Child Presence Detection) | Có hình ghế; muốn biết ghế có người hay không thì phải thấy bên trong, mà vùng tối trên RGB thì phải nhờ IR (S01, S08) |
| 5 | `occupant_class` (đề xuất thêm): adult / child / infant | 3 lớp | Phân loại người ngồi để quyết định túi khí; CPD cần biết "có trẻ" chứ không chỉ "có ghế trẻ em" | Có theo kích thước và hình dạng (trẻ thấy rõ trên S03, S05, S07, S08) |
| 6 | `object` + `type` | bag / box / cup / other | Nhắc để quên đồ khi rời xe; tránh nhầm vật với người (báo động giả) | Có theo hình dạng; mất màu nên không phân biệt được bằng màu (ba lô đỏ ở S06 thành xám) |
| 7 | `pet` (đề xuất thêm) | dog / cat / other | Nhắc quên thú cưng trong xe nóng | Có theo hình dạng |
| 8 | `posture` / out-of-position (đề xuất thêm) | normal / leaning / feet_up … | Túi khí thích ứng: không bung mạnh khi người ngồi quá gần | Có theo hình dạng cơ thể |

## 2 câu hỏi leo thang

1. **Câu hỏi:** Guideline v1.0 nói bộ SVIRO "không có trẻ em ngồi", nên không có lớp cho trẻ em hay em bé, và `child_seat.occupied` không có giá trị `yes`. Nhưng bộ G01 có trẻ ngồi trong ghế trẻ em và em bé nằm trong ghế em bé. Vậy ghế có trẻ thì gán thế nào: thêm `occupied=yes`, thêm lớp `child`/`infant`, hay không gán gì?
   **Khung ví dụ:** S03 (rear_left: trẻ trong ghế nâng), S05 (cả rear_right và rear_left), S02 (rear_right: em bé trong nôi), S07 (rear_middle), S08 (rear_right).
   **Bạn tạm gán thế nào và vì sao:** Vẽ box ghế (`child_seat` hoặc `infant_seat`) vì ghế là vật thấy được. Không vẽ box cho trẻ, theo GUIDE §3 "thấy hình giống trẻ em thì báo coach, không gán". Với `child_seat`, ghi `occupied=unknown` theo RULES §4 (ca guideline không nói tới thì chọn unknown), không ghi `no` vì ghế không trống. Đã ghi vào ca khó trong `check_final.txt` để báo coach.

2. **Câu hỏi:** `infant_seat.facing` không có giá trị `unknown`. Khi ghế em bé trống, nhìn từ camera đặt phía trước và quay về sau, thì dựa vào dấu hiệu nào để chọn `rear` hay `front`: tay xách, mái che, phần đầu cao của vỏ ghế?
   **Khung ví dụ:** S01_rgb và S01_ir (rear_right), S06_rgb và S06_ir (rear_left).
   **Bạn tạm gán thế nào và vì sao:** Gán `rear`. Lý do: ghế cùng mẫu vỏ và cùng cách đặt như ghế ở S02. Ở S02 thấy em bé có đầu hướng về camera và chân hướng về lưng ghế, tức là quay về sau. Ngược lại, S07 và S08 thấy em bé quay mặt về camera nên ghi `front`. Đề xuất: thêm giá trị `unknown` cho `facing`, hoặc ghi rõ dấu hiệu nhận biết trong guideline.
