================================================================
           HƯỚNG DẪN SỬ DỤNG VÒNG QUAY TRÚNG THƯỞNG
================================================================

----------------------------------------------------------------
1. THIẾT LẬP GIẢI THƯỞNG (Màn hình Setup)
----------------------------------------------------------------

Nhập danh sách giải thưởng vào ô văn bản theo định dạng:

    tên giải|số lượng|trọng số|mã màu

Mỗi giải một dòng. Ví dụ:

    Giải Nhất|1|1|#FFD700
    Áo thun|10|3|#FF5733
    Voucher 100k|5|2|#3498DB
    Tham gia lần sau|20||#AAAAAA

Giải thích từng trường:
  - Tên giải   : Tên hiển thị trên vòng quay (bắt buộc)
  - Số lượng   : Tổng số lần giải đó có thể trúng (bắt buộc)
  - Trọng số   : Mức độ ưu tiên khi quay — số càng lớn càng dễ
                 trúng. Để trống (dùng ||) thì mặc định là 1.
  - Mã màu     : Màu ô trên vòng quay. Chấp nhận các định dạng:
                   #FF0000  (hex)
                   red      (tên màu tiếng Anh)
                   rgb(255,0,0)
                   hsl(0,100%,50%)
                 Để trống thì tự động chọn màu.

Xác suất trúng của mỗi giải được tính theo công thức:
    Xác suất = (Số lượng còn lại × Trọng số) / Tổng tất cả giải

----------------------------------------------------------------
2. CÁC NÚT TRONG MÀN HÌNH SETUP
----------------------------------------------------------------

  [ Cập nhật giải ]
      Áp dụng danh sách vừa nhập/chỉnh sửa lên vòng quay.
      Dùng khi muốn xem trước hoặc sửa lại giải trước khi quay.

  [ Hiển thị giải thưởng hết hàng ] (checkbox)
      Khi bật: các giải đã hết (số lượng = 0) vẫn hiển thị trên
      vòng quay nhưng KHÔNG được chọn khi quay.
      Khi tắt (mặc định): giải hết sẽ biến mất khỏi vòng quay.

  [ Chọn ảnh nền ]
      Tải ảnh từ thiết bị để đặt làm hình nền trang web.

  [ Chế độ: PC / Mobile ]
      PC     : Tất cả nút chức năng luôn hiển thị.
      Mobile : Xuất hiện thêm nút 👁️ để ẩn/hiện nhóm nút
               chức năng — giúp giao diện gọn hơn khi trình diễn.

  [ Ready ]
      Chuyển sang màn hình quay. Vòng quay sẽ được vẽ lại theo
      danh sách giải đã nhập.

----------------------------------------------------------------
3. CÁC NÚT TRONG MÀN HÌNH QUAY
----------------------------------------------------------------

  [ Quay ] (nút ở giữa vòng quay)
      Bắt đầu quay. Không thể nhấn khi vòng quay đang chạy.
      Phím tắt: nhấn SPACE (khi không đang focus vào ô nhập liệu).

  [ History ]
      - Tải xuống file CSV chứa toàn bộ lịch sử trúng thưởng.
      - Đồng thời hiện thông báo thống kê: số lượng đã dùng,
        còn lại và tỉ lệ trúng hiện tại của từng giải.

  [ Xóa lịch sử ]
      Xóa toàn bộ lịch sử quay (có hỏi xác nhận trước khi xóa).

  [ Setup ]
      Quay trở lại màn hình Setup để chỉnh sửa giải thưởng.
      * Tự động cập nhật số lượng còn lại vào ô nhập liệu
        (xem chi tiết ở mục 5).

  [ 👁️ ] (chỉ có ở chế độ Mobile)
      Ẩn/hiện nhóm nút History, Xóa lịch sử, Setup.
      Phím tắt: F1.

----------------------------------------------------------------
4. PHÍM TẮT
----------------------------------------------------------------

  SPACE   Quay vòng quay (khi không focus vào ô nhập liệu)
  F1      Ẩn/hiện nhóm nút chức năng (chế độ Mobile)

  Các phím sau bị vô hiệu hóa để bảo vệ tool:
  F12     (mở DevTools trình duyệt)
  Ctrl+S  (lưu trang)
  Chuột phải bị chặn.

----------------------------------------------------------------
5. TÍNH NĂNG ĐẶC BIỆT
----------------------------------------------------------------

  A. GHI NHỚ PHIÊN LÀM VIỆC (Tự động phục hồi khi tải lại trang)
  -------------------------------------------------------------------
  Tool tự động lưu toàn bộ trạng thái hiện tại vào bộ nhớ trình
  duyệt sau mỗi lần quay và mỗi khi chuyển màn hình.

  Dữ liệu được lưu bao gồm:
    - Danh sách giải và số lượng còn lại
    - Góc hiện tại của vòng quay
    - Màn hình đang mở (Setup hay Play)
    - Chế độ PC/Mobile
    - Trạng thái checkbox "Hiển thị giải hết hàng"
    - Lịch sử các lần trúng thưởng

  Khi trình duyệt bị lỗi, tắt ngoài ý muốn hoặc tải lại trang,
  tool sẽ tự động mở lại đúng màn hình đang dùng với đúng số
  lượng giải còn lại — không cần setup lại từ đầu.

  B. TỰ ĐỘNG CẬP NHẬT SỐ LƯỢNG KHI NHẤN NÚT SETUP
  -------------------------------------------------------------------
  Khi đang quay và nhấn nút [ Setup ] để quay về màn hình cài đặt,
  ô nhập liệu sẽ tự động hiển thị SỐ LƯỢNG CÒN LẠI (sau các lần
  đã quay), thay vì số lượng ban đầu.

  Ví dụ: Ban đầu nhập "Áo thun|10|1|#FF0000", sau 3 lần quay
  trúng áo thun, khi nhấn Setup sẽ thấy "Áo thun|7|1|#FF0000".

  Điều này cho phép:
    - Tiếp tục buổi quay vào hôm sau mà không cần đếm lại
    - Điều chỉnh giải giữa chừng dựa trên số lượng thực tế còn lại

  C. CẢNH BÁO KHI THOÁT
  -------------------------------------------------------------------
  Nếu có lịch sử quay, trình duyệt sẽ hiện hộp thoại xác nhận
  khi bạn cố tắt tab hoặc điều hướng ra khỏi trang — tránh mất
  dữ liệu ngoài ý muốn.

----------------------------------------------------------------
6. LƯU Ý QUAN TRỌNG
----------------------------------------------------------------

  - File CSV lịch sử được lưu với encoding UTF-8 (có BOM) để hiển
    thị đúng tiếng Việt khi mở bằng Excel.

  - Khi tất cả giải đã hết (số lượng = 0), nút Quay sẽ thông báo
    "Không còn giải thưởng nào để quay!" và ngừng hoạt động.

  - Dữ liệu lưu trong bộ nhớ trình duyệt (localStorage). Nếu xóa
    dữ liệu trình duyệt hoặc dùng chế độ ẩn danh (Incognito),
    trạng thái sẽ không được lưu giữa các phiên.

  - Tool hoạt động hoàn toàn offline sau lần tải trang đầu tiên
    (ngoại trừ hiệu ứng confetti cần kết nối internet).

================================================================
