# AGENTS.md — ràng buộc kiến trúc cho agent

> Hầu hết agent lập trình đọc tự động tệp này trước khi làm việc trong repo. Trong đây ghi những gì agent **phải** tuân theo và **không được** làm. Viết ở mốc M4, sau khi đã có kiến trúc. Trước đó cứ để nguyên phần "Quy ước chung".
>
> Muốn thử xem nó có tác dụng không thì giao cho agent một việc đụng tới ranh giới module. Agent xuyên qua ranh giới nghĩa là ràng buộc viết chưa đủ rõ, sửa tệp này trước rồi hãy sửa mã.

## Quy ước chung (giữ nguyên)

- Sơ đồ viết bằng Mermaid trong `diagrams/`, không dùng ảnh.
- Mỗi quyết định kiến trúc một tệp trong `adr/`, theo mẫu `adr/0000-mau.md`.
- Không commit khoá API, mật khẩu, tệp `.env`.
- Đừng đụng vào `.github/workflows/`.
- Commit message tiếng Việt hoặc Anh, một dòng, nói **vì sao** chứ không phải cái gì.

## Kiến trúc

### Phân tầng

- Đề tài: **Quản lý Dịch vụ Giao hàng Thương mại điện tử** (đặt đơn, vận chuyển, hoàn hàng, ngoại lệ).
- Stack: C# / .NET 10, ASP.NET Core + Blazor (Interactive Server), EF Core 10 + SQLite, ASP.NET Core Identity, SignalR, xUnit, Stryker.NET, Microsoft Agent Framework.
- Lệnh chính: `dotnet restore`, `dotnet build`, `dotnet test`, `dotnet format --verify-no-changes`.

- `src/Delivery.Domain/` chứa entity, bất biến và state machine của Orders, Shipments, Returns. **KHÔNG** tham chiếu project nào khác, không dùng EF, HTTP, SignalR hay LLM.
- `src/Delivery.Application/` chứa use case, DTO, interface port, kiểm quyền theo đối tượng. Chỉ phụ thuộc `Delivery.Domain`.
- `src/Delivery.Infrastructure/` chứa EF Core, Identity và adapter (Persistence, Identity, Realtime). Phụ thuộc Application và Domain.
- `src/Delivery.Web/` chứa Blazor component, endpoint, SignalR hub, composition root. Phụ thuộc Application và Infrastructure. UI **không** tự quyết luật nghiệp vụ.
- `src/Review.Agent/` chứa AI reviewer (contract, provider, workflow, tool). **KHÔNG** tham chiếu `Delivery.Infrastructure`; nhận code dưới dạng dữ liệu.
- `src/Review.Cli/` chỉ phụ thuộc `Review.Agent`; chạy reviewer và xuất báo cáo.
- `tests/Delivery.UnitTests/<Module>/`: test không I/O thật (không DB, mạng, đồng hồ thật).
- `tests/Delivery.IntegrationTests/<Module>/`: DB, Identity, endpoint, realtime; dữ liệu tách riêng mỗi test.
- `tests/Review.Agent.Tests/`: dùng fake provider và fixture; không gọi model trả phí.

### Bất biến nghiệp vụ — KHÔNG được vi phạm

- Chuyển trạng thái đơn và shipment chỉ đi qua state machine trong `Delivery.Domain`. Chuyển sai (ví dụ từ đã giao về chờ xác nhận) phải bị chặn và ném lỗi ở Domain, không chỉ ẩn nút trên UI.
- Kiểm quyền theo đối tượng (ai được xem/sửa đơn nào) nằm trong `Delivery.Application`, không nằm trong component Blazor.
- Client SignalR chỉ nhận sự kiện của đơn mà người đó được phép xem.
- Các luật BR-xx trong `docs/yeu-cau.md`: mỗi luật ghi kèm nơi chặn. Bảng sẽ điền sau M1 và M4; khi chưa có luật trong SRS thì **không tự đặt ra luật mới**, hãy hỏi.

Phần nghiên cứu:

- `research/baselines/` là test do người viết, **bất biến sau khi freeze**.
- Giữ nguyên production code giữa lần đo trước và sau của cùng một unit.
- Không đổi model, prompt, SDK hay rubric giữa các lần chạy đã khoá.
- Test hoặc gợi ý do AI hỗ trợ phải ghi nguồn; không đưa vào baseline human-only.

### Ranh giới — agent KHÔNG được

- Không gọi EF/DbContext trực tiếp từ `Delivery.Web` hay `Delivery.Domain`.
- Không thêm tham chiếu project ngược chiều phụ thuộc ở trên.
- Không thêm package NuGet mới hay nâng phiên bản mà không có ADR trong `docs/adr/`.
- Không sửa `research/baselines/`, `research/dataset/`, `research/prompts/` hay `research/evaluation/` trừ khi task yêu cầu rõ.
- Không viết hay sửa unit test trong dataset baseline thay cho người viết.
- Không mock `DbSet` của EF để chứng minh hành vi database; dùng integration test với SQLite.
- Không xoá, bỏ qua hay đánh dấu skip test để CI xanh.
- Không commit khoá API, chuỗi kết nối, tệp `.env`. Bí mật đặt trong GitHub Secrets.
- Không sửa `.github/`, `CODEOWNERS`, cấu hình deploy Cloudflare hay Settings repo; những thay đổi này đi qua Nguyễn Đức Mạnh (quản lý repo và deploy).
- Không push thẳng vào `main`, không force-push, không tự merge PR.
- Không chạy lệnh `rm -rf`, `sudo`, hay lệnh ghi khoá vào tệp mà chưa được người dùng xác nhận.
- Không đổi tên lớp trong `diagrams/class.mmd` mà không sửa tương ứng `seq-*.mmd` và `docs/`.

### Khi thêm tính năng mới

1. Thêm hoặc cập nhật FR trong `docs/yeu-cau.md`; đổi luật nghiệp vụ thì cần Change Request trước.
2. Thêm use case vào `diagrams/use-case.mmd` và lớp vào `diagrams/class.mmd`.
3. Rồi mới viết mã. Sơ đồ là nguồn sự thật.

- Làm theo một task/issue một lần, trên nhánh `week<n>/<chu-de>` (nộp mốc dùng `moc/Mx`). Commit dạng `[loại]: <mô tả tiếng Anh> (<mã yêu cầu>)`, xem mục 3–4 của `CONTRIBUTING.md`.
- Chỉ sửa tệp trong phạm vi issue. Cần sửa ngoài phạm vi thì dừng lại và nói rõ.
- Giải thích cách làm trước khi viết code. Viết từng phần nhỏ để người dùng trả lời được: đoạn này làm gì, xoá thì hỏng gì, có chỗ nào trùng không.
- Thay đổi luật nghiệp vụ hay hành vi người dùng thấy được cần Change Request trong `docs/change-requests/` trước khi code.
- Trước khi kết thúc: chạy `dotnet build`, `dotnet test`, `dotnet format --verify-no-changes`, và báo kết quả thật. Không báo "đã chạy" khi chưa chạy.
- Ghi lại hội thoại vào `ai-log/` theo mốc.

## Kiểm thử

- Mọi luật nghiệp vụ trong Domain phải có unit test không cần CSDL.
- Hành vi database, Identity, SignalR kiểm bằng integration test với SQLite, không mock `DbSet`.
- Chạy: `dotnet test`.
