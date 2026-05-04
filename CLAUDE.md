# Dự án: Vòng Quay Trúng Thưởng (LuckeyWheel – Mobile_PC)

## Tổng quan

Ứng dụng web vòng quay may mắn dùng cho sự kiện bốc thăm/trao giải trực tiếp. Người dùng nhập danh sách giải thưởng kèm số lượng, trọng số và màu sắc, sau đó quay để chọn ngẫu nhiên có trọng số. Kết quả được lưu vào localStorage và có thể xuất CSV. Được triển khai lên GitHub Pages với domain tùy chỉnh `A80.luckydraw.site`.

## Công nghệ sử dụng

- **HTML/CSS/JavaScript thuần** – không framework, không build tool, không package manager
- **SVG** – vẽ bánh xe quay trực tiếp bằng `<path>` và `<text>` trong SVG
- **canvas-confetti** – hiệu ứng bắn confetti khi trúng thưởng (dùng CDN + bản local `confetti.browser.min.js`)
- **localStorage** – lưu lịch sử quay dạng CSV
- **GitHub Pages** – hosting với CNAME tùy chỉnh

## Cấu trúc thư mục

```
Mobile_PC/
├── index.html              # Toàn bộ ứng dụng: HTML + CSS + JS trong 1 file
├── confetti.browser.min.js # Thư viện canvas-confetti (bản local dự phòng)
├── wheel_spin.mp3          # Âm thanh phát khi vòng quay đang quay
└── CNAME                   # Domain tùy chỉnh GitHub Pages: A80.luckydraw.site
```

## Kiến trúc & Luồng dữ liệu

### Hai màn hình chính (toggle bằng `display: none/flex`)
1. **Setup panel** (`#setup`): Nhập giải, chọn ảnh nền, chọn chế độ PC/Mobile → nhấn "Ready"
2. **Play panel** (`#play`): Vòng quay SVG + nút Quay + lịch sử + nút điều khiển

### Luồng quay thưởng
```
Nhập text → updateWheel() → prizes[] (parse)
                                  ↓
Nhấn Quay → pickPrizeIndexByCount() → chọn ngẫu nhiên có trọng số (count × weight)
                                  ↓
           spinWheel() → tính góc quay → CSS transition xoay wheel
                                  ↓
           setTimeout(durationMs+100) → prize.count-- → drawWheel() → confetti + lịch sử
```

### Cơ chế trọng số
- Xác suất trúng = `(count × weight) / tổng(count × weight)` của tất cả giải còn hàng
- Sau mỗi lần quay, `count` giảm 1; giải hết hàng (`count === 0`) bị loại khỏi pool

### Lưu trữ
- Lịch sử lưu `localStorage` key `"lich_su_trung_thuong_csv"` dạng CSV (UTF-8 BOM khi xuất)
- Phục hồi lịch sử khi load lại trang (`restoreHistoryFromLocalStorage`)

## Các file quan trọng

| File | Chức năng |
|------|-----------|
| `index.html:1–190` | HTML + CSS toàn bộ layout, 2 panel, style responsive |
| `index.html:245–258` | Biến global: `prizes[]`, `lastRotation`, `spinning`, `historyData` |
| `index.html:295–350` | `updateWheel()` + `drawWheel()` – parse input và vẽ SVG bánh xe |
| `index.html:352–378` | `pickPrizeIndexByCount()` – thuật toán chọn ngẫu nhiên có trọng số |
| `index.html:380–458` | `spinWheel()` – xử lý animation quay và kết quả |
| `index.html:506–613` | Event listeners + bảo vệ chặn F12/Ctrl+S/chuột phải |
| `wheel_spin.mp3` | Âm thanh quay – phát khi spin bắt đầu, dừng khi kết thúc |
| `CNAME` | `A80.luckydraw.site` – domain GitHub Pages |

## Cài đặt & Chạy project

**Không cần cài đặt gì.** Mở `index.html` trực tiếp trên trình duyệt là chạy được.

Để deploy:
- Push lên GitHub, bật GitHub Pages từ branch `main`
- File `CNAME` tự động cấu hình domain `A80.luckydraw.site`

## Quy tắc phát triển

- **Toàn bộ code trong 1 file `index.html`** – CSS trong `<style>`, JS trong `<script>`, không tách file (trừ asset media)
- **Không dùng framework hay bundler** – giữ pure JS/HTML/CSS để deploy đơn giản
- **Biến global** cho state app: `prizes`, `lastRotation`, `spinning`, `historyData`, `showZeroQuantityPrizes`, `showControlButtons`
- **Màu sắc giải**: ưu tiên `customColor` từ input, fallback về `hsl((i×360)/n, 70%, 50%)`
- **Responsive**: wheel dùng `85vw` max `420px`, container max `500px`, layout flex column

