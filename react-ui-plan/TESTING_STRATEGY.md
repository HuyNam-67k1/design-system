# Chiến lược Testing và Definition of Done

Tài liệu kế hoạch; commands là contract cho repository tương lai. Không có test library nào đã được chạy trong giai đoạn thiết kế này.

## 1. Phân tầng và nhiệm vụ

| Layer | Vị trí | Công cụ | Gate |
| --- | --- | --- | --- |
| Unit | cạnh source hoặc `tests/unit` | Vitest Node | Pure transitions, equality, locale fallback, date math |
| Component | `*.test.tsx` | Vitest jsdom + Testing Library | DOM semantics, callbacks, state contracts, refs |
| Browser | `tests/browser` | Playwright chạy Vite lab | Focus/layout/inert/scroll/portals/keyboard/pointer |
| A11y automated | `tests/accessibility` | axe-core Playwright | Applicable automated rules, no unresolved violation |
| Types | `tests/types` | tsc positive/negative fixtures | Public declaration shapes and invalid usage rejection |
| Package | `tests/package`, temp consumers | npm pack + publint/ATTW + bundlers | Thực sự cài được package độc lập |
| Visual | `tests/visual` | Playwright screenshots | Theme/state/viewport regression review |
| Manual | `docs/a11y` | NVDA/VoiceOver + human reviewer | Screen reader interaction usability |

Playwright dùng stable page testing, không experimental React component runner. jsdom không layout engine; tabbable visibility/focus cases phải chạy browser. Không đổi production tabbable `displayCheck` để làm unit tests xanh. Coverage không phải bằng chứng accessibility. [S11, S14, S23]

## 2. Commands contract

| Script | Phạm vi | Khi chạy |
| --- | --- | --- |
| `npm run lint` | TS/React/hooks/a11y static rules | Task touched code và mọi PR |
| `npm run format:check` | File formatting | PR |
| `npm run typecheck` | Source/lab/docs examples | Task và PR |
| `npm run test:unit -- <filter>` | Unit + component targeted | Mỗi task |
| `npm run test:browser -- <filter>` | Interaction targeted browsers | Task có DOM behavior |
| `npm run test:types` | Public API TS matrix | API changes và release |
| `npm run test:visual` | Reviewed visual cases | CSS/state/theme changes |
| `npm run build` | JS/declarations/CSS | Packaging/exports changes và release |
| `npm run test:package` | Packed isolated consumers | PR artifacts và release |
| `npm run size` | min+gzip graph/budgets | Dependency/public export/style changes |
| `npm run check` | PR aggregate checks | PR CI |
| `npm run release:verify` | All full gates + evidence validation | Trước publish |

Không chạy toàn bộ matrix sau mỗi một dòng CSS; targeted tests trước, full gates khi PR/release. Nếu sửa sau full pass, chạy lại những checks bị ảnh hưởng và aggregate cần thiết, không dựa report của artifact cũ.

## 3. Test case matrix theo contract

| Chủ đề | Cases bắt buộc | Assertions hữu ích |
| --- | --- | --- |
| Controlled | Parent accepts/rejects value, prop update, same-value | Display/state theo prop; callback một lần |
| Uncontrolled | Default, user change, default prop changes, reset | Default chỉ initial; reset contract đúng |
| Native forms | name/value/required/disabled/fieldset/form attr | FormData, native validity và label focus |
| Events | preventDefault, nested handlers, keyboard/pointer | Internal action cancellation và callback order |
| Refs | Object/callback/cleanup, React18/19, unmount | Node đúng, cleanup không leak |
| Collections | Insert/remove/reorder/disabled/all-disabled | Valid active ID/tab stop; no stale node |
| Overlay | Nested, two roots, custom host, outside gestures | Topmost dismissal, no parent unintended close |
| Focus | Empty/dynamic content, disconnected restore target | Actual activeElement, no escape modal |
| Inert/scroll | Existing attributes/styles, nested lock | Exact restore, scroll position stable |
| SSR | Closed/open, multi-root, en/vi/theme | No hydration warning/recoverable error |
| Locale | Missing key, per-component override, count functions | Stable fallback, text meaningful |
| Styling | Plain/TW3/TW4, token inheritance, inline/slot overrides | getComputedStyle + screenshot |
| Date | Leap days, limits, locale ambiguity, timezone changes | Civil value stable; invalid not normalized silently |

