# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Tiến Đạt
- MSSV: 2A202603289
- Email: nguyentiendat.qc2004@gmail.com
- Lớp: E403
- Link repo bài làm: https://github.com/datpro-sudo/K5-L2-NguyenTienDat-2A202603289-Day02-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1X2B5tD1lZVnwYdZ5KOdLWmPwZf_aJzS5

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Ba ca mơ hồ đã quyết định:

1. **`drive_007` — van so với car/bus.** Chỉ gán `van` khi xe có thân hộp cao liền khối, nhỏ hơn `bus` và cao hơn `car`; các xe còn lại gán `car`. Xe nào vẫn không chắc thì không tự chọn mà ghi lại để hỏi giảng viên.
2. **`drive_004` — xe bị cắt bởi rìa ảnh.** Vẽ box quanh phần nhìn thấy được, cạnh box chạm đúng rìa ảnh, không đoán phần nằm ngoài ảnh.
3. **`drive_002` — xe rất nhỏ ở xa.** Chỉ gán box khi vẫn xác định được lớp; mảnh quá nhỏ không chắc lớp thì không tự đoán.

Các quyết định này áp dụng đúng schema bốn lớp (`car`, `truck`, `bus`, `van`). Không gán xe máy, người đi bộ, biển báo, phản chiếu hay hình xe trên bảng quảng cáo.

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/drive_001.txt` đến `drive_050.txt`).
- Kết quả checker và lỗi đã xử lý: `python tools/check_yolo_labels.py` báo "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ". Lỗi đã xử lý trong quá trình làm: (a) CVAT xuất lần đầu chỉ có 4 file nhãn do bản xuất cũ bị cache, đã xuất lại với tên custom mới; (b) một số ảnh (`drive_012`, `drive_017`, `drive_018`) ban đầu vẽ bằng polygon nên không được xuất sang YOLO Detection, đã đổi sang Rectangle; (c) ảnh không có xe phải có file `.txt` rỗng nên đã tạo các file còn thiếu.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_005`, `drive_006`, `drive_009`, `drive_012`, `drive_019`, `drive_024`, `drive_026`, `drive_027`, `drive_041`, `drive_050`. Đây là các ảnh mình mở lại và sửa nhiều nhất trong quá trình rework (số box khớp với gold tăng mạnh giữa lần chạy đầu và lần cuối).
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: lỗi chính là **bỏ sót xe**, đặc biệt xe nhỏ, ở xa, bị che hoặc cắt rìa, ở các ảnh đông xe. Cách sửa là phóng to ảnh trong CVAT và vẽ thêm box bằng Rectangle cho từng xe còn thiếu. Số box mình vẽ trước và sau khi sửa (lần chạy đầu → lần cuối):

  | Ảnh | Gold | Box lúc đầu | Box sau sửa | Khớp gold lúc đầu → cuối |
  |---|---:|---:|---:|---:|
  | `drive_005` | 24 | 10 | 24 | 6 → 21 |
  | `drive_006` | 21 | 10 | 21 | 8 → 19 |
  | `drive_009` | 21 | 11 | 21 | 6 → 21 |
  | `drive_012` | 23 | 14 | 23 | 13 → 23 |
  | `drive_019` | 27 | 14 | 27 | 9 → 22 |
  | `drive_024` | 30 | 10 | 30 | 6 → 25 |
  | `drive_026` | 39 | 20 | 39 | 17 → 39 |
  | `drive_027` | 31 | 12 | 31 | 8 → 26 |
  | `drive_041` | 24 | 6 | 24 | 4 → 24 |
  | `drive_050` | 18 | 9 | 18 | 6 → 16 |

  Một phần box còn lại chưa khớp vì sai lớp (car/van/truck/bus) hoặc lệch vị trí (IoU dưới 0.5); mình kiểm tra lại lớp theo định nghĩa trong `GUIDELINE_MINI.md`.
- Commit khóa nhãn: `8a2f749` ("Day 2: add 50 YOLO train labels"), đưa đủ 50 file nhãn đã qua các vòng rework lên repo; sau đó commit `b2c10d0` ("Add GUIDELINE_MINI") thêm mini guideline. Không sửa nhãn sau khi đã chạy đánh giá lần cuối.

## 4. Gold QC và rework

Kết quả các lần chạy `evaluate_annotations.py` trong quá trình rework:

