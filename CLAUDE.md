# Dự án: Vòng Quay Trúng Thưởng (LuckeyWheel – Mobile_PC)

## Tổng quan

Ứng dụng web vòng quay may mắn dùng cho sự kiện bốc thăm/trao giải trực tiếp. Người dùng nhập danh sách giải thưởng kèm số lượng, trọng số và màu sắc, sau đó quay để chọn ngẫu nhiên có trọng số. Kết quả được lưu vào localStorage và có thể xuất CSV. Được triển khai lên GitHub Pages (repo `thamtunhut/luckywheelver2`) với domain tùy chỉnh trong file `CNAME` (hiện là `luckywheel.vinhhp2.site`).

## Công nghệ sử dụng

- **HTML/CSS/JavaScript thuần** – không framework, không build tool, không package manager
- **SVG** – vẽ bánh xe quay trực tiếp bằng `<path>` và `<text>` trong SVG; mũi tên chỉ giải cũng là SVG
- **canvas-confetti** – hiệu ứng bắn confetti khi trúng thưởng, load từ file local `confetti.browser.min.js` (không còn dùng CDN)
- **localStorage** – lưu lịch sử quay (CSV) và toàn bộ trạng thái app (`app_state`)
- **GitHub Pages** – hosting với CNAME tùy chỉnh

## Cấu trúc thư mục

```
(repo root)
├── index.html              # Toàn bộ ứng dụng: HTML + CSS + JS trong 1 file
├── confetti.browser.min.js # Thư viện canvas-confetti (load trực tiếp bằng <script src>)
├── wheel_spin.mp3          # Âm thanh phát khi vòng quay đang quay
├── ReadMe.txt              # Hướng dẫn sử dụng cho người dùng cuối (tiếng Việt)
├── CNAME                   # Domain tùy chỉnh GitHub Pages (hiện: luckywheel.vinhhp2.site)
├── Guide/                  # (chưa commit) 4 ảnh minh họa hướng dẫn
└── index-test.html         # (chưa commit) bản thử nghiệm, không phải bản deploy
```

## Kiến trúc & Luồng dữ liệu

### Hai màn hình chính (toggle bằng `display: none/flex`)
1. **Setup panel** (`#setup`): Nhập giải, chọn ảnh nền, chọn chế độ PC/Mobile → nhấn "Ready"
2. **Play panel** (`#play`): Mũi tên + vòng quay SVG + nút Quay + banner kết quả + lịch sử + nút điều khiển

### Luồng quay thưởng
```
Nhập text → updateWheel() → prizes[] (parse) → drawWheel() (vẽ ô + chữ + lưu wheelSliceColors)
                                  ↓
Nhấn Quay / phím bất kỳ → pickPrizeIndexByCount() → chọn ngẫu nhiên có trọng số (count × weight)
                                  ↓
           spinWheel() → tính góc quay → CSS transition xoay #wheel
                       → trackArrowColorWhileSpinning() (mỗi frame: màu mũi tên = màu ô đang nằm dưới)
                                  ↓
           setTimeout(durationMs+100) → prize.count-- → drawWheel() → confetti + lịch sử + saveAppState()
```

### Cơ chế trọng số
- Xác suất trúng = `(count × weight) / tổng(count × weight)` của tất cả giải còn hàng
- Sau mỗi lần quay, `count` giảm 1; giải hết hàng (`count === 0`) bị loại khỏi pool

### Lưu trữ
- Lịch sử: `localStorage` key `"lich_su_trung_thuong_csv"` dạng CSV (UTF-8 BOM khi xuất), phục hồi bằng `restoreHistoryFromLocalStorage`
- Trạng thái app: `localStorage` key `"app_state"` (JSON) do `saveAppState()` ghi sau mỗi lần quay và mỗi lần đổi màn hình; `restoreAppState()` đọc lại lúc `DOMContentLoaded`. Gồm: màn hình đang mở, nội dung ô nhập giải, `prizes` (kèm số lượng còn lại), `lastRotation`, chế độ PC/Mobile, `showZeroQuantityPrizes`, `showControlButtons`
- Bấm "Setup" (`backToSetup`) sẽ ghi số lượng **còn lại** vào ô nhập giải (`prizesToInputText`)

## Các hàm quan trọng trong `index.html`

Số dòng thay đổi liên tục, tra bằng tên hàm.

