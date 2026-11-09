# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện chở người cá nhân/gia đình cỡ nhỏ đến trung (sedan, hatchback, SUV, CUV, coupe, mui trần) và xe bán tải chở khách/gia đình|Ví dụ: Toyota Camry, Honda CR-V, Mazda 3, Ford Ranger. Phân biệt: Thấp hơn van, không có thùng chở hàng thương mại rời như truck.  |
| `truck` |Phương tiện vận tải hàng hóa cỡ trung đến lớn, có cabin lái và phần thùng chở hàng riêng biệt (thùng kín, thùng bạt, xe ben, xe container, xe đầu kéo). |Ví dụ: Xe tải 3.5-15 tấn, xe ben, xe container. Phân biệt: Thùng hàng lớn/rời phân tách rõ với cabin; hình dáng dài hoặc phân cao hẳn so với van. |
| `bus` |Phương tiện vận tải hành khách công cộng hoặc thương mại cỡ lớn (từ 16–29 chỗ trở lên), thân dài hình hộp, trần cao, nhiều cửa sổ dọc thân xe. |Ví dụ: Xe buýt đô thị, xe khách 45 chỗ. Phân biệt: Chiều dài vượt trội so với van, thiết kế chuyên dụng chở đông hành khách. |
| `van` |Phương tiện hình hộp liền khối (box-shaped), trần cao, khoang chở người/hàng hóa nối liền với cabin lái, thường có cửa trượt bên hông hoặc cửa mở 2 cánh phía sau. |Phân biệt: Thân hình hộp vuông vức liền khối (khác truck có thùng rời, khác car ở trần cao hẳn). |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Bounding box phải ôm sát các góc mép thực tế của vỏ xe/bánh xe (tight bounding box), không để thừa khoảng trống lề (margin > 2-3 px) và không cắt lẹm vào thân xe.
- Vật thể bị che khuất: Nếu vật thể bị che khuất một phần (partial occlusion) dưới 70-80% nhưng vẫn nhận dạng rõ là phương tiện, vẽ 1 bounding box ước lượng bao quanh toàn bộ phần nhìn thấy + phần bị che khuất của phương tiện đó.
- Vật thể bị cắt bởi rìa ảnh: Vẽ bounding box ôm sát phần thân xe xuất hiện trong khung hình, kéo chạm sát mép rìa ảnh (không vẽ chờm ra ngoài biên ảnh).
- Vật thể rất nhỏ/mơ hồ: Bỏ qua không gán nhãn các vật thể ở quá xa có kích thước dưới 10x10 pixel, bị mờ nhoè nặng do chuyển động/ánh sáng, hoặc bị che khuất > 80% không thể xác định chính xác ranh giới và lớp.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_009` |Nhiều phương tiện hình hộp dạng 16 chỗ (Ford Transit) và xe tải nhỏ chạy nối đuôi nhau ở khoảng cách tầm trung, dễ nhầm giữa car, van và truck. | Gán van cho các xe 16 chỗ liền khối; gán truck cho xe có thùng chở hàng rời phía sau.|Thân xe liền khối trần cao thuộc schema van, xe có kết cấu cabin + thùng hàng độc lập thuộc schema truck. Giúp sửa triệt để lỗi sai nhầm class đúp (FP + FN). |
| `drive_024` |Hàng xe đỗ nối đuôi nhau ở lề đường tầm xa, các xe ở mép góc ảnh bị che khuất một phần bởi cây cối và dải phân cách. |Bổ sung đầy đủ bounding box cho toàn bộ các xe nhìn rõ bánh/mui xe; bỏ qua xe bị che khuất > 80% ở tít mép đường. |Khôi phục các nhãn bị bỏ sót (False Negatives) để nâng Recall lên tiêu chuẩn mà không làm phát sinh nhãn nhiễu ngoài quy chuẩn. |
| `drive_041` | Nhiều xe hơi nhỏ (sedan/hatchback) di chuyển song song ở các làn đường khác nhau, một số xe bán tải (pickup) đi cạnh xe tải chở hàng.|Gán xe bán tải gia đình là car; gán xe tải thùng là truck. Chuẩn hóa kích thước box ôm sát bánh xe. |Theo đúng định nghĩa schema: xe bán tải chở khách/cá nhân xếp vào car. Viền box ôm sát giúp duy trì chỉ số Mean IoU đạt mức chuẩn xác. |