| Lần chạy | TP | FP | FN | Precision | Recall | F1 | mean_iou_of_matches | passed |
|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 1 (trước sửa) | 294 | 133 | 359 | 0.6885 | 0.4502 | 0.5444 | 0.8751 | false |
| 2 | 401 | 146 | 252 | 0.7331 | 0.6141 | 0.6683 | 0.9286 | false |
| 3 | 460 | 133 | 193 | 0.7757 | 0.7044 | 0.7384 | 0.9481 | false |
| 4 | 483 | 147 | 170 | 0.7667 | 0.7397 | 0.7529 | 0.9622 | false |
| 5 | 508 | 147 | 145 | 0.7756 | 0.7779 | 0.7768 | 0.9704 | false |
| 6 (cuối, sau sửa) | 532 | 123 | 121 | 0.8122 | 0.8147 | 0.8135 | 0.9718 | **true** |

Kết quả đầy đủ nằm trong `outputs/annotation_evaluation.json` của repo.

Cách rework: ở lần chạy đầu, recall thấp (0.45) trong khi mean IoU đã đạt, nên phần lớn lỗi là FN do bỏ sót xe hoặc xe bị nhầm lớp, chứ không phải box lỏng. Mình rà lại từng ảnh theo thứ tự số box còn thiếu giảm dần (các ảnh có nhiều xe như `drive_024`, `drive_026`, `drive_027`, `drive_041`, `drive_009`, `drive_008`), vẽ thêm các xe nhỏ, xa, bị che hoặc bị cắt rìa, và kiểm tra lại class của các box. Sau mỗi đợt sửa mình xuất lại nhãn từ CVAT, chép vào `dataset/labels/train/` và chạy lại đánh giá. Mình không suy nguyên nhân chỉ từ số FP/FN mà mở ảnh để kiểm tra từng box.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU Tesla T4 (14913 MiB), Python 3.13.16, Ultralytics 8.4.208, Torch 2.14.1+cu130 (CUDA).
- Các tham số thay đổi so với notebook nếu có và lý do: theo `args.yaml` của lần train: model `yolo26n.pt`, 50 epoch, `imgsz` 640, `batch` tự động (-1). Không thay đổi tham số so với notebook.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3445 |
| Recall | 0.4984 |
| mAP50 | 0.3752 |
| mAP50–95 | 0.2515 |

Kết quả dự đoán và log train: [assets/eval_predictions/](assets/eval_predictions/) và [assets/yolo26_runs/](assets/yolo26_runs/) (giải nén từ `day2_colab_outputs.zip`).

Nhận xét chung: kết quả thấp là hợp lý với bộ dữ liệu chỉ 50 ảnh train và 10 ảnh eval. Precision (0.34) thấp hơn recall (0.50), nghĩa là model dự đoán nhiều box sai hoặc thừa hơn là bỏ sót. Mình xem như tín hiệu về độ phủ dữ liệu, mất cân bằng lớp và chất lượng annotation, không dùng để sửa lại gold labels.

Chỉ số theo lớp trên 10 ảnh eval:

| Lớp | Số ảnh | Số vật thể | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|---:|
| all | 10 | 113 | 0.345 | 0.498 | 0.375 | 0.252 |
| car | 10 | 94 | 0.692 | 0.894 | 0.913 | 0.648 |
| truck | 5 | 5 | 0.322 | 0.600 | 0.262 | 0.196 |
| bus | 2 | 2 | 0.000 | 0.000 | 0.015 | 0.012 |
| van | 6 | 12 | 0.365 | 0.500 | 0.311 | 0.150 |

