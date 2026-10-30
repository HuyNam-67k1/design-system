# Bộ kế hoạch React UI Component Library và Design System

Ngày: 30/10/2026. Ngôn ngữ: tiếng Việt. Trạng thái: **đề xuất kiến trúc và kế hoạch, chưa triển khai source code**.

Đề xuất chính: hybrid UI library tự xây behavior, một npm package ESM, Tailwind tại build time, CSS Variables và CSS đã build. MVP 17 components cộng provider/theme family; custom Select, floating overlays và DatePicker ở các phase sau. Storybook repository riêng chỉ bắt đầu sau npm release và registry smoke thành công.

## Đọc theo thứ tự

| File | Nội dung |
| --- | --- |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Technical Design Document đủ20 mục: product/stack/layers/APIs/tokens/locale/a11y/catalog/performance/build/release/Storybook/risks |
| [ROADMAP.md](ROADMAP.md) | 13 phases, 74 task contracts, dependencies, files, acceptance/tests và phase gates |
| [COMPONENT_GUIDELINES.md](COMPONENT_GUIDELINES.md) | API/React/TypeScript/events/refs/slots/CSS/component rules |
| [TESTING_STRATEGY.md](TESTING_STRATEGY.md) | Test pyramid, package/browser/AT matrices, performance và component DoD |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Development/review/dependency/Git/SemVer/release/maintenance workflow |
| [AGENTS.md](AGENTS.md) | Quy tắc Codex theo task và continuity giữa các phiên |
| [SOURCES.md](SOURCES.md) | Nguồn chính thức đã đối chiếu, version evidence và giới hạn xác minh |

## Cách bàn giao

Đọc và chấp nhận ADR/scope ở P00. Khi muốn bắt đầu code, giao một Task ID cụ thể cùng thư mục repository. Codex kiểm prerequisite reports và thực hiện trong ranh giới task, không tự publish hoặc triển khai toàn roadmap. Mẫu prompt và report nằm cuối ROADMAP.

Giả định mặc định: một maintainer, React18.3.1/19.2+, modern browsers, TypeScript consumers≥5.5, en mặc định và vi đầy đủ, Gregorian date-only. `@scope/ui`, tên repository và prefix là placeholder; owner chốt trước publish.

Ước lượng MVP 44–61 ngày công, khoảng12–16 tuần lịch nếu75% capacity; Storybook thêm5–8 ngày. Đây là planning estimate, phụ thuộc kết quả spikes và manual accessibility testing. Không có claim test/build/publish đã pass trong bộ kế hoạch.
