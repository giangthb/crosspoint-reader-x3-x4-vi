# CrossPoint Reader — Xteink X3 & X4 (Vietnamese)

Firmware CrossPoint cho máy đọc sách **Xteink X3 và X4** với hỗ trợ tiếng Việt đầy đủ (UI + font).

> ⚠️ **Bản không chính thức.** Flash tự chịu rủi ro. Hãy backup partition stock trước khi flash.

## Điểm đặc biệt

- ✅ **UI tiếng Việt** — 292 chuỗi đã dịch (menu, settings, dialog, thông báo). Upstream có 22 ngôn ngữ nhưng **chưa có tiếng Việt** — đây là điểm khác biệt chính của fork.
- ✅ **Font UbuntuVietHoa** — hiển thị đủ dấu tiếng Việt ngay trên giao diện (không cần copy font vào SD).
- ✅ **Hỗ trợ cả X3 lẫn X4** — base trên upstream `crosspoint-reader` 1.3.0, đã hỗ trợ chính thức **cả hai panel**: X4 (480×800) và X3 (528×792). Một bản firmware chạy cho cả hai máy.
- ✅ **Đa ngôn ngữ** — ngoài tiếng Việt, giữ đủ 22 ngôn ngữ của upstream (EN, ES, FR, DE, IT, PT, RU, UK, PL, TR, ...) → tổng 23 ngôn ngữ.
- ✅ **Patch riêng:** fix sleep screen không fill đủ màn hình khi dùng ảnh kích thước panel khác (X4 trên X3 hoặc ngược lại) — xem [Patches](#patches-riêng-của-fork-này). User xác nhận lỗi này **vẫn còn trên 1.3.0**, nên patch vẫn cần.

### Tính năng mới kế thừa từ upstream 1.3.0

- **SD-card fonts (`.cpfont`)** — tự convert TTF/OTF thành font load từ SD, không cần reflash firmware (xem [Sau khi flash](#sau-khi-flash)).
- **Tilt page turn (chỉ X3)** — lật trang bằng nghiêng máy.
- **KOReader progress sync**, **WebDAV**, **OPDS browser** (tối đa 8 server, search, phân trang, tải trực tiếp).
- **OTA update** từ GitHub releases, **EPUB Optimizer**, **WebSocket fast uploads**.
- Nhiều theme (Classic, Lyra, Lyra Extended, RoundedRaff), remap nút trước/bên, điều khiển status bar.

## Cách flash

> 🔒 **Máy bị khoá USB?** Một số máy Xteink mua từ store bên thứ ba (vd AliExpress) bị khoá flash USB từ nhà máy. Nếu trình duyệt không thấy thiết bị qua WebSerial, dùng **Xteink Unlocker** tại <https://crosspointreader.com/#unlock-tool> trước. Máy mua trực tiếp từ xteink.com **không** bị khoá.

### Cách 1 — Web flasher (dễ nhất, khuyên dùng)

1. **Backup trước:** Vào <https://crosspointreader.com/#flash-tools>, chọn máy (**X3** hoặc **X4**), dùng phần backup để lưu partition stock về máy (để rollback nếu cần).
2. Kết nối máy với máy tính qua USB-C, bật/wake máy.
3. Trình duyệt: **Chrome / Edge** (Firefox không hỗ trợ WebSerial).
4. Tải `firmware.bin` từ [Releases](../../releases/latest).
5. Vào <https://crosspointreader.com/#flash-tools>, chọn đúng máy (X3 hoặc X4), chọn **"Custom .bin"**, upload `firmware.bin` vừa tải, flash.
6. Rút USB và cắm lại để restart.

### Cách 2 — esptool (manual)

```bash
pip install esptool
esptool.py --chip esp32c3 --port /dev/ttyACM0 --baud 921600 \
    write_flash 0x10000 firmware.bin
```

Thay `/dev/ttyACM0` bằng device của máy bạn (Linux: chạy `dmesg` sau khi cắm USB).

## Sau khi flash

1. Boot lên, vào **Settings → Language**, chọn **Tiếng Việt**.
2. (Khuyến nghị) Muốn đọc sách tiếng Việt đẹp hơn? Nạp thêm font Unicode có dấu vào SD bằng SD-card font builder chính thức:
   - Vào <https://crosspointreader.com/fonts>, mở form **"SD-card font builder"**.
   - Upload tối đa 4 style (regular, bold, italic, bold-italic), đặt tên family, point size, Unicode range.
   - Tải các file `.cpfont` về, copy vào SD card thư mục `/fonts/TenFont/` (hoặc `/.fonts/TenFont/` để ẩn).
   - Reboot, chọn font trong **Settings → Font**.

## Rollback về firmware stock

Vào <https://crosspointreader.com/#flash-tools>, chọn máy (X3/X4) và flash lại firmware official.

## Nguồn gốc & Credits

Fork này tổng hợp:

- **Base:** [`crosspoint-reader/crosspoint-reader`](https://github.com/crosspoint-reader/crosspoint-reader) — firmware CrossPoint gốc (MIT License), rebase lên **release 1.3.0** (upstream master, commit [`2dd491b`](https://github.com/crosspoint-reader/crosspoint-reader/commit/2dd491b)).
- **Vietnamese i18n:** cherry-pick từ [`danoooob/crosspoint-reader-vi`](https://github.com/danoooob/crosspoint-reader-vi) bởi [@danoooob](https://github.com/danoooob) — bản dịch 292 chuỗi.
- **Font UbuntuVietHoa:** HoangDesign / DesignerViet ([designerviet.com](https://designerviet.com)) — font Ubuntu Việt hóa.

Khác biệt so với `danoooob/crosspoint-reader-vi`:

- Target **cả Xteink X3 lẫn X4** (danoooob build chủ yếu cho X4; upstream 1.3.0 nay hỗ trợ chính thức cả hai panel).
- Dựa trên upstream mới hơn (release 1.3.0, bao gồm SD-card fonts, tilt page turn cho X3, OPDS/WebDAV, và nhiều fix khác).
- Giữ đủ 22 ngôn ngữ upstream + thêm tiếng Việt.
- Có thêm patch sleep-screen crop/scale chưa có trong upstream (xem bên dưới).

Bản upstream gốc (tiếng Anh) được giữ nguyên tại [README-UPSTREAM.md](./README-UPSTREAM.md).

## Patches riêng của fork này

Những fix tôi tự phát hiện và vá thêm so với upstream `crosspoint-reader`. Đã submit upstream PR để các fork khác cùng hưởng.

### Fix: Sleep screen không fill đủ màn hình (X3 & X4)

**Hiện tượng:** Đặt một `sleep.bmp` kích thước của panel này lên máy có panel kia — ví dụ ảnh 480×800 (chuẩn X4) trên X3 (528×792) ở chế độ `Cover Mode: CROP` → ảnh bị render ở góc trái trên với tỉ lệ 1:1 pixel, chừa viền trống bên phải/dưới. Đúng ra ở CONTAIN/CROP phải scale lên cho vừa màn hình. Lỗi đối xứng cho cả hai chiều: ảnh nhỏ hơn panel ở bất kỳ chiều nào đều không được upscale.

**Nguyên nhân (3 bug cộng dồn trong render path):**

1. `SleepActivity::renderBitmapSleepScreen()` chỉ vào branch scale/crop khi bitmap **lớn hơn** màn hình ở ít nhất một chiều. Ảnh nhỏ hơn panel → rơi vào branch "center only" không hề scale.
2. `GfxRenderer::drawBitmap()` chỉ áp dụng scaling khi `fitScale < 1.0` (chỉ downscale). Yêu cầu upscale bị bỏ qua âm thầm.
3. Render loop iterate source pixel và map 1:1 sang dest pixel. Khi upscale, loop để lại dòng/cột trống giữa các source row/col (nearest-neighbor artifact).

**Fix:**

- Trigger scale/crop khi `bitmap size != screen size` (không chỉ khi lớn hơn).
- Thêm flag `allowUpscale` vào `drawBitmap()` (default `false`). Chỉ `SleepActivity` opt-in. BmpViewer, cover thumbnails, Lyra themes giữ nguyên hành vi 1:1 centering như upstream.
- Bypass 1-bit fast path `drawBitmap1Bit()` khi `allowUpscale=true` → ảnh 1-bit cũng được upscale đúng.
- Thay render loop 1:1 bằng **dest-span fill** (nearest-neighbor block): mỗi source pixel ghi ra span `[floor(idx*scale), floor((idx+1)*scale) - 1]`. Khi `scale ≤ 1.0` span = 1, nên hành vi downscale / no-scale không đổi, bit-for-bit giống upstream.

**Áp dụng cho cả hai máy:** ảnh X4 480×800 → X3 528×792 (CROP) giờ render full 528×792; tương tự ảnh X3 528×792 → X4 480×800 cũng fill đúng. Không còn viền trống (đúng cả 2-bit greyscale lẫn 1-bit monochrome).

> Behavior change nhỏ: user có `/sleep/*.bmp` nhỏ hơn panel (trước đây render 1:1 centered) giờ sẽ thấy ảnh scale lên fill panel ở CONTAIN/CROP. Đúng semantics của 2 mode đó, nhưng khác hành vi cũ.

Commits: [`10aeb6f`](../../commit/10aeb6f) + [`1740fdc`](../../commit/1740fdc) + [`7f03723`](../../commit/7f03723) • Files: `lib/GfxRenderer/GfxRenderer.{h,cpp}`, `src/activities/boot_sleep/SleepActivity.cpp`.

Upstream PR: [crosspoint-reader/crosspoint-reader#1716](https://github.com/crosspoint-reader/crosspoint-reader/pull/1716) — chưa merge vào 1.3.0 (lỗi vẫn còn trên bản chính thức), nên fork vẫn giữ patch.

## Build từ source

Yêu cầu: Python 3.8+, PlatformIO Core (pioarduino), `clang-format` 21, USB-C cable.

```bash
git clone --recursive https://github.com/giangthb/crosspoint-reader-x3-x4-vi.git
cd crosspoint-reader-x3-x4-vi
git submodule update --init --recursive
pio run -e gh_release
# Output: .pio/build/gh_release/firmware.bin
```

## License

MIT License — kế thừa từ upstream `crosspoint-reader/crosspoint-reader`. Xem [LICENSE](./LICENSE).

Font UbuntuVietHoa thuộc về HoangDesign / DesignerViet, phân phối theo tinh thần cộng đồng. Font Ubuntu gốc: Ubuntu Font License.

## Disclaimer

Project này **không liên kết với Xteink**. Firmware được build bởi cộng đồng, không có bảo hành. Flash sai có thể brick thiết bị. Luôn backup partition trước khi thử firmware mới.

---

Góp ý / issue: mở [issue](../../issues) hoặc PR.
