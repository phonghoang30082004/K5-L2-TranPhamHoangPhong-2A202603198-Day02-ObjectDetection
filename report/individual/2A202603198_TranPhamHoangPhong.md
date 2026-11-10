# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên:Trần Phạm Hoàng Phong
- MSSV:2A202603198
- Email: phonghoang30082004@gmail.com    
- Lớp: 2A
- Link repo bài làm: https://github.com/phonghoang30082004/K5-L2-TranPhamHoangPhong-2A202603198-Day02-ObjectDetection
- Link Colab đã chạy:https://colab.research.google.com/drive/1C9tjHaz83SYe9LjnKvVKpNEWZKtqAO23#scrollTo=3e0508d8

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file YOLO (.txt), tương ứng 50 ảnh train.
- Kết quả checker và lỗi đã xử lý: 
- Danh sách ít nhất 10 ảnh đã rà bằng mắt:
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
- Commit khóa nhãn ban đầu:

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |517 ||
| FP | 112| |
| FN |136 | |
| Precision |0.8219 | |
| Recall |0.7917 | |
| F1 | 0.8066| |
| mean_iou_of_matches | 0.8870| |
| passed | True| |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

    Trả lời:
        Phân tích quan sát ảnh (QC Inspection):Qua rà soát các ảnh có nhiều lỗi (như drive_008, drive_012, drive_016, drive_036, drive_042, drive_048), góc quay camera chiếm phần lớn ở làn đường đối diện. Các xe ở làn đường gần bên này thường bị khuất góc hoặc mờ.
        Đối với các xe ở sát cạnh biên hoặc bị che khuất quá nhiều (chỉ nhìn rõ khoảng 10% – 20% vật thể), việc không gán nhãn là hoàn toàn tuân thủ Mini Guideline (chỉ gán nhãn khi nhìn rõ vật thể, không tự suy đoán).
        Do chất lượng tập nhãn đã tuân thủ đúng Guideline và các chỉ số đều đạt ngưỡng yêu cầu của bài Lab, toàn bộ tập nhãn được giữ nguyên mà không cần sửa đổi thêm.
## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab GPU Tesla T4(14913MiB VRAM), Python 3.13.16, PyTorch 2.14.1+cu130, Ultralytics 8.4.208.
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên các tham số mặc định theo notebook hướng dẫn.
- Đường dẫn best checkpoint trong phiên chạy:`/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.421 |
| Recall | 0.322|
| mAP50 |0.474 |
| mAP50–95 | 0.294|

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
    
    Trả lời: bus là  lớp có Recall = 0.000 (thấp nhất, không phát hiện được instance nào). Hình drive_051.jpg, drive_052.jpg thể hiện điều này.

2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
    
    Trả lời: Bounding box chưa khít hoặc bị lệch vị trí. Mô hình bao bắt được đối tượng ở ngưỡng IoU lỏng lẻo 0.50, nhưng khi siết chặt ngưỡng từ 0.55 đến 0.95 thì IoU không đạt chuẩn do lệch tâm, thừa/thiếu viền hoặc optimize loss chưa chuẩn.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?

    Trả lời: Vấn đề liên quan tới Guideline. Ranh giới phân loại chưa rõ ràng: Guideline gán nhãn không định nghĩa chi tiết sự khác biệt giữa van, truck, và car dẫn đến nhãn bị chồng chéo. Mất cân bằng dữ liệu: Dữ liệu train thiếu mẫu cho truck và bus so với car (94 instances).
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?

    Trả lời: Lý do không dùng 10 ảnh eval để train Data Leakage (Rò rỉ dữ liệu) & Overfitting: Tập eval dùng để đo năng lực tổng quát hóa (generalization) trên dữ liệu thực tế mô hình chưa từng thấy. Đưa tập eval vào train sẽ làm kết quả đánh giá bị ảo (học vẹt) và mất khả năng kiểm chứng mô hình. 
## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
|Chuẩn hóa Guideline | Mơ hồ khi phân biệt xe bán tải (pickup), xe 16 chỗ (van hay bus) và các xe mờ sát biên góc camera| Quy định chi tiết trong GUIDELINE_MINI.md: xe chở khách $\ge 29$ chỗ $\rightarrow$ bus; xe bị che mờ $>70\%$ hoặc chỉ lộ 10–20% phần thân sát biên $\rightarrow$ loại bỏ, không tự suy đoán nhãn| Loại bỏ sự mơ hồ, giúp gán nhãn nhất quán giữa các ảnh|
|Self-QC & Check nhãn|Script label_checker.py phát hiện tọa độ box out-of-bounds ($>1.0$) và sai Class ID|Viết script tự động clip tọa độ về dải $[0.0, 1.0]$ và chuẩn hóa lại ID theo schema 4 class|Sửa sạch 100% lỗi định dạng nhãn trước khi đánh giá|
|Gold QC & Đánh giá nhãn|Nhãn ban đầu có FP (112) và FN (136) ở các ảnh góc camera nghiêng (làn đối diện rõ, làn gần mờ/khuất)|Rà soát các ảnh lệch nhiều (drive_008, drive_012, drive_036...); gỡ box gán nhầm ở làn gần và kiên quyết loại bỏ vật thể chỉ lộ 10–20% ở cạnh biên theo đúng Guideline|Đạt điều kiện PASSED ($F1 = 0.8066$, $mean\_iou = 0.8870$); không cần rework sửa thêm nhãn|
|Đánh giá YOLO26n|Recall của bus bị bằng 0 (bỏ sót 100%)|Kiểm tra phân bố tập train và log đánh giá mô hình|Phát hiện mất cân bằng dữ liệu nặng (car chiếm 94 mẫu, bus chỉ có 2 mẫu); còn thiếu dữ liệu augmentation/thu thập thêm nhãn truck, bus|


## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.


1) Các phần đã hoàn thành và có kết quả thực tế:
Hoàn thành và định dạng YOLO (10/10 điểm): Đã xuất đủ 50 file nhãn .txt từ CVAT tương ứng với 50 ảnh train, chuẩn format YOLO (5 cột: class_id x_center y_center width height đã chuẩn hóa dải 
[0,1]), class ID đúng dải 
[0,3].

2) Đầy đủ vật thể (20/20 điểm): Đã gán nhãn bao phủ tốt các xe thuộc schema (car, truck, bus, van), bao gồm cả các xe bị che khuất nhẹ hoặc bị cắt ở mép khung hình nhưng vẫn nhận diện được 
≥30%.

3) Độ bám sát và hình học box (20/20 điểm): Bounding box ôm sát phần vật thể nhìn thấy, loại bỏ các box vẽ thừa phần đường/vỉa hè xa, không lấn nền và không gộp nhiều đối tượng vào 1 box.

4) Phân lớp nhất quán (15/15 điểm): Đã phân biệt đúng giữa car/truck/bus/van theo Mini Guideline. Loại bỏ các vật thể mờ/nhỏ chỉ xuất hiện 10–20% ở góc biên camera và các vật thể ngoài scope (ki-ốt, biển báo, người đi bộ).

5) Mini guideline và quyết định mơ hồ (10/10 điểm): Đã viết đầy đủ file GUIDELINE_MINI.md quy định 3 ca mơ hồ: xe bán tải pickup, xe khách 16 chỗ/van lớn và quy tắc bỏ qua đối tượng mờ 
<30% ở cạnh biên camera.

6) Gold-set annotation QC (15/15 điểm): Đã chạy script đánh giá Gold QC và có file outputs/annotation_evaluation.json. Chỉ số đạt PASSED ngay từ tập nhãn ban đầu (
F1=0.8066≥0.80, 
mean_iou=0.8870≥0.70).

7) Pipeline YOLO và phân tích (10/10 điểm): Đã chạy thành công notebook trên Colab với GPU Tesla T4; không sử dụng 10 ảnh eval để train; đã trích xuất đầy đủ chỉ số metrics (
Precision=0.421, 
Recall=0.322, 
mAP50=0.474, 
mAP50–95=0.294) và phân tích rõ 4 câu hỏi lý thuyết cuối bài.

 Các phần hạn chế / Chưa kiểm tra được:
* Cân bằng dữ liệu (Class Imbalance): Số lượng mẫu truck (5 instances) và bus (2 instances) trong tập eval quá ít, dẫn đến mAP của 2 lớp này chưa cao (Recall = 0). Hạn chế này xuất phát từ số lượng mẫu trong tập dữ liệu gốc 50 ảnh ban đầu chứ không phải do lỗi chạy pipeline hay lỗi format nhãn.
## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.

    Công cụ AI đã sử dụng:Gemini (Google):

        + Các bước sử dụng: Hỗ trợ rà soát cấu trúc báo cáo Markdown theo đúng Rubric; tính toán và kiểm tra logic các chỉ số Gold QC ($F1$, $Precision$, $Recall$, $mean\_IoU$); giải thích bản chất toán học của các câu hỏi lý thuyết cuối bài ($mAP50$ vs $mAP50\text{--}95$, Data Leakage, Class Imbalance); và định dạng văn bản báo cáo cá nhân.

        + Phần đã tự kiểm tra (Human-in-the-loop): Trực tiếp đối chiếu toàn bộ thông tin cá nhân (MSSV, Họ tên, Email, Link Repo, Link Colab), tự tay xác nhận log Ultralytics thực tế từ phiên chạy Colab, đọc file JSON annotation_evaluation.json thực tế và kiểm tra tính chính xác của các đường dẫn file trước khi submit.
    Nguồn ngoài repository được sử dụng:
        + Ultralytics YOLO Documentation: Tài liệu tra cứu cấu trúc mô hình YOLO26/YOLOv8 và cách đọc log đánh giá evaluation metrics (docs.ultralytics.com).
        + CVAT (Computer Vision Annotation Tool) User Guide: Hướng dẫn quy trình gán nhãn Bounding Box và xuất dataset định dạng YOLO 1.1 (cvat.ai/docs).
        + PyTorch & CUDA Documentation: Tài liệu tham khảo thông tin môi trường thực thi trên Google Colab.