# Quy tắc đóng góp – Nhóm [ ]

**Đề tài:** Quản lý Dịch vụ Giao hàng Thương mại điện tử.

Mọi thành viên phải tuân theo các quy tắc dưới đây. Chúng lấy từ tiêu chí của 6 bài thực hành (TH1–TH6) và workflow *Kiểm mốc*; vi phạm thường làm CI đỏ hoặc mất điểm đóng góp.

## 1. Tài khoản và commit

- Commit bằng **tài khoản GitHub của chính mình**. `git config --global user.email` phải **trùng email GitHub**, nếu không commit sẽ không được tính (TH1).
- Sai email thì sửa commit gần nhất bằng `git commit --amend --reset-author --no-edit`.
- Ai cũng phải có commit ở mỗi mốc. Kiểm bằng **Insights → Contributors**.
- Không commit khoá API, mật khẩu, tệp `.env`. Biến môi trường đặt trong **Settings → Secrets and variables → Actions** (TH6).

## 2. Trước mỗi commit

Tự trả lời ba câu, không nhìn màn hình (TH2). Chưa trả lời được thì chưa commit:

1. Đoạn này làm gì?
2. Xoá nó thì chỗ nào hỏng?
3. Trong repo có chỗ nào khác đang làm cùng việc này không?

## 3. Nhánh và nộp nhánh

### 3.1 Đặt tên nhánh

Mỗi người làm trên nhánh riêng, đặt tên theo cú pháp `week<n>/<chu-de>`, trong đó `<n>` là tuần của môn học và `<chu-de>` viết không dấu, nối bằng gạch ngang:

```
week5/fr-shipper
week5/nfr-khoi-phuc
week8/class-diagram
week8/state-shipment
week10/adr-luu-phien
week13/test-br-huy-don
```

Nhánh nộp mốc là ngoại lệ, bắt buộc tên `moc/Mx` (`moc/M0`, `moc/M1`…) vì workflow *Kiểm mốc* đọc tên này.

- Mỗi nhánh chỉ phục vụ **một chủ đề**, làm xong trong vài ngày rồi mở PR. Không dồn việc cả tuần vào một nhánh.
- Không commit hay push thẳng vào `main`.

### 3.2 Thao tác hằng ngày

```
git checkout main
git pull origin main
git checkout week5/fr-shipper
git rebase main          # hoặc: git merge main
```

Xung đột thì mở tệp ra, giữ phần của cả hai bên, `git add`, rồi `git rebase --continue`. Không tự ý xoá dòng của người khác.

Mỗi khi xong một phần nhỏ: commit, push lên nhánh, mở hoặc cập nhật PR. Không để dồn code cả tuần mới push một lần.

### 3.3 Nộp nhánh (Pull Request)

- Tiêu đề PR theo đúng quy ước commit ở mục 4. Khi squash, tiêu đề PR thành commit trên `main`.
- Một PR giải quyết **một chủ đề**, ít tệp để dễ review.
- Mỗi PR cần **ít nhất một người khác** duyệt. Sửa tệp dùng chung (`docs/yeu-cau.md`, `diagrams/class.mmd`, `AGENTS.md`, `.github/`) thì tag người phụ trách phần đó.
- Dùng **Squash and merge** để lịch sử `main` gọn.
- **Nguyễn Đức Mạnh quản lý repo**: Settings, collaborator, branch protection, merge PR mốc. Muốn đổi cấu hình repo hoặc deploy thì báo Mạnh.
- Ghép code của nhiều người: merge từng nhánh một, chạy thử ngay sau mỗi lần. Hai phần không khớp giao kèo thì **ghi lại trước khi sửa** (TH2).
- Nộp mốc: nhánh `moc/Mx` → mở PR vào `main` → đọc từng dòng ❌ của *Kiểm mốc* → sửa → push lại. **Chỉ merge khi xanh.**

Trước khi mở PR, đánh dấu đủ:

- [ ] `dotnet build` và `dotnet test` chạy xanh ở máy mình.
- [ ] Workflow trên GitHub (Kiểm mốc, CI) không có ❌ mới do mình gây ra.
- [ ] Tự trả lời được 3 câu ở mục 2 cho mọi đoạn mình sửa.
- [ ] Chỉ sửa tệp trong phần việc của mình, hoặc đã báo người phụ trách.
- [ ] Sơ đồ `.mmd` vừa sửa render được trên GitHub.
- [ ] Không có tệp `.env`, khoá API, thư mục `bin/`, `obj/` hay tệp rác.
- [ ] Đã lưu ai-log nếu có dùng agent.

## 4. Quy ước viết commit

Định dạng: `[loại]: <mô tả ngắn> (<mã yêu cầu nếu có>)`

```
[feat]: add create order form (FR-02)
[fix]: block cancel after shipment picked up (BR-01)
[test]: add test for invalid state transition (BR-02)
[diagram]: add Shipment lifecycle to state.mmd
[docs]: add NFR for recovery time (NFR-03)
```

### Phân loại commit

