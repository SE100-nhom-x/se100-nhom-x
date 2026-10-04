# ai-log M0 – Claude

**Agent:** Claude (Claude Code, trong Project của nhóm)

---

## 1. Workflow Kiểm mốc không có trong repo

**Tình huống:** repo được tạo từ bản zip của môn, bản zip không có thư mục `.github/`, nên mục E của TH1 ("Workflow Kiểm mốc đã chạy ít nhất một lần") không làm được.

**Agent làm:** viết lại `.github/workflows/kiem-moc.yml`: chạy `python scripts/kiem_moc.py` trên mọi PR vào `main` từ nhánh `moc/Mx`, checkout `fetch-depth: 0` để script đếm được email tác giả trên toàn bộ lịch sử.


## 2. Viết tên hệ thống và 5 dòng mô tả trong README

**Tôi yêu cầu:** "thay tên hệ thống và viết 5 dòng mô tả theo yêu cầu vào readme".

**Agent đề xuất:** tiêu đề "Quản lý Dịch vụ Giao hàng Thương mại điện tử — SE100 · Nhóm [ ]" và 5 dòng:

1. Hệ thống quản lý dịch vụ giao hàng cho các shop bán hàng online, gồm ba vai: Chủ shop, Điều phối viên và Shipper.
2. Chủ shop tạo đơn giao hàng, theo dõi trạng thái đơn và yêu cầu huỷ khi đơn chưa được lấy hàng.
3. Điều phối viên phân công shipper cho từng đơn và xử lý các đơn giao thất bại hoặc phải hoàn về shop.
4. Shipper xem các đơn được giao cho mình và cập nhật trạng thái lấy hàng, đang giao, đã giao hoặc giao thất bại.
5. Không được phép xảy ra: một đơn có hai shipper cùng lúc, hoặc đơn đã lấy hàng bị huỷ trực tiếp thay vì chuyển sang xử lý hoàn.

Agent chạy `python scripts/kiem_moc.py M0` ở máy: hai mục README đều ✅.

## 3. Kiểm mốc bị skip


**Tôi yêu cầu:** "không được skip kiemmoc"".

**Agent làm:** Kiểm mốc chạy thật trên PR #87.

**Bài học:** mọi thay đổi để nộp M0 phải nằm trên nhánh `moc/M0` (đúng mục E1–E2 của TH1). Nhánh khác thì Kiểm mốc không chạy.


## Còn lại cho M0

- [ ] Mỹ và Trọng Minh mỗi người tự commit ít nhất một lần.
- [ ] `docs/cau-hoi-khach-hang.md` đủ 3 câu hỏi có dấu "?".
- [ ] `phan-tu/M0.md` ≥ 40 từ, có ít nhất một hash commit.