Test user-observable behavior thay snapshot internals/reducer implementation line-by-line. Query ưu tiên `getByRole`/label; `data-testid` cho phép khi structural sentinel không có semantic role. Không assert generated useId literal; assert relationships. Không `waitForTimeout` để xử lý race; chờ state/role/focus/animation completion có căn cứ.

## 4. Browser và assistive technology matrix

PR: Chromium full interactions, Firefox/WebKit critical focus/keyboard cases; visual trên một image/OS/font set cố định. Nightly hoặc release: toàn bộ browser suite 3 engines. Runtime support floor khác bundled browsers latest: cần kiểm thêm Chrome111/Firefox128/Safari16.4 bằng môi trường thật/phù hợp trước khi công bố floor. Không coi Playwright WebKit mới nhất là Safari16.4.

Manual trước MVP release và sau thay đổi behavior family:

| Môi trường | Luồng tối thiểu |
| --- | --- |
| Windows + NVDA + Chrome | Form labels/errors, Checkbox/Radio, Tabs, Dialog open/tab/close |
| Windows + NVDA + Firefox | Focus order, announcements, nested Dialog |
| macOS + VoiceOver + Safari | Names/roles/states, modal boundaries, form navigation |
| iOS + VoiceOver + Safari | Touch exploration/rotor, modal background isolation, native select |
| Các phase Select/DatePicker | Thêm tất cả các môi trường trên với chọn option/ngày và errors |

Mỗi record có reviewer, ngày, OS/browser/AT version, package commit/hash, steps, expected/actual, issue IDs, verdict. Không có tester thì status pending, release gate chưa pass. Human testing không có SLA giả định; owner bố trí lịch.

## 5. Accessibility checks

Automated tags WCAG2A/AA, WCAG2.1A/AA, WCAG2.2AA nếu axe version hỗ trợ; ghi rules enabled/excluded, không blanket disable rules. Thêm keyboard/focus assertions vì axe không mô hình đủ. Mỗi exclusion phải có lý do, owner và review date; không exclude component đang fail để đạt zero.

Kiểm manual: tên/role/value có nghĩa; thứ tự đọc; không unexpected focus; ESC có đường thoát; errors đọc được; hover content dismissible/hoverable/persistent; reduced motion; forced-colors; 200% zoom và reflow; min targets; contrast text/boundaries/focus. Component customization examples cũng phải pass, đặc biệt unstyled và theme tối.

## 6. Visual testing

Pairwise matrix cho theme × locale × viewport × variant/state, cộng cases rủi ro cao cố định. Không chụp toàn Cartesian product. Baselines gồm focus-visible, invalid, disabled/loading, open overlay, long Vietnamese content, no-content/loading states. Use local/system deterministic fonts; chờ fonts/animations ổn định; test reduced-motion riêng.

Không snapshot input value động/time/random. Pixel tolerance phải nhỏ và có lý do; thay baseline cần reviewer ghi expected visual change. Snapshot pass không thay computed style assertions cho cascade/override và DOM role tests. Screenshot diff fail không tự `--update-snapshots` rồi commit.

## 7. Package testing chi tiết

