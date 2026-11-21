# TEAM — Ngày 12

Mã bộ ảnh: **G01** · Hình thức: cá nhân

| Họ tên | MSSV | Đã gán đủ 22 ảnh? | Review chéo với (peer diff) | Số dòng disagreement đã xử lý |
|---|---|---|---|---|
| Nguyễn Quốc Thắng | 2A202603158 | có (+ 4 ảnh mở rộng S09–S10) | **Người gán thứ hai là AI** (một phiên Claude gán độc lập 22 ảnh, không xem nhãn của mình), chưa phải bạn do coach ghép | 47 dòng (34 mine · 4 peer · 9 ambiguous) |

**Lưu ý trung thực:** tại thời điểm nộp, mình chưa review chéo với một học viên cùng mã G01. Cột "peer" trong `disagreement_log.csv` là bản gán của AI (task CVAT `Day12-G01-annotator2`), không phải của một học viên. Khi có bạn được ghép, mình sẽ chạy lại `cabin_check diff` với file của bạn và cập nhật nếu coach yêu cầu.

Ca hai bên vẫn không thống nhất, cần hỏi coach (ghi tên ảnh + đối tượng):

- S04_rgb / S04_ir · `face` của người ở rear_middle: có vẽ hay không (tóc che gần hết).
- D01 · `face` + `face13`: có thấy mũi hay không (trùng đầu ống tay áo).
- D05 · `phone`: dải tối thẳng giữa các ngón có phải cạnh điện thoại không.
- D05 · `face13` vùng miệng: vị trí khóe miệng (hai bên lệch ~40 px).
- Ca đã báo coach không cần chờ review chéo: trẻ/em bé đang ngồi trong ghế ở S02 (rear_right), S03 (rear_left), S05 (rear_right, rear_left), S07 (rear_middle), S08 (rear_right). Guideline nói SVIRO "không có trẻ em ngồi" và không có lớp cho trường hợp này.

## Cá nhân

### NguyenQuocThang-2A202603158
- Phần mình làm (ngoài 22 ảnh của mình): tự review với một bản gán độc lập của AI (chạy `cabin_check diff`, phân xử 47 dòng theo guideline, sửa 4 điểm thành `export_final_v2.zip`); gán thêm S09–S10 ở task riêng `Day12-G01-extra` (phần mở rộng a); chạy `rgb_ir_diff.py` (phần mở rộng c) để lấy số liệu cho `ir_log`; tổng hợp các ca có trẻ em để báo coach.
- Điều mình học được hôm nay: camera OMS nhìn về phía sau nên bên trái ảnh là `rear_right`. Trên ảnh NIR xoay 90° thì trái/phải phải xét theo trục y. `unknown` là một nhãn đúng, không phải lựa chọn để né. Ngoài ra, guideline có thể sai so với dữ liệu thật (bộ ảnh có trẻ em), khi đó cần leo thang thay vì tự đặt luật.
