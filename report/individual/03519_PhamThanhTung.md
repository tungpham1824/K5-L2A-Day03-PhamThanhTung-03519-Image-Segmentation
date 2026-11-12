# Báo cáo thực hành Day 3 — Segmentation

- Mã học viên theo lớp: 03519
- Ngày / CVAT local: …
- Công cụ đã dùng: SAM 2.1

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` (Segmentation mask 1.1) | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` (COCO 1.0) | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` (COCO 1.0) | 2 / 2 | 30 |
| cp1_holes | `submissions/cp1_holes.zip` (COCO 1.0) | 1 / 1 | 3 |
| cp2_slice | `submissions/cp2_slice.zip` (COCO 1.0) | 1 / 1 | 3 |
| cp5_occlusion | `submissions/cp5_occlusion.zip` (COCO 1.0) | 1 / 1 | 3 |
| cp3_thin | `submissions/cp3_thin.zip` (Segmentation mask 1.1) | 1 / 1 | 3 |
| cp4_curb | `submissions/cp4_curb.zip` (Segmentation mask 1.1) | 1 / 1 | 3 |
| cp6_coverage | `submissions/cp6_coverage.zip` (Segmentation mask 1.1) | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Số liệu từ dữ liệu CVAT đã Save:

- **easy_semantic** (job 2, 3 ảnh): ảnh 1 có 12 mask (road 9, vegetation 2, sky 1); ảnh 2 có 20 mask (đủ 5 class); ảnh 3 có 19 mask (đủ 5 class). Vùng đã tô phủ khoảng 97–98% diện tích mỗi ảnh.
- **medium_instance** (job 3, 3 ảnh): ảnh 1 có 22 object (person 13, motorcycle 4, car 3, truck 1, bus 1); ảnh 2 có 26 object (car 17, person 8, bus 1); ảnh 3 có 24 object (car 15, person 8, motorcycle 1). Mỗi vật là một polygon riêng.
- **hard_panoptic** (job 4, 2 ảnh): ảnh 1 có 30 mask (gồm stuff road, sidewalk, building, vegetation, sky và thing car 13, person 3, bicycle 2, traffic light 2, motorcycle 1); ảnh 2 có 37 mask (car 14, vegetation 7, building 5, truck 3, motorcycle 3, sidewalk 2, road 1, sky 1).
- Sáu checkpoint (`cp1_holes`, `cp2_slice`, `cp5_occlusion`, `cp3_thin`, `cp4_curb`, `cp6_coverage`): mỗi trạm 1 ảnh, đã Save, export và có đủ 6 ZIP trong `submissions/`.
- Tổng cộng có đủ 9 ZIP trong `submissions/`, không có task nào thiếu.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: `medium_instance`, ảnh `000000181542.jpg`, object `car` ở góc trên bên trái (khoảng x 1–123, y 127–201), sát mép ảnh.
- Class và quy tắc tôi dùng để chọn biên: gán `car` và chỉ vẽ **phần nhìn thấy** theo phiếu quy tắc; dừng mask ở chỗ xe bị mép ảnh cắt và ở ranh với `bus` nằm cạnh/chồng lên, không tự đoán phần bị che.
- Nếu dùng gợi ý sau đó: không dùng.
- Nếu không dùng gợi ý: không dùng gợi ý tự động. Một quyết định gán nhãn: vật bị mép ảnh cắt vẫn là một instance `car` riêng, không gộp vào `bus` cạnh nó dù hai bounding box chồng nhau.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `easy_semantic`, ảnh `7ee6d192-89e2408b.jpg`, vùng mặt đường ở giữa ảnh (phần road gồm nhiều mảnh nhỏ).
- Lỗi thuộc loại: phủ vùng (mặt đường bị chia thành nhiều mảnh nhỏ, có khoảng trống giữa các mảnh).
- Bằng chứng tôi nhìn thấy: ảnh này có 9 mảnh `road` rời nhau và tổng vùng đã tô chỉ phủ khoảng 98% ảnh; các mảnh nhỏ nằm rải rác quanh giữa ảnh nên dễ để lại khe hở trên mặt đường.
- Quy tắc và hành động sửa: theo bước tự kiểm “mask có bỏ sót vùng rõ ràng không”, tôi phóng to các khe hở giữa các mảnh road, tô nối lại để mặt đường liền mạch, rồi kiểm lại ranh road–sidewalk theo bó vỉa.
- Sau sửa đã Save và export lại chưa? Đã Save trong CVAT và export lại `easy_semantic.zip` (Segmentation mask 1.1).

Chưa có điểm tự đánh giá từ GitHub Actions hoặc script.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `easy_semantic`, ảnh `7ee6d192-89e2408b.jpg`: không có mask `sidewalk` và `building` | Ảnh thật sự không có vỉa hè/nhà, hoặc tôi bỏ sót vùng thuộc hai class này | Phiếu quy tắc: “không ép đoán cho đủ coverage”; ranh road–sidewalk theo chức năng và bó vỉa | Tôi chỉ vẽ phần nhìn thấy rõ và không bịa nhãn cho đủ 5 class. Xin coach xác nhận ảnh này có sidewalk/building cần tô không. |
| 2. `medium_instance`, ảnh `000000181542.jpg`: `car` và `bus` ở góc trái chồng lên nhau | Là hai instance riêng, hoặc một phần xe bị bus che | Phiếu quy tắc: vật bị che có thể có vùng nhìn thấy rời nhau nhưng vẫn là một instance; hai vật cùng lớp sát nhau là hai instance | Giữ hai instance riêng (`car` và `bus`), chỉ vẽ phần nhìn thấy của mỗi vật. |
| 3. `medium_instance`, ảnh `000000373353.jpg`: nhiều xe và người ở xa rất nhỏ (diện tích chỉ vài trăm pixel) | Vẽ từng vật nhỏ riêng, hoặc bỏ qua vì quá nhỏ | Phiếu quy tắc: phóng to kiểm nét mảnh và vật nhỏ; không gộp hai vật thành một | Vẽ từng vật nhỏ thành instance riêng (kể cả vật chỉ vài chục pixel), không gộp các xe đỗ sát nhau. Hỏi coach: vật dưới ngưỡng bao nhiêu pixel thì có thể bỏ qua? |