Ví dụ cụ thể: trong ảnh `drive_052.jpg` (eval), nhãn có một xe buýt hai tầng lớn ở bên trái ảnh được gán lớp `van`, nhưng model chỉ dự đoán các xe `car` nhỏ ở phía xa và không phát hiện được chiếc xe lớn này. Ở `drive_053.jpg`, chiếc xe buýt xanh trắng ở giữa ảnh cũng được gán `van` trong nhãn mà model bỏ sót, trong khi nó dự đoán `bus` cho chiếc xe ở góc phải trên (nhãn của vị trí đó là `van`). Chi tiết xem [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/) và `val_batch0_labels.jpg` / `val_batch0_pred.jpg` trong `assets/yolo26_runs/vehicle_eval/`.

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?** Lớp `bus` có recall thấp nhất: 0.000 (chỉ có 2 vật thể trên 2 ảnh eval, mAP50 0.015). Ảnh minh họa là `drive_058.jpg`: nhãn có một xe `bus` ở phía trên bên phải ảnh, trong khi model chỉ dự đoán `bus` (độ tin cậy 0.4) ở một vị trí khác nên không khớp. Ngoài ra `drive_052.jpg` và `drive_053.jpg` có xe buýt lớn được gán là `van`, và model bỏ sót cả hai. Lớp `van` cũng thấp (recall 0.500, 12 vật thể) và `truck` (recall 0.600, 5 vật thể) cũng thấp hơn `car` (recall 0.894, 94 vật thể). Lớp càng ít mẫu thì recall càng thấp.
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?** Điều đó cho thấy model tìm đúng vị trí tương đối nhưng box chưa khít (IoU thấp ở các ngưỡng cao). Cần kiểm tra độ sát của box nhãn: có lỏng, lấy thêm mặt đường hoặc xe bên cạnh không, có nhất quán giữa các ảnh không, và box ở xe bị che hoặc cắt rìa có đúng quy tắc không. Trong kết quả này mAP50 là 0.375 và mAP50–95 là 0.252, tức chênh lệch khoảng 0.12, cho thấy box dự đoán chưa thật khít.
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?** Theo confusion matrix (`assets/yolo26_runs/vehicle_eval/confusion_matrix.png`), nhầm lẫn nằm ở nhóm xe lớn. Trong 12 vật thể `van` thật, model dự đoán 5 là `van`, 4 là `bus`, 2 là `truck`, còn 1 bị bỏ sót. Trong 5 vật thể `truck` thật, 3 bị dự đoán thành `van` và 2 thành `car`, không cái nào được nhận đúng `truck` ở mức độ tin cậy được tính trong ma trận. Trong 94 `car` thật, 92 được nhận đúng, nên `car` ít bị nhầm. Điều này liên quan đến guideline: định nghĩa của mình phân biệt `van` (thân hộp cao liền khối, nhỏ hơn `bus`) với `bus` (dài và lớn), nhưng trong nhãn eval có xe buýt lớn như ở `drive_052` và `drive_053` được gán `van`, nên ranh giới `van`/`bus`/`truck` mơ hồ ngay cả với người gán nhãn và model khó học khi chỉ có ít mẫu. Đây cũng chính là ca mơ hồ mình đã ghi ở `drive_007`. Lưu ý các giá trị "background" lớn trong ma trận là do ma trận tính ở ngưỡng tin cậy rất thấp, nên không dùng số đó để kết luận về độ chính xác.
4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?** Nếu dùng ảnh eval để train, model đã nhìn thấy đáp án nên các chỉ số trên chúng không còn phản ánh khả năng tổng quát hóa sang ảnh mới. Trong notebook này 10 ảnh eval cũng được dùng làm validation để theo dõi quá trình train và chọn `best.pt`, nên chúng không phải test set độc lập hoàn toàn; số liệu eval vì vậy có thể hơi lạc quan.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Xuất nhãn từ CVAT | File zip chỉ có 4 file nhãn dù đã vẽ nhiều ảnh hơn | Đối chiếu bằng cách xuất định dạng CVAT for images 1.1, xuất lại YOLO với tên custom mới | Xuất lại đầy đủ nhãn các ảnh đã vẽ |
| Xuất nhãn từ CVAT | Một số ảnh không xuất sang YOLO vì vẽ bằng polygon | Vẽ lại bằng Rectangle | Nhãn của các ảnh đó xuất được |
| Chuẩn bị dataset | Ảnh không có xe không có file `.txt` | Tạo file `.txt` rỗng cho các ảnh thiếu | Checker báo đủ 50/50 file hợp lệ |
| Chạy lệnh trên Windows | `wc` không có trong PowerShell | Dùng `(git ls-files ...).Count` | Đếm được số file |
| Git add | Cảnh báo LF sẽ đổi thành CRLF | Xác định chỉ là cảnh báo, không ảnh hưởng nội dung nhãn | Vẫn commit và push bình thường |
| Gold QC | F1 ban đầu chỉ 0.544, recall thấp | Rà lại từng ảnh, bổ sung xe bị bỏ sót, sửa class | F1 tăng qua từng lần chạy (bảng mục 4) |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md). Phần dưới chỉ nêu bằng chứng có trong repo; mình không tự nhận hoàn thành bước chưa chạy hoặc chưa kiểm chứng.

