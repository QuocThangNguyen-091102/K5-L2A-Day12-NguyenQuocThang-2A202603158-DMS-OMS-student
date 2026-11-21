# Tự đánh giá — Nguyễn Quốc Thắng · G01

## 1. Tự rà soát 4 cảnh

Mở lại bài, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ. Rà bằng ảnh chồng box và điểm lên bản sao đã tăng tương phản, rồi so với một lượt gán độc lập thứ hai (không xem nhãn trước).

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| S01_rgb / S01_ir | `infant_seat` rear_right, `facing` | `guess` | Lúc đầu nghiêng về `front` vì phần mở của nôi hướng về camera, tức là đoán từ một dấu hiệu yếu. Sau khi thấy ở S02 cùng mẫu nôi có em bé quay mặt về lưng ghế, đổi thành `rear` và ghi thành câu hỏi leo thang | Có (trước export cuối) |
| S03_rgb / S03_ir | `object` rear_middle, `type` | `guess` | Lúc đầu định ghi `bag` vì thấy một sợi giống quai; nhìn kỹ thì sợi đó có thể là khóa dây an toàn ghế giữa. Không chắc nên ghi `other` (GUIDE 4.8) | Có (trước khi đẩy lên CVAT) |
| D03 | `face` + `face13` | `missing` | Lúc đầu không vẽ mặt vì ảnh tối, tưởng mặt quay đi hẳn. Xem lại với gamma 1.8 thì thấy đường viền mũi và gọng kính phải, nên vẽ `face` và `face13`: mắt phải `occluded`, mắt trái và miệng `outside` | Có (trước export cuối) |
| D02, D05 | `face.glasses` | `guess` | Đã ghi `clear` (D02, vì nhìn xuyên được tròng) và `sun` (D05, vì tròng tối). Trên NIR cả hai dấu hiệu đều không chắc: kính râm có thể trong suốt, còn tròng tối có thể chỉ là bóng hốc mắt. Đổi cả hai thành `unknown` | Có (trước export cuối) |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: **`guess`**. Mình điền một giá trị cụ thể (`front`, `bag`, `clear`, `sun`) dựa vào kiến thức chung, ví dụ "nôi em bé thường đặt quay về sau" hay "tròng tối thì là kính râm", thay vì dựa vào thứ thực sự thấy trên pixel.

Vì sao mình mắc: ảnh NIR và ảnh dựng tối, mờ, nên dễ lấp chỗ trống bằng suy luận. Thêm nữa, giá trị `unknown` cảm giác như "chưa làm xong".

Lần sau tránh bằng cách: trước khi chọn giá trị khác `unknown`/`other`, tự hỏi **"pixel nào cho thấy điều này?"** và chỉ tay được vào chỗ đó. Xem mỗi ca khó với hai cách hiển thị (gamma và kéo giãn tương phản). Ca nào vẫn chưa chắc thì ghi `unknown` và đưa vào ghi chú ca khó hoặc câu hỏi leo thang.

## 3. Ghi chú về disagreement_log

(Người gán thứ hai là AI, chưa phải bạn do coach ghép; xem `TEAM.md`.)

Một dòng bạn đúng và mình sai: **D06 · hand side=left · box** (IoU giữa hai bản chỉ 0.26). Box của mình [335,270,540,600] lệch hẳn sang trái và bỏ mất nửa phải của bàn tay bị cháy sáng; box của bên kia [440,250,708,600] ôm đúng vùng tay. Đã sửa trong `export_final_v2.zip`.

Mình học được gì từ dòng đó: khi một vật bị **bão hoà trắng** trên ảnh IR, mắt dễ lấy mép của vùng xám lân cận (ống tay áo, ghế) làm mép vật. Lần sau phải vẽ box theo **đúng ranh giới vùng bão hoà**, rồi vẽ chồng box lên ảnh để kiểm, thay vì chỉ ước lượng trên lưới toạ độ. Ngoài ra, ở D03, D02 và D05 bên kia cũng giúp mình sửa thêm (mặt bị bỏ sót, cờ chóp mũi), còn những chỗ bên kia đoán (điểm mắt đặt trên đốm sáng của đèn phát ở D04, mắt trái `closed` ở D06) thì mình giữ nhãn của mình theo guideline.
