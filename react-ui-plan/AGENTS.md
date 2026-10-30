# Quy tắc thực thi Codex cho React UI Library

Tài liệu này được cung cấp như **mẫu để đặt vào repository triển khai sau khi kiến trúc được phê duyệt**. Nó không cấp quyền triển khai hoặc publish từ yêu cầu lập kế hoạch hiện tại.

## Mục tiêu và thứ tự đọc

1. Đọc yêu cầu task hiện tại và trạng thái workspace/Git.
2. Đọc AGENTS.md, task tương ứng trong ROADMAP.md và báo cáo prerequisites.
3. Đọc ADR và phần ARCHITECTURE liên quan; COMPONENT_GUIDELINES cho component work; TESTING_STRATEGY cho verification.
4. Kiểm dependencies/version lock hiện có trước thay đổi.

Yêu cầu trực tiếp của owner là nguồn scope hiện tại. ADR approved là nguồn quyết định kiến trúc; tài liệu proposal chưa tự trở thành approved. Nếu tài liệu mâu thuẫn, chỉ ra điều mâu thuẫn, phạm vi ảnh hưởng và đề xuất cụ thể; tiếp tục phần độc lập trong task nếu có.

## Ranh giới mỗi phiên

Chỉ thực hiện task hoặc nhóm task được giao. Không tự kéo task sau vào scope. Không scaffold future folders/components để “chuẩn bị”. Không thay React/browser support, module format, public APIs, CSS distribution, locale model hoặc package split ngoài task/ADR được duyệt.

Chi tiết implementation nội bộ hợp lý trong scope có thể tự quyết; không xin duyệt từng file. Nếu cần đổi architecture, tạo proposal/ADR với reproduction, alternatives, impact; không silently ship thay đổi. Runtime dependency mới phải có record và được task/ADR cho phép; không install rồi mới giải thích.

Không bắt đầu Storybook trước G07. Không publish npm, deploy website hoặc gửi thông báo cho người khác nếu task hiện tại chưa ủy quyền rõ. Chuẩn bị artifact/reviewable changes và checks đầy đủ trước bước quyền phát hành. Không dùng tool để giả manual AT evidence.

## Quy tắc code

- React/TypeScript strict; native semantics; no public any; valid DOM.
- Tôn trọng controlled/uncontrolled/ref/event contracts; default không đổi nghĩa props native.
- Không UI engine wrappers, CSS-in-JS runtime, hidden app state hoặc network/analytics.
- Core không import components; nội bộ không import root barrel; explicit exports only.
- Styles theo CSS Variables/layers; no global Preflight/reset; no runtime dynamic Tailwind class construction.
- Ref/event merge helper dùng consistent; no arbitrary cloneElement/asChild/polymorphic engine.
- DOM/listeners chỉ lifecycle phù hợp, cleanup đối xứng; SSR-safe import và first render deterministic.
- Global coordination chỉ lazy document overlay manager được approved; không store user/theme/locale data tại module scope.

## Verification

Chạy targeted tests đúng risk. Interaction phải kiểm browser khi focus/layout/inert liên quan; jsdom/axe không thay manual AT. Type/API changes phải chạy type contracts; exports/build/CSS changes phải packed consumer; dependency changes phải size/license/graph.

Không gọi test pass nếu chưa chạy. Nếu thiếu browser binary/device/auth, ghi exact blocked check và bằng chứng đã có, không bỏ gate. Không giảm coverage/size/a11y threshold hoặc accept snapshots để hết lỗi. Không sửa tests để hợp behavior sai specification; đề xuất thay spec riêng nếu có căn cứ.

Sau thay đổi kiểm diff để không đè code của user, không secret/fixture data riêng tư, không unused deps, không export accidental. Những check bắt buộc của task phải pass trước DONE. Giữ status IN_PROGRESS/BLOCKED nếu manual prerequisite còn thiếu.

## Báo cáo và continuity

Lưu `docs/reports/<TASK-ID>.md` theo mẫu ROADMAP: input evidence, files, behavior, commands/result, manual reviewer, public API/dependency/bundle impact, deviations/ADR, remaining risk và next eligible task.

Cập nhật task ledger và docs/API inventory khi scope yêu cầu. Không đổi toàn bộ architecture/roadmap prose cho một implementation fix. Task tiếp theo đọc report trước; không tin claim “đã xong” thiếu evidence. Dùng status rõ ràng để phiên mới không lặp lại completed work hoặc bỏ qua gate.

Kết thúc phiên bằng kết quả có thể review: đã thay đổi gì, vì sao, kiểm tra nào pass, phần nào còn blocked. Không tự gửi PR/message cho người khác khi chưa có chỉ dẫn.