| Hàm / khối | Chức năng |
|------------|-----------|
| `<style>` | CSS toàn bộ layout; `.arrow-top`, `.wheel`, `.result-slot`/`.result`, `#history-list` |
| `saveAppState` / `restoreAppState` | Lưu/khôi phục toàn bộ trạng thái (key `app_state`) |
| `updateWheel` | Parse ô nhập giải → `prizes[]` → `drawWheel()` |
| `fitLabelToSlice` / `measureLabel10` | Tính cỡ chữ (3–12) cho từng ô theo độ dài chữ và bề rộng ô; quá dài thì cắt "…" |
| `drawWheel` | Vẽ SVG: mỗi ô là `<path>` + `<text>` xoay theo bán kính; ghi `wheelSliceColors` |
| `updateArrowColor` / `getWheelRotation` / `trackArrowColorWhileSpinning` | Màu mũi tên bám theo ô đang nằm dưới nó (đọc góc thật từ `getComputedStyle(#wheel).transform`) |
| `warmUpWheel` / `warmUpAudio` | Làm nóng layer transform và decode âm thanh khi vào màn Play |
| `pickPrizeIndexByCount` | Thuật toán chọn ngẫu nhiên có trọng số |
| `spinWheel` | Animation quay và xử lý kết quả |
| `startGame` / `backToSetup` / `toggleControlButtons` | Chuyển màn hình, ẩn/hiện nút |
| Khối cuối `<script>` | Chặn chuột phải/F12/Ctrl+S, phím tắt, cảnh báo `beforeunload` |

## Cài đặt & Chạy project

**Không cần cài đặt gì.** Mở `index.html` trực tiếp trên trình duyệt là chạy được.

Để deploy:
- Push lên GitHub, bật GitHub Pages từ branch `main`
- File `CNAME` tự động cấu hình domain (hiện `luckywheel.vinhhp2.site`)

## Quy tắc phát triển

- **Toàn bộ code trong 1 file `index.html`** – CSS trong `<style>`, JS trong `<script>`, không tách file (trừ asset media)
- **Không dùng framework hay bundler** – giữ pure JS/HTML/CSS để deploy đơn giản
- **Biến global** cho state app: `prizes`, `lastRotation`, `spinning`, `historyData`, `showZeroQuantityPrizes`, `showControlButtons`, `wheelSliceColors`
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
| **Mọi phím thường** (chữ, số, `Space`, `Enter`…) | Quay (chỉ khi đang ở màn Play và không focus vào input/textarea/button) |
| `F1` | Ẩn/hiện nút điều khiển (Mobile mode) |
| `F1`–`F12`, `Esc`, `Tab`, mũi tên, `Home/End/PageUp/PageDown`, `Backspace/Delete/Insert`, phím modifier | **Không** kích hoạt quay |
| Tổ hợp có `Ctrl`/`Alt`/`Meta` | **Không** kích hoạt quay |
| `F12` | Bị chặn (DevTools) |
| `Ctrl+S` | Bị chặn (Save) |

### Chế độ PC vs Mobile
- **Mobile**: hiện nút `👁️` (toggle-controls-btn) để ẩn/hiện nhóm nút chức năng
- **PC**: ẩn nút `👁️`, nhóm nút chức năng luôn hiển thị

## Trạng thái hiện tại

**Đang hoạt động tốt:**
- Vòng quay SVG với trọng số theo `count × weight`
- Chữ trên ô xoay theo bán kính và tự co cỡ theo kích thước ô (chịu được 40+ ô)
- Mũi tên SVG nổi bật (viền trắng + viền tối + bóng), đổi màu theo ô đang chỉ trong lúc quay, giữ màu ô trúng khi dừng
- Banner kết quả và danh sách lịch sử có chỗ cố định, layout không nhảy khi banner hiện/ẩn
- Lưu/khôi phục toàn bộ phiên làm việc qua localStorage (tải lại trang vẫn giữ màn hình, số lượng giải còn lại, góc quay)
- Xuất CSV có BOM (đúng encoding tiếng Việt)
- Báo cáo thống kê giải (đã dùng, còn lại, tỉ lệ %) khi bấm History
- Hiệu ứng confetti (file local), âm thanh quay
- Chế độ PC/Mobile
- Chặn F12, Ctrl+S, chuột phải
- Cảnh báo trước khi thoát nếu có lịch sử

**Lưu ý:**
- Chỉ hiển thị lần trúng cuối cùng trong `#history-list` (không hiển thị toàn bộ, chỉ lưu đầy đủ trong `historyData[]` và localStorage)
- Chữ trên ô quay theo hướng bán kính nên nửa vòng bên trái bị lộn ngược (chấp nhận được, như hầu hết vòng quay kiểu này)
- Tên giải rất dài trên vòng nhiều ô sẽ bị cắt bằng "…" hoặc thu nhỏ tới cỡ tối thiểu

## Thay đổi gần nhất

