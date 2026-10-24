# Kiểm tra Gerber panel — 25/10/2026

## Kết luận

Chưa nên chốt sản xuất ngay. Gói xuất đủ lớp chính và file khoan; kiểm tra hình học không phát hiện đứt/chập các net có pad được đối chiếu. Cần xử lý khe solder mask quá hẹp tại bản GUARD, và xác nhận/gia cố khung panel với nhà sản xuất.

Chỉ đọc PCB/Gerber/ZIP, không sửa bộ file sản xuất.

## Đối tượng

- ZIP: `manufacturing/export-2026-10-25/Panel_100x100_Slots.zip`
- PCB: `Panel_100x100_Slots.PcbDoc`
- Các file trong ZIP khớp từng byte với file xuất rời cùng thư mục.
- Có GTL/GBL, GTS/GBS, GTO/GBO, profile GM, file NC Drill PTH và NPTH riêng. GBO và GBP không có hình học, phù hợp với mặt dưới không có chữ hoặc kem hàn.

## Các kiểm tra đạt

- Profile GM là một đường bao kín hình vuông **97 × 97 mm**, không phải 100 × 100 mm như tên file/chữ trên panel.
- Đối chiếu đủ 7 board con, 5 thiết kế nguồn.
- 32 lỗ PTH danh nghĩa Ø0,3 mm.
- 10 slot NPTH rộng 1,5 mm cho dây đeo.
- 29 slot NPTH rộng 2 mm để tách panel, có hai đầu tròn. Có đường chạy dao M15/G01/M16 thực sự trong file NC; không phải chỉ hình vẽ đồng.
- Các rãnh nằm trong outline; phần FR4 còn lại của panel vẫn liền nhau.
- Đối chiếu tâm các pad có net trong PCB nguồn với các miền đồng Gerber, nối lớp qua 32 lỗ PTH: không thấy pad mất đồng, net bị chia tách hoặc hai net có pad cùng chập vào một miền đồng.
- Khoảng cách giữa các đảo đồng sau trừ lỗ: nhỏ nhất khoảng 0,173 mm trên Top và 0,1686 mm trên Bottom.
- Sau bỏ các flash đồng trùng kích thước slot NPTH, đồng chức năng cách NPTH nhỏ nhất khoảng 0,925 mm ở Top và 0,750 mm ở Bottom. Không thấy đồng chức năng nằm ngoài panel.

## Cần xử lý trước khi chốt

### 1. Khe solder mask quá hẹp ở bản GUARD

Tại board GUARD hàng dưới, giữa panel, các khe giữa cửa sổ Top Solder của guard và cửa sổ pad lân cận nhỏ nhất **0,04265 mm**, hai chỗ khác khoảng **0,0714 mm**.

Tọa độ tính theo gốc dưới-trái của outline Gerber, đơn vị mm:

- Khe 0,04265 mm: gần (47,318; 22,186).
- Khe 0,0714 mm: gần (46,590; 22,564).
- Khe 0,0714 mm: gần (45,375; 22,564).

JLCPCB công bố solder-mask bridge tối thiểu 0,10 mm cho đồng 1 oz, màu xanh/đỏ/vàng/xanh dương/tím; 0,13 mm với đen/trắng. Các khe này nhỏ hơn giới hạn. Đây không phải chập đồng hiện tại, nhưng nhà sản xuất có thể bỏ dải mask mỏng, để hai vùng hở liền nhau và tăng rủi ro cầu thiếc gần guard.

Sửa trong PCB nguồn GUARD: điều chỉnh cục bộ cửa sổ mask của guard/pad để khe đạt ít nhất 0,10 mm với màu xanh, nên 0,15 mm nếu đủ chỗ. Không xóa đường đồng guard hoặc thay đổi net chỉ để hết lỗi mask. Với LDI, JLCPCB cho phép opening pad 1:1; có thể cân nhắc giảm expansion cục bộ về 0 thay vì giảm toàn bộ rule. Repour, DRC và xuất lại Gerber sau sửa.

### 2. Khung viền ngoài quá mảnh để mặc định coi là tooling rail chuẩn

Khoảng cách nhỏ nhất từ rãnh tách đến mép ngoài:

| Phía | Khung còn lại |
|---|---:|
| Trái | 2,10 mm |
| Phải | 1,90 mm |
| Trên | 1,65 mm |
| Dưới | 1,35 mm |

JLCPCB nêu tooling edge tối thiểu 3 mm, và 5 mm khi SMT tại JLCPCB. Không tự động kết luận nhà sản xuất từ chối PCB trần, nhưng cần shop xác nhận giữ được panel khi phay. Nên nới outline để khung ít nhất 3 mm; có thể bố trí lại outline thành 100 × 100 mm mà không dời các mạch/rãnh hiện tại, nếu chia phần tăng thêm thích hợp cho bốn cạnh. Không chỉ kéo outline về một phía rồi cho rằng mọi cạnh đã đủ rộng.

### 3. Làm rõ bản vẽ phay

File GM hiện chỉ có outline ngoài. Các rãnh nằm đầy đủ trong NC Drill NPTH, nhưng chưa có Gerber Mechanical 2 mô tả contour phay hợp nhất. JLCPCB hướng dẫn thể hiện NPTH slot ở mechanical layer.

Nên xuất thêm lớp contour rãnh để shop xem và gửi ảnh panel. Ghi rõ contour là biên rãnh, không phải đường tâm dao; các slot giao nhau phải được hợp nhất, không phay trùng hoặc tự đóng kín đường bao từng điện cực làm mất cầu giữ. Có thể giữ file NC NPTH là dữ liệu gia công chính nếu shop xác nhận đọc đúng.

### 4. Các điểm phụ

- Chữ trên panel ghi 100x100mm nhưng profile là 97x97mm: sửa chữ hoặc outline cho thống nhất.
- Silkscreen có một ít phần chồng cửa sổ mask (tổng diện tích khoảng 0,078 mm²); nên chuyển chữ/viền linh kiện ra khỏi vùng hở. Nhà máy thường cắt bỏ các phần chữ đó.
- Slot NPTH được tạo bằng pad nên có flash đồng cùng kích thước lỗ trên GTL/GBL. Đồng đó dự kiến bị phay hết; nhờ CAM loại bỏ/clear copper quanh NPTH, không giữ viền đồng mảnh do sai số phay.
- Gửi shop xác nhận đây là panel 7 board con/5 thiết kế khác nhau, phay slot NPTH và giữ cầu nối; không thể kết luận giá từ Gerber hoặc giá cơ bản 10×10 trên Shopee.

## Phạm vi và giới hạn

Kiểm tra bằng đọc nội dung Gerber/Excellon, dựng hình học và đối chiếu pad từ PCB đã lưu. Đây không phải chứng nhận của nhà máy, không thay thế DRC Altium mới nhất, kiểm tra schematic/netlist độc lập, mô phỏng ổn định op-amp hoặc thử nghiệm tín hiệu. Chưa tải file lên dịch vụ sản xuất và chưa đặt hàng.

Nguồn đối chiếu năng lực sản xuất: https://jlcpcb.com/capabilities/pcb-capabilities/ (đọc ngày 25/10/2026).

Ảnh chồng lớp: `gerber_overlay.png`. Màu vàng là đồng được mở mask; trắng là lỗ/rãnh và chữ Top. Bottom được nhìn từ Top, không lật gương.

Báo cáo ghi lại kiểm tra hình học của lần xuất ngày 25/10/2026; không phải kết quả DRC trực tiếp từ Altium.