| Loại | Áp dụng khi |
| --- | --- |
| `feat` | Thêm chức năng mới. |
| `fix` | Sửa lỗi. |
| `test` | Thêm hoặc sửa test. |
| `docs` | Sửa tài liệu: `docs/`, README, CONTRIBUTING, AGENTS.md, ADR, phan-tu. |
| `diagram` | Thêm hoặc sửa sơ đồ trong `diagrams/`. |
| `ui` | Đổi giao diện Blazor: bố cục, component, CSS. |
| `data` | Đổi dữ liệu mẫu, seed, migration. |
| `research` | Đổi protocol, rubric, dataset, prompt, kết quả chạy reviewer. |
| `refactor` | Cấu trúc lại mã, không đổi hành vi. |
| `ci` | Sửa workflow trong `.github/` hoặc cấu hình deploy (qua Mạnh). |
| `chore` | Việc vặt: `.gitignore`, di chuyển tệp, cập nhật cấu hình nhỏ. |
| `revert` | Hoàn tác một commit trước đó. |

### Quy tắc viết mô tả

- Viết bằng tiếng Anh, ngắn gọn, dùng động từ mệnh lệnh: add, fix, remove, rename, split.
- Commit đáp ứng một yêu cầu cụ thể thì ghi mã ở cuối tiêu đề: `(FR-03)`, `(BR-01)`, `(NFR-02)`, hoặc mã task `(W03-05)`.
- **Mỗi commit một việc.** Không gộp "thêm form đặt đơn" với "sửa sơ đồ lớp" vào một commit.
- Cần giải thích thì xuống một dòng trống và viết phần thân: nói **vì sao** đổi, không diễn giải lại mã.
- Ít nhất một hash commit của mình phải được trích trong `phan-tu/Mx.md`, nên viết commit sao cho đọc tiêu đề là hiểu.

### Ví dụ đúng và sai

| Đúng | Sai |
| --- | --- |
| `[feat]: add shipper status update (FR-05)` | `update code` |
| `[fix]: prevent duplicate return request (BR-03)` | `fix bug` |
| `[test]: cover cancel order after pickup (BR-01)` | `test` |
| `[refactor]: split OrderService validation into Order` | `changed many things` |
| `[diagram]: rename Phòng to Phong in class.mmd` | `sửa sơ đồ` |
| `[docs]: add M1 reflection to phan-tu/M1.md` | `some changes` |

## 5. Mỗi mốc phải nộp kèm

| Tệp | Yêu cầu |
|---|---|
| `phan-tu/Mx.md` | M0 ≥ 40 từ, các mốc sau ≥ 120 từ, có **ít nhất một hash commit** |
| `ai-log/Mx-*.md` | Bản ghi hội thoại với agent trong mốc đó |

Hạn nộp: 23:59 Chủ nhật của tuần có mốc.

## 6. Dùng agent (AI)

- Được dùng, nhưng **đọc qua mọi lệnh agent bảo chạy**. Thấy `rm -rf`, `sudo`, hoặc lệnh dán khoá vào tệp thì dừng lại hỏi (TH1).
- Hỏi từng việc nhỏ, yêu cầu giải thích cách làm trước rồi mới viết. Không hỏi "viết toàn bộ tính năng X" (TH2).
- Không cài thư viện agent đề xuất nếu không cần. Thêm thư viện mới phải có ADR.
- Agent phản biện nói sai thì bác bỏ bằng văn bản, ghi rõ nó hiểu nhầm chỗ nào (TH4).
- Ràng buộc cho agent ghi trong `AGENTS.md`. Agent làm sai ranh giới thì sửa `AGENTS.md` cho rõ hơn (TH5).

## 7. Tài liệu và sơ đồ

- Sơ đồ ở `diagrams/`: tệp `.mmd` viết Mermaid thuần, **không** bọc ```mermaid. Bản để xem trên GitHub đặt trong `.md` có bọc.
- Tên lớp **không dấu, không khoảng trắng**, chỉ chữ, số, gạch dưới. Tên lớp trong `class.mmd` phải trùng tên `participant` trong `seq-*.mmd`; lệch thì sửa sequence cho khớp class (TH4).
- Mọi mũi tên trong `state.mmd` có nhãn. Mỗi lớp phải được nhắc trong ít nhất một tệp ở `docs/`.
- Yêu cầu chức năng viết dạng *Là [ai], tôi muốn [gì] để [được gì]*, đánh số FR-01… Không viết FR cho đăng nhập, đăng ký, trang chủ, kết nối CSDL (TH3).
- Yêu cầu phi chức năng phải đủ: *[chỉ số] phải [ngưỡng có đơn vị] khi [điều kiện], đo bằng [cách nào]*. Mỗi người viết ít nhất một dòng; ít nhất một dòng không nói về tốc độ.
- Ràng buộc nghiệp vụ (BR-01…) là luật kinh doanh, không phải kiểm tra định dạng dữ liệu. Mỗi BR phải ghi rõ được chặn ở tầng nào (TH5).
- Quyết định kiến trúc ghi bằng ADR trong `adr/`, có bảng ≥ 2 phương án và cột "Mất".

## 8. Mã và kiểm thử

- Mỗi người **đọc toàn bộ `src/`**, kể cả phần người khác viết. Thầy có thể hỏi bất kỳ ai về bất kỳ tệp nào (TH2, TH6).
- Ưu tiên test cho ràng buộc nghiệp vụ hơn test cho getter (TH6).
- Không bỏ qua hay tắt test để CI xanh. Pipeline phải chạy dưới 5 phút.
- Từ tuần 13 **không thêm tính năng mới**, chỉ hoàn thiện luồng lõi, dữ liệu mẫu, README.

## 9. Deploy

- **Nguyễn Đức Mạnh phụ trách deploy Cloudflare Pages.** Push vào `main` là tự deploy.
- URL demo phải luôn sống và mở được trên điện thoại. Ai phát hiện URL hỏng thì báo Mạnh ngay.
