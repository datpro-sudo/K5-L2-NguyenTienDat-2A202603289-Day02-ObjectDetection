# Mini guideline — bài làm cá nhân

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe ô tô con chở người, thân thấp, thường 4 đến 5 chỗ: sedan, hatchback, SUV, crossover. | Có kính chắn gió thấp và thân xe thấp, không có thân hộp cao kéo dài ra sau. Phân biệt với `van`: `van` có thân hộp cao hơn và liền khối. |
| `truck` | Xe tải hoặc xe chở hàng có cabin và thùng hàng tách rời: xe tải nhỏ, xe tải lớn, xe bán tải, xe đầu kéo kèm rơ moóc. | Cabin ở phía trước, thùng hàng phía sau cao hoặc dài hơn cabin. Phân biệt với `van`: `truck` có cabin tách riêng với thùng. |
| `bus` | Xe buýt hoặc xe khách cỡ lớn chở nhiều người. | Thân rất dài, cao, liền một khối và có hàng cửa sổ dọc thân. Phân biệt với `van`: `bus` dài và lớn hơn rõ rệt. |
| `van` | Xe tải van hoặc minivan có thân hộp cao, liền một khối. | Cabin và khoang chở nối liền nhau, không có thùng hàng tách rời, nhỏ hơn `bus`. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: cạnh trái, phải, trên, dưới của box chạm đúng phần thân xe nhìn thấy được, không lấy thêm mặt đường, bóng đổ, trời hay xe bên cạnh. Mỗi xe có một box riêng, kể cả cùng lớp.
- Vật thể bị che khuất: vẫn gán box quanh phần nhìn thấy được nếu nhận ra được lớp. Hai xe chồng lên nhau thì vẽ hai box riêng trên phần nhìn thấy của từng xe.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa ảnh, không đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: nếu chỉ còn mảnh rất nhỏ và không xác định chắc lớp thì không tự đoán, ghi lại để hỏi giảng viên.
- Xe đang đỗ vẫn được gán lớp theo loại xe.
- Không gán nhãn cho: người đi bộ, xe đạp, mô tô/xe máy, biển báo, đèn giao thông, phản chiếu trong kính hoặc gương, bảng quảng cáo có hình xe, và mọi vật thể ngoài bốn lớp trên.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_007` | Có xe thân hộp cao, khó phân biệt giữa `van` với `car` hoặc `bus`. | Gán `van` cho xe có thân hộp cao liền khối, nhỏ hơn `bus` và cao hơn `car`; các xe còn lại gán `car`. Xe nào vẫn không chắc thì ghi lại và hỏi giảng viên, không tự chọn. | Schema yêu cầu không tự chọn khi van/minivan mơ hồ với `bus` hoặc `car`, nên chỉ gán `van` khi đặc điểm thân xe khớp định nghĩa. |
| `drive_004` | Xe bị cắt bởi rìa ảnh. | Vẽ box quanh phần xe nhìn thấy được, cạnh box chạm đúng rìa ảnh, không mở rộng ra ngoài. | Quy tắc: box chạm đúng rìa ảnh khi vật thể bị cắt khung, không đoán phần nằm ngoài ảnh. |
| `drive_002` | Xe rất nhỏ ở xa, chỉ chiếm vài chục điểm ảnh. | Chỉ gán box khi vẫn xác định được lớp xe; nếu chỉ còn mảnh quá nhỏ không chắc lớp thì không tự đoán. | Quy tắc: mảnh rất nhỏ không xác định chắc lớp thì không tự đoán, ghi lại và hỏi giảng viên. |
