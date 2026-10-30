# Nguồn chính thức và phạm vi kiểm chứng

Đối chiếu ngày **30/10/2026**. Các nguồn dưới đây đã được mở/đọc trong quá trình phân tích. Version patch/registry dist-tags không được coi là lockfile đã test; P00-01 xác minh exact versions trước implementation. Budgets, scope, API examples, time estimates và ADR choices là đề xuất kiến trúc của kế hoạch.

| ID | Nguồn | Dữ kiện sử dụng và giới hạn |
| --- | --- | --- |
| S01 | [React Versions](https://react.dev/versions) | Trang docs hiển thị dòng React19.3; lựa chọn hỗ trợ18.3.1/19.2+ là quyết định sản phẩm |
| S02 | [React forwardRef](https://react.dev/reference/react/forwardRef) | React19 nhận ref như prop, forwardRef không còn cần cho19; giữ forwardRef để tương thích18 là lựa chọn dự án |
| S03 | [Tailwind Compatibility](https://tailwindcss.com/docs/compatibility) | Docs đang ở4.3; browser foundations Chrome111/Safari16.4/Firefox128; floor library cần test riêng |
| S04 | [Tailwind Source Detection](https://tailwindcss.com/docs/detecting-classes-in-source-files) | node_modules bị bỏ qua mặc định; full static classes và @source cho external source |
| S05 | [Tailwind Functions and Directives](https://tailwindcss.com/docs/functions-and-directives) | @apply inline utilities; @reference dùng theme/utility definitions không duplicate stylesheet; CSS strategy vẫn cần prototype test |
| S06 | [TypeScript6.0 release notes](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-6-0.html) | Release notes hiện hành cho6.0; consumer5.5 declaration floor phải kiểm bằng fixture |
| S07 | [Node releases](https://nodejs.org/en/about/previous-releases) | Node24 và26 đang LTS; Node20 đã EOL; chọn24 vì maturity, không tuyên bố24 là latest |
| S08 | [Vite8 announcement](https://vite.dev/blog/announcing-vite8), [Library mode](https://vite.dev/guide/build) | Vite8 dùng Rolldown; library mode hỗ trợ external dependencies, entry points và CSS; declaration build riêng là thiết kế của dự án |
| S09 | [Vitest Getting Started](https://vitest.dev/guide/), [Migration](https://vitest.dev/guide/migration/) | Docs yêu cầu Node≥22.12/Vite≥6.4; migration5.0 hiện có; pin stable dist-tag phải xác minh tại foundation |
| S10 | [tsup repository](https://github.com/egoist/tsup) | Maintainer thông báo không còn actively maintained, đề xuất tsdown; không chọn tsup cho project mới |
| S11 | [tabbable repository](https://github.com/focus-trap/tabbable) | Focusability/tab order utility, visibility layout caveats và jsdom limitations; không cung cấp toàn bộ modal behavior |
| S12 | [Floating computePosition](https://floating-ui.com/docs/computeposition), [autoUpdate](https://floating-ui.com/docs/autoupdate) | DOM positioning và lifecycle cleanup; dự án chỉ dùng geometry, tự viết interactions |
| S13 | [Internationalized Date](https://react-aria.adobe.com/internationalized/date/) | Immutable date/calendar utilities; docs nêu8kB Brotli toàn thư viện và2.8kB Gregorian subset; không lấy các số đó làm gzip guarantee của UI package |
| S14 | [Playwright accessibility testing](https://playwright.dev/docs/accessibility-testing) | axe integration và giới hạn automated accessibility testing |
| S15 | [WCAG2.2](https://www.w3.org/TR/WCAG22/) | AA goals cho contrast/keyboard/focus/target size và các tiêu chí liên quan; app-level conformance không được thư viện tự bảo đảm |
| S16 | [APG Modal Dialog](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/) | Focus containment, initial/return focus, Escape và modal semantics |
| S17 | [APG Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) | Roving tab focus, manual/automatic activation và tab/panel relationships |
| S18 | [APG Combobox](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/), [Tooltip](https://www.w3.org/WAI/ARIA/apg/patterns/tooltip/), [DatePicker example](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/examples/datepicker-dialog/) | Interaction guidance; Tooltip pattern còn work in progress; DatePicker example không production code và cần AT tests |
| S19 | [Changesets repository](https://github.com/changesets/changesets) | Version/changelog workflow tool; dùng cho một package là lựa chọn vận hành |
| S20 | [npm trusted publishing](https://docs.npmjs.com/trusted-publishers/) | OIDC, Node≥22.14/npm≥11.5.1; dist-tag OIDC có CLI/permission riêng; kiểm lại tại release |
| S21 | [Storybook React-Vite](https://storybook.js.org/docs/get-started/frameworks/react-vite), [Installation](https://storybook.js.org/docs/get-started/install), [Storybook10 announcement](https://storybook.js.org/blog/storybook-10/) | React-Vite integration, static build; Storybook10 ESM-only; chọn stable version khi phase bắt đầu, không mặc định10 là latest |
| S22 | [publint](https://publint.dev/docs/), [Are The Types Wrong](https://github.com/arethetypeswrong/arethetypeswrong.github.io) | Package configuration/type resolution validation; không thay consumer browser smoke |
| S23 | [Testing Library principles](https://testing-library.com/docs/guiding-principles/) | Tests ưu tiên hành vi người dùng và DOM, không component instances |
| S24 | [Node package exports](https://nodejs.org/api/packages.html) | type/module, subpath/conditional exports; ESM-only là quyết định dự án |
| S25 | [Semantic Versioning](https://semver.org/) | Major/minor/patch cho public API; pre-1.0 convention cụ thể được dự án ghi riêng |

## Version verification trước implementation

P00-01 phải ghi từng dependency: exact version, dist-tag, release date nếu xác minh được, license, engines, peer dependencies, link source, expected consumer impact. So sánh docs với registry; nếu lệch hoặc docs preview thì ưu tiên stable release đã xác nhận và ghi lý do. Không đổi major vì công cụ scaffold tự chọn latest.

Không dùng các số performance vendor để cam kết performance của UI library; đo bundle cuối cùng trên production fixture, gồm transitives và ghi compression method. Không coi APG example là permission copy implementation production chưa review.
