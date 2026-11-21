# Phần mở rộng — Nguyễn Quốc Thắng · G01

Phần mở rộng không cộng điểm (RUBRIC), chỉ để coach nhận xét. Lựa chọn chính là **(a)**. Hai hướng (b) và (c) ghi ngắn vì đã có số liệu sẵn trong quá trình làm bài.

## (a) S09–S10: task riêng `Day12-G01-extra`

- File nộp: `export_extra.zip`, kiểm bằng `check_extra.txt` (`--expect 4`), kết quả 0 lỗi, 0 cảnh báo.
- S09: một `object` type=box ở `rear_right` (bên trái ảnh). `rear_middle` và `rear_left` trống.
- S10: một `child_seat` đỏ, trống, `occupied=no` ở `rear_right`. Ghế nằm trong bóng râm, giữa ảnh có mảng nắng bão hoà (~16% pixel > 250 ở phần ba giữa, trên cả RGB lẫn IR-sim). `camera_blocked=no`.
- Nhờ coach so lớp ghế với nhãn gốc SVIRO và phân loại lỗi (sai ánh xạ / sai lớp / mơ hồ thật). Tự đánh giá trước:
  - Không có ca nào thuộc loại "sai ánh xạ", vì mọi box đều kiểm lại quy tắc trái ảnh = `rear_right`. Một phiên gán độc lập thứ hai cũng ra cùng ghế.
  - Ca "mơ hồ thật" là `infant_seat.facing` khi ghế trống (S01, S06) và các ghế có trẻ (S02, S03, S05, S07, S08). Đây đúng là chỗ lớp gốc SVIRO ("infant seat có em bé", "child seat có trẻ") không có ánh xạ sang Guideline v1.0.

## (b) Đề xuất sửa guideline (có bằng chứng)

| # | Vấn đề | Bằng chứng trong G01 | Đề xuất |
|---|---|---|---|
| 1 | GUIDE §3 nói "bộ ảnh không có trẻ em ngồi" | 5/10 cảnh có trẻ hoặc em bé: S02, S03, S05 (2 trẻ), S07, S08 | Thêm `occupied=yes` cho `child_seat` và `infant_seat`, hoặc thêm lớp `child` / `infant`. Model CPD cần đúng thông tin "có trẻ" |
| 2 | SVIRO không ai thắt dây | S02, S04 đều `not_worn`; không có mẫu `worn_correct` hay `worn_incorrect` | Không dùng SVIRO để dạy lớp seatbelt; bổ sung dữ liệu thật có người thắt dây đúng và sai |
| 3 | `infant_seat.facing` không có `unknown` | Ghế trống ở S01, S06: không chắc hướng | Thêm `unknown`, hoặc mô tả dấu hiệu nhận biết (vị trí phần đầu cao của vỏ, tay xách) |
| 4 | GUIDE §4.4 tiêu đề "Sáu quy tắc chung" nhưng chỉ liệt kê 5 | Văn bản guideline | Bổ sung quy tắc thứ 6 hoặc sửa tiêu đề |
| 5 | Box `face` trên SVIRO bắt buộc kèm thuộc tính mắt, kính, vùng nhìn trong schema CVAT, dù guideline nói OMS "chỉ box" | S02 face: để mặc định unknown/other | Tách label `face_oms` không có thuộc tính, hoặc ghi rõ "để mặc định" |

## (c) `rgb_ir_diff.py`: 2 cảnh RGB tối

Lệnh chạy: `python scripts/rgb_ir_diff.py G01/oms --out ir_pairs`. Ảnh ghép nằm trong `ir_pairs/`, không nộp vì đây là ảnh.

| Cảnh | RGB mean / % gần đen | IR-sim mean / % gần đen | Trường IR-sim còn giữ được | Trường IR-sim làm mất |
|---|---|---|---|---|
| S05 | 12.5 / 80.3% | 48.4 / 19.9% | Hình dạng hai `child_seat` và việc có trẻ bên trong (dẫn tới `occupied=unknown` và báo coach); `seat` theo xe | Màu ghế đỏ và xanh (không cần cho schema) |
| S06 | 13.5 / 81.2% | 50.6 / 14.8% | Đường viền `infant_seat` ở rear_left (RGB phần ba phải: mean 7.8, 88% gần đen) | Ba lô đỏ ở rear_middle gần như cùng tông với đệm ghế: IR-sim **làm mất** độ tương phản mà RGB có. Vậy IR không phải lúc nào cũng tốt hơn |

Kết luận: với ảnh dựng SVIRO, IR-sim giữ được các trường **hình dạng** (lớp, ghế, có người hay không) ở vùng tối, nhưng mất các trường dựa vào **màu và độ tương phản màu**. Hiện tượng của IR thật (phản xạ kính, mắt sáng, ánh sáng giảm theo khoảng cách) chỉ ghi cho D## (xem `ir_log.csv`).