### Format nhập giải thưởng
```
tên giải|số lượng|trọng số|mã màu
Ví dụ: Giải A|3|5|#FF0000
```
- `trọng số` mặc định = 1 nếu bỏ trống (dùng `||` để skip)
- `mã màu`: hỗ trợ hex, named color, `rgb()`, `hsl()`

### Phím tắt
| Phím | Chức năng |
|------|-----------|
| `Space` | Quay (khi không focus vào input) |
| `F1` | Ẩn/hiện nút điều khiển (Mobile mode) |
| `F12` | Bị chặn (DevTools) |
| `Ctrl+S` | Bị chặn (Save) |

### Chế độ PC vs Mobile
- **Mobile**: hiện nút `👁️` (toggle-controls-btn) để ẩn/hiện nhóm nút chức năng
- **PC**: ẩn nút `👁️`, nhóm nút chức năng luôn hiển thị

## Trạng thái hiện tại

**Đang hoạt động tốt:**
- Vòng quay SVG với trọng số theo `count × weight`
- Lưu/khôi phục lịch sử qua localStorage
- Xuất CSV có BOM (đúng encoding tiếng Việt)
- Báo cáo thống kê giải (đã dùng, còn lại, tỉ lệ %) khi bấm History
- Hiệu ứng confetti, âm thanh quay
- Chế độ PC/Mobile
- Chặn F12, Ctrl+S, chuột phải
- Cảnh báo trước khi thoát nếu có lịch sử

**Lưu ý:**
- Chỉ hiển thị lần trúng cuối cùng trong `#history-list` (không hiển thị toàn bộ, chỉ lưu đầy đủ trong `historyData[]` và localStorage)
- Confetti load từ CDN (`cdn.jsdelivr.net`), cần internet; file local `confetti.browser.min.js` tồn tại nhưng không được load trực tiếp trong HTML hiện tại

## Thay đổi gần nhất

| Commit | Nội dung |
|--------|----------|
| `cdf491b` | Cập nhật CNAME sang domain A80 (A80.luckydraw.site) |
| `80395ec` | Bật lại chặn F12 và chuột phải (contextmenu + keydown) |
| `9bd32fc` | Thêm nút bật/tắt fullscreen + tích hợp âm thanh wheel_spin.mp3 |
| `96341ea` | Xóa tính năng/code (không rõ cụ thể) |
| `ccdf547` | Thêm chức năng fullscreen lần đầu |
| `a8a4bc9` | Bỏ chặn F12 và chuột phải (giai đoạn tạm thời) |

## TODO / Việc cần làm tiếp

- [ ] Xem xét load `confetti.browser.min.js` local thay vì CDN (hiện tại file có sẵn nhưng HTML vẫn dùng CDN)
- [ ] Hiển thị toàn bộ lịch sử trong `#history-list` thay vì chỉ mục cuối
- [ ] Tính năng fullscreen đã được thêm rồi xóa – xem xét có cần thêm lại không
- [ ] Cho phép lưu/load cấu hình giải thưởng (localStorage hoặc file JSON)
- [ ] Thêm animation đếm ngược hoặc hiệu ứng highlight ô trúng

## Ghi chú quan trọng cho Claude

### Quy tắc nghiệp vụ
- Mỗi lần quay **bắt buộc** giảm `count` của giải trúng đúng 1 đơn vị – đây là cơ chế kiểm soát số lượng giải
- Thuật toán chọn giải dùng `count × weight` (không phải chỉ `weight`) – ý nghĩa: giải nhiều phần thưởng hơn có cơ hội trúng cao hơn theo tỉ lệ số lượng × trọng số
- Khi `showZeroQuantityPrizes = true`, vòng quay vẫn vẽ cả giải hết nhưng `pickPrizeIndexByCount` **chỉ chọn giải còn hàng**

### Những điều cần tránh
- **Không tách code thành nhiều file** – thiết kế hiện tại là single-file để deploy GitHub Pages đơn giản
- **Không thêm build step, npm, bundler** – giữ nguyên pure HTML/JS
- **Không xóa/bypass các block F12 và chuột phải** – đây là yêu cầu bảo mật của chủ dự án
- **Không dùng `<!—- comment -->` quá nhiều** trong code

### Cách tiếp cận ưu tiên
- Khi thêm tính năng mới: thêm CSS vào `<style>`, HTML vào đúng panel, JS vào `<script>` – giữ theo thứ tự này
- Tính năng liên quan đến giải thưởng: luôn kiểm tra cả 2 trường hợp `showZeroQuantityPrizes = true/false`
- Khi sửa animation quay: chú ý `lastRotation` là tích lũy (không reset về 0) để tránh wheel giật ngược

### Nguồn dữ liệu
- Toàn bộ state nằm trong RAM (biến global JS) + localStorage key `"lich_su_trung_thuong_csv"`
- Không có backend, không có API, không có database
- Khi tải lại trang: lịch sử được restore từ localStorage, **nhưng cấu hình giải thưởng bị mất** (phải nhập lại)