1. Clean install bằng npm ci, build JS/types/CSS, validate every explicit exports target.
2. `npm pack --dry-run` allow-list: dist, README, LICENSE, CHANGELOG; không secrets/tests/assets không license.
3. Pack thật một tarball, SHA256, unpack để chạy package linters và inspect declarations/CSS.
4. Tạo temp consumer ngoài repo, không workspace/node_modules fallback; install tarball, assert resolved path và package version.
5. Vite plain CSS app, Tailwind3, Tailwind4 đúng prelude, webpack5 production; Browser smoke import root/subpaths.
6. TS5.5/6 moduleResolution Bundler/NodeNext với matching React18/19 typings; ref/type negative tests.
7. Node renderToString và browser hydrate; default-open portal policy; RSC directive interoperability fixture nếu ghi supported.
8. Measure import graph: chỉ Button không chứa Dialog/tabbable/floating/date; all-MVP size includes actual reachable code and transitives.
9. Publish chính tarball đã verified; sau publish install exact registry version và repeat representative consumer smoke.

Không chỉ `npm link`, không bundler alias source, không force-resolve dependencies từ repo để che package missing deps. Runtime deps được khai dependencies, external trong library JS; final consumer bundles include transitives đo thật. React peers luôn external.

## 8. Coverage và performance gates

Core branches ≥90%; components branches ≥85%; total lines ≥90%. Không tính docs/generated CSS/config vào coverage JS. Coverage exceptions cần rationale cho unreachable platform fallback, không ép unit fake DOM để tăng số.

Size budget Architecture §12 là canonical. Report uncompressed/minified/gzip và graph; gzip consistent level; không dùng Brotli số vendor để so với gzip budget. Timings có machine/browser/commit, warmup, 30 runs, p95, median và noise notes. Hard CI gates: bytes, missing exports, DOM leaks/listener cleanup; timing regression >20% triggers reference-machine investigation.

## 9. Definition of Done cho từng component

- [ ] Specification đã review: purpose/non-goals, public props/defaults, state transitions, semantics, slots/tokens.
- [ ] Native HTML được dùng khi phù hợp; invalid DOM và nested interactive elements không có.
- [ ] Controlled/uncontrolled/default/reset/disabled/readonly contracts có tests tương ứng.
- [ ] Ref/event semantics typed và tested; no public any; no accidental API exports.
- [ ] Keyboard/focus/dismiss/restoration đúng spec trên browser thật nếu component tương tác.
- [ ] A11y name/role/value/error/required đúng; axe zero unresolved violations; manual AT đủ cho family phức tạp.
- [ ] vi/en built-in text; fallback và locale override; no hardcoded user-facing internal English.
- [ ] Light/dark/brand/unstyled; class/style/slot overrides; long content/responsive/forced colors/reduced motion.
- [ ] SSR import/render/hydrate không error; IDs deterministic; portal behavior documented.
- [ ] No listener/timer/observer leak; size budget và no accidental cross-family dependency.
- [ ] CSS closure per component và aggregate render đúng trong packed consumer.
- [ ] Docs examples compile; all API details match artifacts; runtime limitations rõ.
- [ ] Regression cases cho bug fixes; meaningful coverage đạt ngưỡng liên quan.
- [ ] Changeset/SemVer classification; dependency ADR nếu cần; task report có evidence.

Không phải mọi static component cần manual AT riêng từng release; áp dụng risk-based matrix đã ghi. Dialog/Select/DatePicker luôn cần manual review khi behavior thay đổi.

## 10. Flaky tests và defect triage

Một test pass sau retry vẫn ghi flaky, không coi lần đầu fail là không tồn tại. Retry chỉ thu thêm diagnostics, không che instability. Quarantine cần owner/issue/expiry và không được quarantine release-critical keyboard/focus test để publish. Tìm nguyên nhân timers/fonts/race/test cleanup trước thêm delay.

Severity: blocker = không cài/build/render; critical = keyboard trap ngoài ý muốn, không thể đóng/chọn, data date sai, security exploitable; high = mất chức năng đáng kể ở supported browser/AT; medium = có workaround rõ; low = cosmetic không ảnh hưởng dùng. MVP gate không còn blocker/critical/high interaction/a11y defects.