| Tiêu chí (điểm) | Bằng chứng trong bài | Trạng thái |
|---|---|---|
| Hoàn thành và định dạng YOLO (10) | 50 file `drive_001.txt` đến `drive_050.txt` cùng basename với ảnh; `check_yolo_labels.py` báo "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ" (5 cột, tọa độ chuẩn hóa, class ID 0 đến 3). | Đã đạt |
| Đầy đủ vật thể (20) | Ở lần chạy gold cuối, recall là 0.8147 (TP 532, FN 121), tăng từ 0.4502 ở lần đầu nhờ bổ sung xe nhỏ, xa, bị che hoặc cắt rìa. Vẫn còn 121 xe chưa khớp, tập trung ở các ảnh như `drive_029`, `drive_030`, `drive_040`, `drive_048`. | Đã đạt ngưỡng, còn cải thiện được |
| Độ bám sát và hình học box (20) | mean_iou_of_matches đạt 0.8751 ở lần đầu và 0.9718 ở lần cuối, cao hơn ngưỡng 0.70 và ngưỡng xuất sắc 0.80. Trong quá trình rework, lỗi chính là bỏ sót xe và sai lớp, không phải box lỏng. | Đã đạt |
| Phân lớp nhất quán (15) | Dùng đúng bốn lớp `car`, `truck`, `bus`, `van`, không gán xe máy, người đi bộ hay vật thể ngoài schema. Quyết định lớp theo định nghĩa trong `GUIDELINE_MINI.md`. Gold QC ghép box cùng lớp nên các box còn sai lớp vẫn nằm trong FP/FN. Ở bước train, confusion matrix cho thấy model nhầm nhiều nhất giữa `van`, `bus` và `truck` (mục 5). | Đã làm, còn một số box lệch lớp |
| Mini guideline và quyết định mơ hồ (10) | `GUIDELINE_MINI.md` có định nghĩa bốn lớp, quy tắc box và ba ca mơ hồ (`drive_007`, `drive_004`, `drive_002`) kèm lý do theo schema. | Đã có |
| Gold-set annotation QC (15) | Chạy `evaluate_annotations.py` nhiều lần; F1 tăng 0.5444 → 0.6683 → 0.7384 → 0.7529 → 0.7768 → 0.8135 và mean IoU 0.9718, đạt cả hai ngưỡng (`passed: true`). Có vòng rework giải thích ở mục 4 (mở lại ảnh, bổ sung box, sửa lớp, chạy lại). Không sửa hay sao chép gold labels. | Đã đạt |
| Pipeline YOLO và phân tích (10) | Colab chạy được; dùng đúng split (50 ảnh train, 10 ảnh eval, không đưa eval vào train); có precision 0.3445, recall 0.4984, mAP50 0.3752, mAP50–95 0.2515 và ảnh dự đoán trên 10 ảnh eval; có nhận xét nguyên nhân ở mục 5. | Đã có, kèm phân tích theo lớp và confusion matrix ở mục 5 |

**Điều kiện bắt buộc:** đủ 50 file nhãn và đã qua `check_yolo_labels.py`; có gold-set evaluation (`outputs/annotation_evaluation.json`); không dùng eval để train; không sửa `gold_train_labels/`; không tìm nhãn ngoài repository.

**Còn thiếu hoặc chưa kiểm được:** ở lần chạy gold cuối vẫn còn 121 FN và 123 FP, tập trung ở các ảnh như `drive_029`, `drive_030`, `drive_040`, `drive_048`, nên chưa tối ưu hoàn toàn về nhận diện lớp. Chưa phân tích sâu từng ảnh eval ngoài `drive_052`, `drive_053`, `drive_058`. mAP trên 10 ảnh eval thấp (mAP50 0.375) và 10 ảnh này cũng là validation nên không phải test độc lập.

**Mức chất lượng annotation tự xếp:** **Đạt** (F1 0.8135 ≥ 0.80 và mean IoU 0.9718 ≥ 0.70). Chưa đạt mức Xuất sắc vì F1 dưới 0.90.

## 8. Công cụ AI và tài liệu tham khảo

- Công cụ AI: Claude (Anthropic). Dùng để giải thích cách xuất và nhập nhãn trong CVAT, đọc và phân tích file kết quả `evaluate_annotations.py` để biết ảnh nào cần sửa trước, soạn khung `GUIDELINE_MINI.md` và báo cáo. Phần mình tự làm và tự kiểm tra: vẽ và sửa toàn bộ box trên CVAT, xuất nhãn, chạy các lệnh kiểm tra và đánh giá, chạy notebook Colab và đọc kết quả.
- Tài liệu tham khảo ngoài repo: tài liệu CVAT về xuất định dạng Ultralytics YOLO Detection, tài liệu Ultralytics YOLO.
- Không đính kèm mật khẩu, token hay `gold_train_labels/` trong repo.
