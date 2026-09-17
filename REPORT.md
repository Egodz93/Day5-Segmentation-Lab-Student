# Báo cáo Day 5

- Mã học viên theo lớp: Phạm Văn Thân - 2A202602322
- Ngày / CVAT local: 17/09/2026
- Công cụ đã dùng: Polygon, Brush, OpenCV

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh `000000181542.jpg`, người ngồi trên xe máy ở phía trái ảnh (object `person`, annotation ID 1 trong bản COCO đã export).
- Class và quy tắc tôi dùng để chọn biên: class `person`, tôi bám theo phần cơ thể còn nhìn thấy và dừng mask tại đường viền tiếp xúc với xe máy, không vẽ xuyên qua phần bị xe che.
- Nếu dùng gợi ý sau đó: tôi dùng OpenCV sau bước tự vẽ, kiểm lại vùng sát xe máy và nền, chỉ giữ phần bám đúng cơ thể người, sửa hoặc bỏ phần tràn sang xe và nền vì task instance chỉ gán phần nhìn thấy của từng vật.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `hard_panoptic`, toàn bộ hai ảnh `000000350023.jpg` và `000000460147.jpg`.
- Lỗi thuộc loại: khác — sai định dạng export.
- Bằng chứng tôi nhìn thấy: bản ZIP ban đầu chỉ chứa `annotations.xml`; lệnh kiểm cấu trúc báo: “cần đúng một COCO JSON trong annotations/”.
- Quy tắc và hành động sửa: task panoptic phải export bằng **COCO 1.0**. Tôi đã export lại trực tiếp từ job CVAT và thay file `hard_panoptic.zip`, không sửa tay XML/JSON trong file nộp.
- Sau sửa đã Save và export lại chưa? Đã Save 2/2 ảnh và export lại. ZIP mới chứa `annotations/instances_default.json` với 89 mask dạng RLE.

Kết quả tự kiểm liên quan: `[OK] hard_panoptic: hard_panoptic.zip`, 89 annotation dạng RLE. Đây là kiểm tra cấu trúc, chưa có metric hoặc điểm tự đánh giá, và tôi vẫn cần kiểm trực quan độ phủ/chồng lấn trong CVAT.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp1_holes` — kính chắn gió và các khe trong bánh xe máy lớn ở giữa ảnh | Khoét các vùng nhìn xuyên qua khỏi mask, hoặc giữ chúng bên trong cùng mask `motorcycle` | Quy tắc của task ghi kính/khe nằm trong mask, không tự khoét lỗ; bản COCO có một mask `motorcycle` lớn bao quanh xe | Giữ các khe đó trong cùng instance xe máy, không tách thành object khác và không khoét lỗ tùy ý |
| `cp2_slice` — hai ô tô màu trắng sát nhau ở giữa-phải ảnh | Gộp thành một mask `car`, hoặc tách thành hai instance | Có hai thân xe và hai đường viền vật lý riêng; bản COCO đã xuất có hai mask `car` riêng ở vùng này (annotation ID 4 và 5) | Tách thành hai instance `car` dù chúng ở sát nhau |
| `cp4_curb` — ranh giữa mặt nhựa bên trái và dải bê tông nâng cao bên phải ở tiền cảnh | Coi cả hai là `road` do màu gần giống nhau, hoặc tách phần nâng cao thành `sidewalk` | Bó vỉa và cao độ/chức năng đi bộ là dấu hiệu chính, không chỉ dựa vào màu bề mặt | Gán mặt nhựa bên trái là `road`, phần bê tông nâng cao bên phải là `sidewalk`, và đặt biên theo mép bó vỉa |