| Commit | Nội dung |
|--------|----------|
| `3e121fb` | Mũi tên nổi bật + đổi màu theo ô đang chỉ; sửa lượt quay đầu bị chậm; chặn âm thanh làm nóng tắt tiếng lượt quay thật |
| `c077fb3` | Chữ trên vòng tự co theo ô; cố định chỗ banner kết quả/lịch sử |
| `25ca087` | Làm nóng layer transform + âm thanh khi vào màn Play |
| `acc06a2` | Tạo lại CNAME với domain `luckywheel.vinhhp2.site` (commit trực tiếp trên GitHub) |
| `bee06f2` | Mọi phím (trừ phím chức năng) đều bắt đầu quay, kể cả Enter |
| `03e14a1` | Đổi confetti từ CDN sang file local |
| `25f4168` | Làm trong nút ẩn/hiện chức năng, thêm ReadMe |
| `fc83405` | Xóa nút fullscreen, sửa lệch viền vòng quay |
| `852f385` | Lưu trạng thái, đồng bộ số lượng khi về Setup |

## TODO / Việc cần làm tiếp

- [ ] Hiển thị toàn bộ lịch sử trong `#history-list` thay vì chỉ mục cuối
- [ ] Cho phép lưu/load cấu hình giải thưởng ra file JSON (hiện chỉ có `app_state` trong localStorage)
- [ ] Hiệu ứng đếm ngược hoặc highlight ô trúng sau khi dừng
- [ ] Cân nhắc đồng bộ lại `ReadMe.txt` (chưa nhắc tới phím tắt "mọi phím" và màu mũi tên)

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
- **Không commit** `Guide/`, `index-test.html` trừ khi chủ dự án yêu cầu (đang để untracked có chủ đích)
- **Không xóa file không phải do mình tạo** trong thư mục repo (đã từng lỡ xóa nhầm `BG.jpg`/`BG1.jpg` của người dùng khi dọn file test)

### Cách tiếp cận ưu tiên
- Khi thêm tính năng mới: thêm CSS vào `<style>`, HTML vào đúng panel, JS vào `<script>` – giữ theo thứ tự này
- Tính năng liên quan đến giải thưởng: luôn kiểm tra cả 2 trường hợp `showZeroQuantityPrizes = true/false`
- Khi sửa animation quay: chú ý `lastRotation` là tích lũy (không reset về 0) để tránh wheel giật ngược

### Bẫy kỹ thuật đã gặp (đọc trước khi sửa phần quay)
- **`#wheel` phải có `transform: rotate(...)` inline trước khi bật transition.** CSS gốc là `translateZ(0)`; nếu chuyển thẳng sang `rotate(2000deg)` thì trình duyệt nội suy bằng ma trận (đường ngắn nhất ≤180°) và vòng chỉ nhích vài chục độ ở lượt quay đầu. `spinWheel()` đã có guard cho việc này – đừng bỏ
- **Màu mũi tên phụ thuộc `getWheelRotation()`** (đọc ma trận từ computed style) nên chỉ đúng khi transform là các hàm `rotate` cùng loại như trên
- **`drawWheel()` gọi `updateArrowColor(lastRotation)` ở cuối** – nếu thêm đường vẽ lại khác thì nhớ cập nhật màu mũi tên
- **Banner kết quả nằm trong `.result-slot` cao cố định (~2 dòng)**; `#history-list` có `min-height`. Đừng bỏ, nếu không layout sẽ nhảy mỗi lần banner hiện/ẩn vì `body` căn giữa theo chiều dọc
- **Mũi tên cắm sâu vào vòng ~8px** và `.wheel-container` có `margin-top: 2.5rem` để mũi tên không bị cắt ở mép trên

### Cách test UI trong trình duyệt của Claude
- Mở bằng static server (ví dụ `python -m http.server`) thay vì `file://` – `file://` bị render như `data:` URL, `localStorage` bị chặn nên `saveAppState()` ném lỗi và `spinning` kẹt ở `true`
- Cửa sổ trình duyệt có thể ở trạng thái ẩn (`document.hidden`) làm animation/`requestAnimationFrame` bị đóng băng → để kiểm tra animation hãy dùng Web Animations API (`wheel.getAnimations()[0].pause()` rồi đặt `currentTime`) thay vì chờ thời gian thực
- Kiểm tra ô nào đang nằm dưới mũi tên bằng `document.elementsFromPoint(...)` (lấy `<path>` trong `#wheel-svg`), không dùng `elementFromPoint` vì lớp phủ mũi tên che vùng đỉnh vòng
- File tạm (vd `.claude/launch.json`) tự tạo để test thì phải tự xóa sau khi xong

### Nguồn dữ liệu
- State nằm trong RAM (biến global JS) + localStorage (`"lich_su_trung_thuong_csv"` và `"app_state"`)
- Không có backend, không có API, không có database
- Khi tải lại trang: cả lịch sử lẫn trạng thái phiên (màn hình, giải còn lại, góc quay…) được khôi phục từ localStorage; xóa dữ liệu trình duyệt hoặc dùng ẩn danh thì mất
