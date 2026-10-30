# Roadmap triển khai React UI Library dành cho Codex

Trạng thái: **kế hoạch chưa thực thi**. Không task nào dưới đây đã hoàn thành chỉ vì có tài liệu. Tham chiếu kiến trúc canonical: `ARCHITECTURE.md`. Chỉ bắt đầu triển khai khi owner giao task cụ thể hoặc phê duyệt phạm vi thực thi.

## Quy ước giao việc và kiểm chứng

- Một Task ID tương ứng một PR/commit scope có thể review; mục tiêu 2–6 giờ, tối đa 8 giờ trước khi tách checkpoint. Ước lượng không gồm thời gian chờ review/thiết bị.
- Mỗi task có **Mô tả / Yêu cầu**, **Input**, **Dependencies**, **Files**, **Output**, **Acceptance**, **Tests**, **Ước lượng**. `Files` là allow-list logic; thêm file trong đúng family được phép, sửa ngoài phạm vi phải giải thích.
- Mọi task implementation kế thừa `COMPONENT_GUIDELINES.md` và DoD. File test/docs/report của family luôn nằm trong phạm vi, kể cả khi không lặp ở từng dòng.
- Báo cáo mỗi task lưu `docs/reports/<TASK-ID>.md`: status, commit, files, commands, actual results, pending manual checks, deviations. `PASS` cần evidence; `BLOCKED` không được đổi thành `DONE` vì đã hết thời gian.
- Task cần manual verification có thể giao phần code/test trước, nhưng task chỉ hoàn thành khi evidence manual bắt buộc đã có. Agent không tự tạo bằng chứng screen reader.
- Task dependencies là điều kiện hoàn thành, không chỉ “đã bắt đầu”. Gate phase phải pass bên cạnh task dependencies.
- Không import Storybook hoặc tạo repo showcase trước G07. Không publish trong task nào ngoài P07/P12 release đã được giao rõ.

## Decision register cần lập ở P00

| ADR | Quyết định dự kiến | Phạm vi |
| --- | --- | --- |
| ADR-001 | Hybrid, MVP 17 components + provider/theme | Product/scope |
| ADR-002 | Một package, ESM, explicit exports, React support | Package/API |
| ADR-003 | Tailwind build-time, compiled CSS, layers, tokens/slots | Styling |
| ADR-004 | Native-first, typed composition, state conventions | Components |
| ADR-005 | Custom modal, document-scoped layers, tabbable adapter | Overlay |
| ADR-006 | vi/en, explicit SSR inputs, date-only model | Locale/date |
| ADR-007 | Packed-package gates và npm trusted publishing | Quality/release |
| ADR-008 | Storybook độc lập sau registry verification | Documentation |

## P00 — Research và khóa kiến trúc

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Biến đề xuất thành ADR được owner duyệt, chốt support và dependency policy; ADR-001…008 |
| Công việc / thứ tự | P00-01 → P00-02 → P00-03 |
| Files | `docs/adr/`, `docs/versions.md`, `docs/support.md`, tài liệu canonical |
| Prerequisites | Yêu cầu sản phẩm và bộ kế hoạch này; chưa cần source |
| Outputs | Decision log, support matrix, task ledger được khóa |
| Acceptance | Không còn mâu thuẫn MVP/React/CSS/locale/Storybook gates; mỗi dependency có category/license/owner |
| Tests bắt buộc | Document consistency review; version/peer evidence đối chiếu nguồn chính thức |
| Rủi ro / xử lý | Chọn version từ trí nhớ → ghi docs + registry và timestamp; chưa chạy fixture thì ghi pending |
| Ước lượng | 2–3 ngày công, độ khó vừa |
| Gate G00 | Owner chấp nhận ADR và scope; không tự coi đề xuất là kiến trúc đã phê duyệt |

### P00-01 — Xác minh version và support policy

- **Mô tả / Yêu cầu:** Kiểm docs chính thức, npm dist-tags, engines/peers, maintenance/license của stack đã chọn. Chốt Node24/npm11 exact patches, Vite8/TS6/Tailwind4 dòng phù hợp; phân biệt build versions và consumer floors. Không scaffold implementation.
- **Input:** Architecture §§3–4, SOURCES. **Dependencies:** không.
- **Files:** `docs/versions.md`, `docs/support.md`.
- **Output:** Bảng exact versions + URLs + ngày kiểm tra; browser/React/TS matrix.
- **Acceptance:** Tất cả tool được pin dự kiến, không dùng canary/next làm stable; registry/docs lệch được ghi rõ; React18 và19 được tách typings.
- **Tests:** Review dependency peer intersections; kiểm support matrix không dùng Node EOL làm default.
- **Ước lượng:** 3–5 giờ.

### P00-02 — Phê duyệt ADR và non-goals

- **Mô tả / Yêu cầu:** Viết ADR-001…008 với context, alternatives, decision, consequences, reversal criteria. Khóa “NativeSelect MVP / Select sau MVP”, CSS precedence, unstyled, Dialog risks và ESM trade-off.
- **Input:** Architecture và P00-01. **Dependencies:** P00-01.
- **Files:** `docs/adr/001-*.md`…`008-*.md`, `ARCHITECTURE.md`.
- **Output:** ADR proposal được owner review; scope table thống nhất.
- **Acceptance:** Mỗi quyết định có người chấp nhận/status; các điểm chưa duyệt được đánh dấu pending, không code vượt gate.
- **Tests:** Review API examples đối chiếu scope; kiểm không public headless hooks hoặc monorepo ngầm.
- **Ước lượng:** 4–6 giờ cộng thời gian review.

### P00-03 — Khóa task contracts và evidence templates

- **Mô tả / Yêu cầu:** Chuyển roadmap này thành ledger; tạo task report/ADR/component specification templates; define naming và commands contract. Không tạo source component placeholder.
- **Input:** ADR approved. **Dependencies:** P00-02.
- **Files:** `ROADMAP.md`, `AGENTS.md`, `docs/templates/`, các guidelines.
- **Output:** Mỗi task có status/prerequisites và report location.
- **Acceptance:** Dependency graph không cycle/missing ID; release/Storybook barrier không bypass; scope mỗi phiên rõ.
- **Tests:** Document lint và review links; duyệt ví dụ report fail/pending.
- **Ước lượng:** 2–4 giờ.

## P01 — Project Foundation và môi trường kiểm thử

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Repo một package, reproducible tools, Vite lab, CI nền; ADR-002/007 |
| Công việc / thứ tự | P01-01 → P01-02; tiếp P01-03 → P01-04 → P01-05 |
| Files | root configs, `lab/`, `tests/`, `scripts/`, `.github/workflows/ci.yml` |
| Prerequisites | G00 |
| Outputs | Build/typecheck/test skeleton chạy thật; packed fixture tối thiểu |
| Acceptance | Fresh clone `npm ci` + checks pass; no UI runtime deps ngoài approved; no Storybook |
| Tests | Unit smoke, browser smoke, declarations/module resolution, SSR import |
| Rủi ro / xử lý | Vite output directives/exports không như dự kiến → fix configuration hoặc ADR trước component work |
| Ước lượng | 4–5 ngày, vừa |
| Gate G01 | Scripts reproducible, packed artifact không lấy source ngầm |

### P01-01 — Package và strict TypeScript foundation

- **Mô tả / Yêu cầu:** Tạo package/private development repo với npm lockfile, license draft đã chốt, tsconfig strict/noUncheckedIndexedAccess/exactOptionalPropertyTypes, ESLint và Prettier. Module ESM; chưa publish.
- **Input:** P00 version/support tables. **Dependencies:** P00-03.
- **Files:** `package.json`, lockfile, `.node-version`, tsconfigs, lint/format configs.
- **Output:** Toolchain cài reproducible, type/lint scripts.
- **Acceptance:** `npm ci` không peer conflict; React peer + dev đúng; không runtime UI engine; intended TS errors được phát hiện.
- **Tests:** Lint/typecheck một fixture valid và negative TS fixture; format check.
- **Ước lượng:** 3–5 giờ.

### P01-02 — JS declarations và exports prototype

- **Mô tả / Yêu cầu:** Vite multi-entry ESM + tsc declarations; external react/react-dom mọi subpaths; prototype một fixture-only client export và pure locale export. Verify directive preservation; prototype không public API sản phẩm.
- **Input:** ADR-002. **Dependencies:** P01-01.
- **Files:** `vite.config.ts`, `tsconfig.build.json`, `scripts/validate-package.mjs`, `tests/fixtures/build-probe/`.
- **Output:** Dist paths convention và export validation script.
- **Acceptance:** Node ESM import không cần DOM; types resolve; client directive tồn tại nơi cần, pure entry không bị gắn client; build không bundle React.
- **Tests:** Inspect output, Node import, TS Bundler/NodeNext, missing-target negative case.
- **Ước lượng:** 4–6 giờ.


### P01-03 — Vite lab và browser runner

- **Mô tả / Yêu cầu:** Lab nội bộ route/case đơn giản, render fixture không Storybook; Playwright Chromium/Firefox/WebKit, deterministic viewport/font/reduced motion và screenshot directory. Lab source imports được phép, package tests thì không.
- **Input:** Build configs. **Dependencies:** P01-02.
- **Files:** `lab/`, `vite.lab.config.ts`, `playwright.config.ts`, `tests/browser/smoke.spec.ts`.
- **Output:** Dev command và browser test chạy trên lab.
- **Acceptance:** Browser startup/teardown sạch; không arbitrary sleeps; assertion bằng role; có failure screenshot/trace.
- **Tests:** Smoke 3 engines, mobile viewport case, negative assertion chứng minh CI fail đúng.
- **Ước lượng:** 3–5 giờ.

### P01-04 — Unit/component/type test infrastructure

- **Mô tả / Yêu cầu:** Vitest projects cho pure logic và jsdom; Testing Library setup/user-event, coverage; type test compile bằng tsc. Không mock focus để dùng thay browser tests.
- **Input:** Testing strategy. **Dependencies:** P01-03.
- **Files:** Vitest config, `tests/setup/`, `tests/types/`, coverage config.
- **Output:** Unit, component và type scripts rõ ràng.
- **Acceptance:** async assertions không leak; cleanup mỗi case; negative type checks dùng expect-error có lý do; coverage không gồm fixtures.
- **Tests:** Một sample mỗi project; unhandled rejection làm fail.
- **Ước lượng:** 3–5 giờ.

### P01-05 — Tarball smoke và PR CI nền

- **Mô tả / Yêu cầu:** Pack prototype vào temp outside repo, install vào React fixture, validate resolved path; CI minimum lint/type/unit/browser/build/pack. Chưa có publish credentials/job.
- **Input:** Build + lab/test infrastructure. **Dependencies:** P01-02, P01-04.
- **Files:** `tests/package/`, `scripts/test-package.mjs`, `.github/workflows/ci.yml`.
- **Output:** Package smoke tái sử dụng suốt dự án.
- **Acceptance:** Không workspace/file symlink bypass tarball; source code không được fixture resolve; failure artifact lưu được.
- **Tests:** Xóa một export target giả phải làm test fail; clean install smoke.
- **Ước lượng:** 4–6 giờ.

## P02 — Design Tokens, CSS và Localization

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Brand contract, CSS compilation/override, vi/en từ đầu; ADR-003/006 |
| Công việc / thứ tự | P02-01 → P02-02 → P02-03; P02-04; P02-05 tích hợp |
| Files | `src/tokens`, `src/styles`, `src/locales`, `src/providers`, styling fixtures |
| Prerequisites | G01 |
| Outputs | Generated tokens, CSS pipeline, locale/provider và scoped themes |
| Acceptance | Light/dark và 2 custom scopes; no Preflight leak; stable SSR locale |
| Tests | Token schema, contrast, CSS layers, nested themes, locale fallback, SSR |
| Rủi ro / xử lý | Inherited aliases/portal theme lệch → scope remapping và documented host contract |
| Ước lượng | 5–7 ngày, vừa đến cao |
| Gate G02 | Consumer không Tailwind dùng CSS build được; override contract pass |

### P02-01 — Token schema và light/dark values

- **Mô tả / Yêu cầu:** Reference/semantic/component token schema, deterministic generation, units, naming; thiết kế neutral default, light/dark và focus. Public component tokens dùng fallback, không shadow override từ ancestor.
- **Input:** ADR-003, token tables. **Dependencies:** P01-05.
- **Files:** `src/tokens/`, `src/styles/tokens.css`, `docs/tokens.md`, generator.
- **Output:** Typed token source + generated CSS/inventory.
- **Acceptance:** Không missing/circular reference; colors pass contrast mục tiêu; generated files có header và không sửa tay.
- **Tests:** Schema/reference tests, light/dark contrast test cho pairs thực sự dùng, reproducible output hash.
- **Ước lượng:** 4–6 giờ.

### P02-02 — Tailwind compiled CSS pipeline

- **Mô tả / Yêu cầu:** PostCSS/Tailwind @reference/@apply; scoped semantic selectors; compile aggregate và closure per-component prototype. Không ship raw directives hoặc global resets.
- **Input:** Token CSS, build prototype. **Dependencies:** P02-01.
- **Files:** `scripts/build-css.mjs`, PostCSS config, `src/styles/{layers,structural,styles}.css`, style manifest.
- **Output:** CSS có thể import vào plain consumer.
- **Acceptance:** No unscoped element/universal selector, no unresolved --tw variables/directives; closure includes tokens + own structural deps.
- **Tests:** CSS AST assertions; two-build determinism; computed styles in plain browser fixture.
- **Ước lượng:** 4–6 giờ.

### P02-03 — Override compatibility fixtures

- **Mô tả / Yêu cầu:** Fixtures plain CSS, Tailwind3, Tailwind4; verify layer prelude phải đầu tiên, unlayered overrides và inline styles; document class order không có quyền ưu tiên.
- **Input:** CSS output. **Dependencies:** P02-02.
- **Files:** `tests/fixtures/{plain-css,tailwind3,tailwind4}/`, `tests/browser/cascade.spec.ts`, styling guide.
- **Output:** Consumer recipes và automated cascade evidence.
- **Acceptance:** Token/class/slot-style overrides thắng theo documented rules; sai import order bị nhận diện trong fixture expectation, không quảng cáo tự sửa được.
- **Tests:** getComputedStyle assertions, disabled/focus states và reset leakage check ngoài library root.
- **Ước lượng:** 4–6 giờ.

### P02-04 — Locale schema và dictionaries

- **Mô tả / Yêu cầu:** en fallback, vi dictionary, typed message functions, nested fallback, Intl formatting cache bounded, extension contract. Chỉ thêm messages đang có consumers/spec, không toàn bộ dịch app.
- **Input:** Locale architecture. **Dependencies:** P01-04.
- **Files:** `src/locales/{types,en,vi,index}.ts`, locale unit/type tests.
- **Output:** Locale data tree-shakeable và fallback helpers.
- **Acceptance:** Missing nested key fallback chính xác; empty explicit string policy documented; unsupported locale không crash; third locale typecheck.
- **Tests:** vi/en completeness, count interpolation, frozen deterministic formatting, no DOM import.
- **Ước lượng:** 3–5 giờ.

### P02-05 — UIProvider và ThemeScope contracts

- **Mô tả / Yêu cầu:** Tách React locale/config context với DOM theme scope. Explicit dir/theme/density, reserved portal host registration để P05 nối vào; CSS maps re-evaluate semantic aliases ở từng scope.
- **Input:** CSS recipes và locales. **Dependencies:** P02-03, P02-04.
- **Files:** `src/providers/`, `lab/src/cases/provider.tsx`, provider docs/tests.
- **Output:** Family provider/theme với SSR-safe first render.
- **Acceptance:** Nested locale override không đổi sibling; theme không gây JS rerender từng control; server/client markup identical; no localStorage/navigator trong render.
- **Tests:** renderToString/hydrate, nested scopes + reference token override, default en, explicit vi, system-theme CSS.
- **Ước lượng:** 4–6 giờ.

## P03 — Core Primitives

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Các hợp đồng state/events/refs/form/collection dùng lại; ADR-004 |
| Công việc / thứ tự | P03-01 → P03-02 → P03-03 → P03-04 → P03-05 → P03-06 |
| Files | `src/core/`, unit/browser fixtures, primitive contracts |
| Prerequisites | G01; G02 cho integration scenarios |
| Outputs | Primitives internal, không public `/core` |
| Acceptance | StrictMode cleanup; callback counts; native form contracts; dynamic collection |
| Tests | Unit branches ≥90%; browser ref/focus/form cases |
| Rủi ro / xử lý | Trừu tượng quá sớm → chỉ xây primitives cần cho MVP, không generic event bus |
| Ước lượng | 5–7 ngày, cao |
| Gate G03 | Primitives có contract tests; không tồn tại runtime global user state |

### P03-01 — Controlled state contract

- **Mô tả / Yêu cầu:** useControllableState, undefined phân mode, explicit empty/null, Object.is equality, functional updates, latest callbacks; warning mode switch; callback ngoài state updater để tránh StrictMode double side effects.
- **Input:** State spec. **Dependencies:** P02-05.
- **Files:** `src/core/state/`, state tests.
- **Output:** Hook internal dùng cho Checkbox/Tabs/Dialog.
- **Acceptance:** Parent từ chối update thì controlled value không drift; defaultValue đổi không reset; callback đúng một lần; consecutive updates không stale.
- **Tests:** Controlled/uncontrolled, same-value, functional batched, StrictMode, mode-switch negative.
- **Ước lượng:** 4–6 giờ.

### P03-02 — Events và ref composition

- **Mô tả / Yêu cầu:** Compose user-before-internal handlers với cancelable actions; callback/object refs và cleanup; không clone element refs hoặc override onChange tùy tiện.
- **Input:** Event/ref API guidelines. **Dependencies:** P03-01.
- **Files:** `src/core/events/`, `src/core/refs/`, React18/19 type tests.
- **Output:** Helpers nhỏ typed theo actual usage.
- **Acceptance:** preventDefault chặn action được phép; unmount cleanup luôn chạy; external/internal refs cùng nhận node và null/cleanup chính xác.
- **Tests:** Native/synthetic event cases, callback throws cleanup policy, React19 cleanup ref, React18 fallback, StrictMode.
- **Ước lượng:** 4–6 giờ.

### P03-03 — IDs và accessibility associations

- **Mô tả / Yêu cầu:** useId wrapper chỉ khi có giá trị, explicit ID precedence, merge describedby unique tokens; visually-hidden structural utility. Không tự lấy placeholder làm name.
- **Input:** SSR/a11y specs. **Dependencies:** P03-02.
- **Files:** `src/core/ids/`, `src/core/a11y/`, structural CSS, SSR fixtures.
- **Output:** Stable name/description ID building blocks.
- **Acceptance:** Multiple roots có identifierPrefix recipe; no dangling describedby từ optional content; no random IDs.
- **Tests:** SSR hydrate exact associations, duplicate tokens, custom IDs, hidden text visible in accessibility tree.
- **Ước lượng:** 3–5 giờ.

### P03-04 — Form ownership và reset bridge

- **Mô tả / Yêu cầu:** Native input reset behavior, form attribute association, disabled fieldset và custom-state reconciliation; reset reason callback cho controlled custom controls. Không custom dispatch synthetic React change để giả onChange.
- **Input:** State/events contracts. **Dependencies:** P03-01, P03-02.
- **Files:** `src/core/form/`, `lab/src/cases/form-contract.tsx`, browser tests.
- **Output:** Internal reset subscription helper và documented native/custom split.
- **Acceptance:** Uncontrolled reset về initial default; controlled vẫn do parent quyết; listener theo form owner cleanup; form moved/owner changed đúng.
- **Tests:** FormData, external form attribute, reset, disabled fieldset, multiple forms.
- **Ước lượng:** 4–6 giờ.

### P03-05 — Collection registry

- **Mô tả / Yêu cầu:** Register/remove stable item keys, DOM order và disabled metadata; reorder/remove xử lý đúng không dùng mount order làm thứ tự. Chỉ internal typed collections cho Tabs trước.
- **Input:** Tabs specification. **Dependencies:** P03-02, P03-03.
- **Files:** `src/core/collection/`, registry tests.
- **Output:** Collection model dùng cho roving focus.
- **Acceptance:** Disabled/hidden items policy explicit; reorder không stale; cleanup StrictMode không duplicate; missing/duplicate key dev warning.
- **Tests:** Dynamic insert/remove/reorder, fragments, nested content, StrictMode mount/unmount.
- **Ước lượng:** 4–6 giờ.

### P03-06 — Roving focus và keyboard helpers

- **Mô tả / Yêu cầu:** One tab stop, orientation/dir, Home/End, skip disabled, fallback khi focused item mất; không xử lý keys khi composition hoặc modifier không thuộc contract.
- **Input:** Collection registry. **Dependencies:** P03-05.
- **Files:** `src/core/collection/roving-focus.ts`, keyboard helpers, Playwright cases.
- **Output:** Tested focus navigation substrate.
- **Acceptance:** Arrows không scroll page khi đã dùng; không block editing input; một enabled tab stop kể cả dynamic removal; all-disabled state defined.
- **Tests:** Real browser keyboard sequence, RTL basis, vertical, removal focused item, IME guard.
- **Ước lượng:** 4–6 giờ.

## P04 — Base, Form và Tabs Components

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | 16 MVP visual/interaction components ngoài Dialog; ADR-003/004 |
| Công việc / thứ tự | Static/feedback/Field → Button/Input/native fields → checkbox/radio/switch → Tabs |
| Files | `src/components/`, lab cases, tests, API docs/exports/style manifest |
| Prerequisites | G02 và G03 |
| Outputs | Component APIs và customization examples có kiểm chứng |
| Acceptance | Mọi family có states matrix, form/SSR/a11y và type tests phù hợp |
| Tests | DoD từng family; browser form/keyboard; theme visual checks |
| Rủi ro / xử lý | API props lệch nhau → specification trước implementation và API inventory review |
| Ước lượng | 8–11 ngày, vừa đến cao |
| Gate G04 | Tất cả families ngoài Dialog qua DoD; không task “done” chỉ có JSX đẹp |

### P04-01 — Text, Badge, Card và Table semantic

- **Mô tả / Yêu cầu:** Implement các wrappers ít behavior; Text allow-list semantic tags giới hạn; Card không tự ép heading; Table native parts/caption/header scope; không sort/filter/virtualization.
- **Input:** Catalog + tokens. **Dependencies:** P03-03, P02-03.
- **Files:** Four component folders, exports/CSS manifest, static lab cases.
- **Output:** 4 simple families với typed props/ref và docs.
- **Acceptance:** Không role thừa; class/style overrides; table caption/header associations; variants không đổi semantics.
- **Tests:** DOM semantics/type/ref, light/dark screenshots, responsive table overflow.
- **Ước lượng:** 5–6 giờ; tách theo family nếu vượt scope.

### P04-02 — Spinner, Alert và EmptyState

- **Mô tả / Yêu cầu:** Feedback primitives và fallback locale; Spinner decorative khi nằm trong Button, standalone có label; Alert static mặc định, live announcement opt-in; EmptyState không chứa business copy cứng.
- **Input:** Locale/token contracts. **Dependencies:** P02-05, P03-03.
- **Files:** `components/{spinner,alert,empty-state}/`, feedback lab/docs.
- **Output:** 3 feedback/display families.
- **Acceptance:** Reduced motion; no duplicate live announcements; vi/en meaningful text; optional action không có nested button.
- **Tests:** Accessibility tree, label override, locale fallback, motion screenshots.
- **Ước lượng:** 4–6 giờ.

### P04-03 — Field family

- **Mô tả / Yêu cầu:** Field/FieldLabel/FieldDescription/FieldError; explicit associations, typed props, no arbitrary child cloning or hidden validation. Field invalid style không tự gắn ARIA cho child.
- **Input:** IDs + a11y convention. **Dependencies:** P03-03.
- **Files:** `components/field/`, form lab/docs.
- **Output:** Layout/name/error building blocks.
- **Acceptance:** Label target đúng; optional description không dangling ID; error text accessible; horizontal/mobile layout không tràn.
- **Tests:** Name/description queries, invalid associations example, responsive/theme visual.
- **Ước lượng:** 3–5 giờ.

### P04-04 — Button

- **Mô tả / Yêu cầu:** Native button, default type button; variants/tone/size; startIcon/endIcon slots; disabled native, loading focus-preserving activation guard; không polymorphic/asChild.
- **Input:** API §7.3, Spinner. **Dependencies:** P04-02, P03-02.
- **Files:** `components/button/`, button CSS/docs/lab/tests, exports.
- **Output:** Button complete contract.
- **Acceptance:** No submit by default, exactly once click, loading không activate bằng keyboard/pointer; named icon-only; slot overrides stable.
- **Tests:** Native form submit/default type, keyboard/disabled/loading, ref, custom CSS, packed JS size.
- **Ước lượng:** 4–6 giờ.

### P04-05 — Input

- **Mô tả / Yêu cầu:** Single native input DOM, forward native props/ref; controlSize không đè size; invalid semantics; controlled/uncontrolled native onChange; không auto formatting/debounce.
- **Input:** Input API, Field. **Dependencies:** P04-03, P03-04.
- **Files:** `components/input/`, typing/IME/form lab cases.
- **Output:** Input native behavior preserved.
- **Acceptance:** Cursor/selection không nhảy; readonly/disabled distinct; autocomplete/name/size pass-through; explicit accessible label.
- **Tests:** User typing + composition, reset/autofill manual check, controlled typing, ref focus, SSR.
- **Ước lượng:** 4–6 giờ.

### P04-06 — Textarea và NativeSelect

- **Mô tả / Yêu cầu:** Native elements dùng control style contract; Textarea không autosize v1; NativeSelect chỉ single-select UI v1, không custom popup hoặc searchable flags.
- **Input:** Input contract. **Dependencies:** P04-05.
- **Files:** `components/{textarea,native-select}/`, native form cases.
- **Output:** Two native entry families.
- **Acceptance:** Native size giữ ý nghĩa; no API advertise custom listbox; required/reset/form submission đúng; long vi labels không hỏng control.
- **Tests:** Selected option FormData, textarea multiline/selection, disabled/required, browser keyboard and mobile select smoke.
- **Ước lượng:** 4–6 giờ.

### P04-07 — Checkbox

- **Mô tả / Yêu cầu:** Native checkbox input với visual decoration; checked/defaultChecked/onCheckedChange, mixed via indeterminate; sync native state và form reset. Native onChange được gọi trước custom callback; preventDefault policy documented.
- **Input:** State/form specs. **Dependencies:** P03-04, P04-03.
- **Files:** `components/checkbox/`, form tests/docs.
- **Output:** Checkbox boolean/mixed contract.
- **Acceptance:** Mixed không submit như checked trừ state checked thực; disabled fieldset hoạt động; label click đúng; không hai accessible controls.
- **Tests:** Space, pointer label, mixed cycles, reset, required, controlled rejection và FormData.
- **Ước lượng:** 4–6 giờ.

### P04-08 — RadioGroup và Switch

- **Mô tả / Yêu cầu:** Native radios với name/form grouping; Switch native checkbox role switch boolean-only. Reuse controller/helpers, không reuse Checkbox decorative DOM để buộc thiết kế.
- **Input:** Checkbox/form evidence. **Dependencies:** P04-07.
- **Files:** `components/{radio-group,switch}/`, browser cases.
- **Output:** 2 families với label/group semantics.
- **Acceptance:** One checked radio per group; group value contract string; Switch không nhận mixed; readonly không giả native unsupported behavior.
- **Tests:** Arrow/native radio grouping across forms, reset, disabled fieldset, keyboard/labels; TS reject mixed Switch.
- **Ước lượng:** 5–6 giờ; tách thành 2 checkpoints nếu native browser differences kéo dài.

### P04-09 — Tabs state và semantics

- **Mô tả / Yêu cầu:** Compound flat exports; state context, trigger/panel IDs, manual/automatic activation, disabled/dynamic items; no styles ngoài structural.
- **Input:** Tabs API + collection. **Dependencies:** P03-06, P03-01.
- **Files:** `components/tabs/`, transition specs/unit/browser tests.
- **Output:** Unstyled Tabs functional.
- **Acceptance:** One tab stop, selected/focused tách biệt manual mode; IDs match; hidden panel inaccessible; controlled remove behavior defined.
- **Tests:** Arrows/Home/End/Enter/Space, controlled/uncontrolled, dynamic remove, all-disabled, RTL basis.
- **Ước lượng:** 4–6 giờ.

### P04-10 — Tabs styling và MVP base integration

- **Mô tả / Yêu cầu:** Tabs underline/pills/size, panel mountStrategy, form state persistence; integrate form/card/table/feedback demo; update exports/CSS closures/API inventory.
- **Input:** All P04 outputs. **Dependencies:** P04-01, P04-02, P04-04, P04-06, P04-08, P04-09.
- **Files:** Tabs CSS/lab, `lab/src/cases/mvp-form.tsx`, public API docs, package fixtures.
- **Output:** Base MVP demo và component reports.
- **Acceptance:** Theme swap không đổi behavior; active-only documented state loss; import từng family không cần private source.
- **Tests:** Integration form in Tabs, visual states, packed exports/types, basic axe suite.
- **Ước lượng:** 4–6 giờ.

## P05 — Overlay Infrastructure và Dialog

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Reliable modal behavior tự xây, chỉ tabbable utility; ADR-005 |
| Công việc / thứ tự | Portal → manager → dismiss → focus → inert → scroll → Dialog → integration |
| Files | `src/core/{portal,overlay,focus}`, `src/adapters/tabbable.ts`, Dialog, browser cases |
| Prerequisites | G03, P04 primitives; G04 trước final integration |
| Outputs | Dialog MVP và reusable overlay substrate có contracts |
| Acceptance | Nested dialogs, restore focus, inert, scroll và scoped themes đều pass |
| Tests | Browser 3 engines, StrictMode, SSR, leak instrumentation, manual AT |
| Rủi ro / xử lý | Phần khó nhất MVP; nếu lỗi focus không ổn định thì gate đóng, không workaround timeout |
| Ước lượng | 9–13 ngày, rất cao |
| Gate G05 | Dialog integration suite pass; AT findings critical/high đã xử lý |

### P05-01 — Portal và scoped hosts

- **Mô tả / Yêu cầu:** Client-only portal mount sau hydration; ownerDocument từ container, theme scope host registration; exact cleanup; default body host cho app không provider. Không render DOM portal server.
- **Input:** Portal/theme contracts. **Dependencies:** P02-05, P03-02.
- **Files:** `src/core/portal/`, provider host integration, portal lab.
- **Output:** Portal primitive với explicit container.
- **Acceptance:** Server + first client render consistent; no orphan nodes; custom host đúng document/theme; default-open content xuất hiện sau mount đúng docs.
- **Tests:** SSR/hydrate closed/open, StrictMode, scope switching, host unmount, iframe same-document ownership case.
- **Ước lượng:** 4–6 giờ.

### P05-02 — Document overlay manager

- **Mô tả / Yêu cầu:** Lazy WeakMap theo Document; layers IDs, parent ownership/branches, topmost resolution, subscriptions. Không registration tại import; nhiều roots cùng package phối hợp.
- **Input:** Overlay ownership model. **Dependencies:** P05-01.
- **Files:** `src/core/overlay/manager.ts`, manager tests, ownership spec.
- **Output:** Stack có cleanup và parent/child queries.
- **Acceptance:** Nested layer tháo ngược thứ tự vẫn không stale; disposed manager không giữ nodes; no app user state; independent documents không ảnh hưởng nhau.
- **Tests:** Register/unregister permutations, StrictMode, multi-root, unmount parent with child.
- **Ước lượng:** 4–6 giờ.

### P05-03 — Dismiss interactions

- **Mô tả / Yêu cầu:** Escape và outside pointer chỉ top eligible layer; parent nhận owned portal branches; pointerdown origin tracking; no broad stopPropagation. Emit reason đã định nghĩa.
- **Input:** Manager, event composition. **Dependencies:** P05-02, P03-02.
- **Files:** `src/core/overlay/dismiss.ts`, browser ownership cases.
- **Output:** Dismissable layer contract.
- **Acceptance:** Drag từ content ra ngoài không dismiss; Escape đóng một lớp; click trong child không đóng parent; canceled user action không đổi open.
- **Tests:** Mouse/touch/pointercancel, nested portals, key composition guard, two roots, callback counts.
- **Ước lượng:** 4–6 giờ.

### P05-04 — Focus scope và restoration

- **Mô tả / Yêu cầu:** Thêm approved tabbable adapter; own focus loop/restore; initialFocusRef, empty dialog fallback, programmatic focus escape, child layer ownership. Không tắt visibility check trong production.
- **Input:** Focus spec + manager. **Dependencies:** P05-02, P03-02.
- **Files:** `src/core/focus/`, `src/adapters/tabbable.ts`, dependency record, browser tests.
- **Output:** Modal focus scope và measured dependency cost.
- **Acceptance:** Tab/ShiftTab không thoát, hidden/disabled không nhận focus; close restore trigger hoặc fallback; removed target không throw; child close restore parent đúng.
- **Tests:** Real browser dynamic elements, radio groups, fieldset/inert, no focusable content, trigger unmount, ref cleanup.
- **Ước lượng:** 6–8 giờ; checkpoint basic trap rồi dynamic restoration.

### P05-05 — Inert background management

- **Mô tả / Yêu cầu:** Inert sibling branches ngoài active modal theo DOM path; preserve original inert/aria-hidden where used; refcounts; nested modal parent temporarily inert; dynamic added background nodes được xử lý.
- **Input:** Layer/focus model. **Dependencies:** P05-02, P05-04.
- **Files:** `src/core/overlay/inert.ts`, browser inert fixtures.
- **Output:** Background isolation lifecycle.
- **Acceptance:** Không inert ancestor chứa active modal; không aria-hide focused subtree; values gốc restore chính xác sau nested close/unmount; no leaked observer.
- **Tests:** Nested hosts, preexisting inert, multi-root DOM, background node insertion, mutation cleanup.
- **Ước lượng:** 5–7 giờ.

### P05-06 — Scroll lock

- **Mô tả / Yêu cầu:** Refcount locks, scrollbar compensation, preserve inline styles/scroll position; iOS viewport/touch cases; avoid wheel/touch listeners chặn scroll content bên trong.
- **Input:** Modal manager. **Dependencies:** P05-02.
- **Files:** `src/core/overlay/scroll-lock.ts`, scroll fixtures/manual checklist.
- **Output:** Modal scroll containment với restore chính xác.
- **Acceptance:** Background không scroll; dialog long content scroll; nested close không unlock sớm; page không nhảy sau close.
- **Tests:** Desktop scrollbar, fixed header fixture, mobile viewport; manual Safari/iOS; 100 cycles cleanup.
- **Ước lượng:** 5–7 giờ, chưa gồm chờ thiết bị.

### P05-07 — Dialog public composition và styles

- **Mô tả / Yêu cầu:** Flat compound parts, title/description, trigger render adapter typed, dismissPolicy, controlled/uncontrolled open; semantic DOM bảo vệ role/IDs. Default animations chỉ CSS, reduced motion.
- **Input:** API §7.6 và primitives. **Dependencies:** P05-03, P05-04, P05-05, P05-06, P04-04.
- **Files:** `components/dialog/`, public exports/types/CSS closure, Dialog docs/lab.
- **Output:** Styled/unstyled Dialog với custom Button trigger example.
- **Acceptance:** Missing title warning; type-safe refs/adapter; no nested button; default close route rõ; same behavior styled/unstyled.
- **Tests:** API types, open callbacks/reasons, Escape/outside policies, name/description, visual light/dark.
- **Ước lượng:** 5–7 giờ.

### P05-08 — Dialog integration và AT review

- **Mô tả / Yêu cầu:** Form/Tabs trong Dialog, nested Dialog, two themes/two roots, default-open SSR, trigger removed, long content và zoom. Chạy manual matrix cùng người kiểm tra có công cụ phù hợp.
- **Input:** Complete MVP and Dialog. **Dependencies:** P05-07, P04-10.
- **Files:** `tests/browser/dialog-integration.spec.ts`, `docs/a11y/dialog.md`, leak/perf reports.
- **Output:** Integrated behavior evidence và defect list đã xử lý.
- **Acceptance:** 0 blocker/critical/high interaction/a11y issue; measured size/listener budgets; không thay pending manual bằng automated pass.
- **Tests:** Chromium/Firefox/WebKit + NVDA/VoiceOver matrix, axe, forced colors, zoom/reduced motion, 100 cycles.
- **Ước lượng:** 6–8 giờ active QA; defect fixes tách task nếu lớn.

## P06 — Testing và Release Hardening

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Chứng minh API/package/interaction contract trên consumer thật; ADR-007 |
| Công việc / thứ tự | Package matrix → SSR/types → a11y → visual → performance → final audit |
| Files | `tests/fixtures`, package/browser tests, `docs/reports`, CI |
| Prerequisites | G04 và G05 |
| Outputs | Release candidate quality report, all gates measurable |
| Acceptance | Full MVP DoD không pending critical evidence |
| Tests | Packed tarball, React/TS/browser matrix, visual, manual AT, performance |
| Rủi ro / xử lý | Local-source tests false confidence → isolated tarball, assert resolution and no leaked dev dependency |
| Ước lượng | 8–11 ngày, cao |
| Gate G06 | Release readiness kỹ thuật pass; còn metadata/owner/publish ở P07 |

### P06-01 — Full package consumer matrix

- **Mô tả / Yêu cầu:** Install cùng tarball vào plain Vite, Tailwind3/4, webpack5; validate every export/CSS closure; publint/ATTW; no postinstall/network runtime. Package type/style paths là source of truth.
- **Input:** Built MVP. **Dependencies:** P05-08, P01-05.
- **Files:** `tests/package/`, consumer fixtures, `scripts/validate-package.mjs`.
- **Output:** Matrix report theo tarball hash.
- **Acceptance:** All documented imports resolve; CSS giữ lại sau prod build; no duplicated React; root/subpaths consistent.
- **Tests:** Consumer prod build + browser smoke; break export/CSS path negative test; `npm ls` runtime tree.
- **Ước lượng:** 5–7 giờ.

### P06-02 — React, typings, SSR và RSC boundary audit

- **Mô tả / Yêu cầu:** React18.3.1 + 19.2 floor/latest stable matching types; TS5.5/6; NodeNext/Bundler. SSR render/hydrate cases và minimal RSC interoperability fixture cho directives, không đổi core thành framework-specific.
- **Input:** Packed artifact. **Dependencies:** P06-01.
- **Files:** `tests/types/`, `tests/fixtures/{ssr,rsc}/`, support report.
- **Output:** Compatibility evidence với exact versions.
- **Acceptance:** No recoverable hydration errors; no DOM during import; declarations không source alias; no “use client” loss; no implicit any leaks.
- **Tests:** Ref callbacks cleanup, controlled forms, multi-root IDs, open portal first render, locale/theme mismatch negative fixture.
- **Ước lượng:** 5–7 giờ.

### P06-03 — Accessibility toàn MVP

- **Mô tả / Yêu cầu:** Audit từng family và sample composition theo WCAG applicable criteria; automation + keyboard + manual AT. Ghi N/A có lý do, limitations có scope; không tuyên bố app consumer conformant.
- **Input:** Family DoDs, Dialog AT. **Dependencies:** P06-02.
- **Files:** `docs/a11y/matrix.md`, `tests/accessibility/`, bug regressions.
- **Output:** A11y release report và issue resolutions.
- **Acceptance:** No unresolved violations trong approved examples; all core user journeys usable bằng keyboard/screen reader; baseline browser manual report có thật.
- **Tests:** NVDA Chrome/Firefox, VoiceOver Safari/macOS/iOS; 320px reflow, contrast, errors/required, target size.
- **Ước lượng:** 6–8 giờ active audit + lịch QA thực tế.

### P06-04 — Visual regression và customization contracts

- **Mô tả / Yêu cầu:** Stable screenshot fixtures cho default/brand/unstyled, light/dark, density và vi/en; pairwise matrix thay Cartesian explosion. Review CSS cascade/portal inheritance cùng screenshots.
- **Input:** All public families. **Dependencies:** P06-03, P02-03.
- **Files:** `tests/visual/`, baselines, customization examples.
- **Output:** Reviewed baselines + override regression suite.
- **Acceptance:** Không auto accept snapshots; no clipping/truncated error labels; focus/disabled/invalid states distinguishable; two scoped themes đúng.
- **Tests:** 320/768/1280 viewports; long Vietnamese labels; component token ancestor overrides; CSS computed values.
- **Ước lượng:** 4–6 giờ.

### P06-05 — Bundle, render và cleanup budgets

- **Mô tả / Yêu cầu:** Reproducible min+gzip fixtures including transitives; no React; root/subpath dead-code checks, CSS closure costs; profiler và 100-cycle overlay listener test.
- **Input:** Budget table. **Dependencies:** P06-04.
- **Files:** `scripts/measure-bundles.mjs`, `tests/performance/`, budget JSON, reports.
- **Output:** Measured baseline kèm machine/browser versions.
- **Acceptance:** Tất cả hard size/leak gates pass; timing regressions giải thích được; Button không kéo overlay; không nâng budget để né fail.
- **Tests:** Production builds, 30 samples warmup/p95, retained orphan nodes/listeners assertions.
- **Ước lượng:** 4–6 giờ.

### P06-06 — API/docs/dependency audit và RC gate

- **Mô tả / Yêu cầu:** Compile every documented snippet; public API inventory diff, dependency licenses/security, README clean-room quickstart và known limitations. Đối chiếu checklist Architecture §19.
- **Input:** All P06 reports. **Dependencies:** P06-01…P06-05.
- **Files:** `README.md`, component docs, `docs/public-api.md`, `docs/reports/rc-readiness.md`.
- **Output:** Release candidate report, exact unresolved items if any.
- **Acceptance:** Không placeholder invalid APIs; all facts match build; zero missing export/type doc; owner nhìn thấy complete reviewable artifact.
- **Tests:** Copy-paste quickstart clean app, snippet TS compile, audit runtime graph and tarball contents.
- **Ước lượng:** 4–6 giờ.

## P07 — npm Release đầu tiên

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Publish `0.1.0` kiểm chứng được, không chỉ local build; ADR-007 |
| Công việc / thứ tự | Metadata/docs → release workflow dry run → authorized publish → registry smoke |
| Files | package metadata, Changesets, release workflow, changelog/readiness report |
| Prerequisites | G06; owner có quyền npm scope/repository và license đã chốt |
| Outputs | Published immutable package + provenance/auth evidence + independent install report |
| Acceptance | Registry exact version usable; README/changelog đúng; artifact traceable to commit |
| Tests | Exact tarball verification, dry run, postpublish registry smoke |
| Rủi ro / xử lý | First package bootstrap/OIDC constraints → owner-assisted documented path; no secret hacks |
| Ước lượng | 3–4 ngày, vừa, chưa tính quyền truy cập |
| Gate G07 | P07-04 pass mới cho phép P08 |

### P07-01 — Metadata và contributor documentation

- **Mô tả / Yêu cầu:** Resolve real scope/name/repo URLs/author/license; README install/styles/support; CONTRIBUTING/SECURITY/CODE_OF_CONDUCT; Changesets config và release notes. Không tự tạo tên owner/license holder.
- **Input:** RC report + owner choices. **Dependencies:** P06-06.
- **Files:** package metadata, README/LICENSE/CHANGELOG, contributor/security docs, `.changeset/`.
- **Output:** Publish-ready metadata/docs.
- **Acceptance:** Package name availability/quyền sở hữu verified; no placeholder URLs; scopes public access policy explicit; all public changes documented.
- **Tests:** npm pack dry-run allow-list, link/docs validation, metadata assertions.
- **Ước lượng:** 3–5 giờ.

### P07-02 — Release pipeline và exact-artifact rehearsal

- **Mô tả / Yêu cầu:** Protected release job, OIDC id-token minimum permission, pinned actions, no long-lived broad token; build/test/pack/hash → publish cùng tarball. Check npm version và dist-tag authorization riêng.
- **Input:** Owner repository/npm configuration. **Dependencies:** P07-01.
- **Files:** `.github/workflows/release.yml`, release runbook, artifact manifest.
- **Output:** Dry-run pipeline và concrete release artifact.
- **Acceptance:** Publish disabled trong dry run; artifact hash retained; không rebuild khác sau verification; fork PR không có release credentials.
- **Tests:** Pipeline rehearsal, invalid version/tag guard, artifact integrity, provenance prerequisites.
- **Ước lượng:** 4–6 giờ.

### P07-03 — Publish được ủy quyền

- **Mô tả / Yêu cầu:** Chỉ khi owner đã giao quyền phát hành: publish RC nếu cần, verify, rồi stable tarball từ stable release commit đã kiểm thử; không publish từ working tree bẩn. Bootstrap 2FA khi OIDC chưa thể cấu hình được ghi runbook.
- **Input:** Reviewed exact tarball + release authorization. **Dependencies:** P07-02.
- **Files:** Release report/changelog/tag; không sửa component source trong publish step.
- **Output:** npm version đã publish, commit/hash/tag links.
- **Acceptance:** Registry metadata/version/access đúng; failed publish không retry tạo version mới tùy tiện; no secret trong log.
- **Tests:** npm view metadata/integrity; inspect published tarball vs intended artifact; publish status confirmed.
- **Ước lượng:** 2–4 giờ, có thể blocked bởi auth.

### P07-04 — Independent registry consumer verification

- **Mô tả / Yêu cầu:** Fresh project ngoài repo install exact stable version từ registry; render toàn MVP, styles, types, SSR và representative user flows; không dùng local tarball/source alias.
- **Input:** Published version. **Dependencies:** P07-03.
- **Files:** `docs/reports/registry-smoke.md`, smoke fixture instructions, release checklist.
- **Output:** G07 pass/fail evidence.
- **Acceptance:** Lockfile/resolution chứng minh npm origin; UI and CSS work; no missing deps; failure thì close G07 và repair release.
- **Tests:** Production build, types, form/Dialog/Tabs flow, browser console/hydration check.
- **Ước lượng:** 3–5 giờ.

## P08 — Independent Storybook Project

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Playground/documentation từ npm package thật; ADR-008 |
| Công việc / thứ tự | G07 → repo foundation → stories → content → CI → update/deploy plan |
| Files | Repository mới `ui-showcase`, không source library |
| Prerequisites | G07; xác minh stable Storybook/React-Vite versions tại thời điểm bắt đầu |
| Outputs | Storybook build usable vi/en, theme/state demos, registry-origin assertion |
| Acceptance | No workspace/source alias, no links; exact package version shown |
| Tests | Story interactions/a11y/visual, production static build, dependency origin |
| Rủi ro / xử lý | Docs source drift → examples compiled against installed exact version |
| Ước lượng | 5–8 ngày, vừa |
| Gate G08 | Full MVP story pack + CI pass; website deploy chỉ nếu được giao |

### P08-01 — Repository và installed package foundation

- **Mô tả / Yêu cầu:** Create standalone private package project, own lockfile; stable Storybook React-Vite, exact @scope/ui from registry, styles import. No import source library; no feature additions vào library từ repo docs.
- **Input:** Registry-smoke report. **Dependencies:** P07-04.
- **Files:** Showcase root, `.storybook/main.ts`, package/lockfile, CI origin test.
- **Output:** Storybook boots/builds with one published Button example.
- **Acceptance:** Resolved package ở node_modules registry path; zero workspace/file/alias refs; version banner matches lockfile.
- **Tests:** Build-storybook + basic browser visit; fail origin assertion for synthetic forbidden alias.
- **Ước lượng:** 3–5 giờ.

### P08-02 — Decorators và story templates

- **Mô tả / Yêu cầu:** Theme/locale/density/viewport toolbar; portal host trong preview iframe; story templates cho static/native/stateful families. Dùng Button và provider để kiểm decorators trước, chưa tạo toàn bộ stories.
- **Input:** Architecture §15, published API docs. **Dependencies:** P08-01.
- **Files:** `.storybook/preview.tsx`, themes, `stories/templates/`, Button/provider sample.
- **Output:** Decorators và template dùng lại cho từng batch.
- **Acceptance:** Portal inherits active theme; controls typed; không dịch user label ngầm; theme switch không remount toàn canvas không cần thiết.
- **Tests:** Sample render smoke, control interactions, theme+locale screenshot sample.
- **Ước lượng:** 3–5 giờ.

### P08-03 — Stories General, Display và Feedback

- **Mô tả / Yêu cầu:** Stories Button, Text, Badge, Card, Table, Spinner, Alert, EmptyState từ published API. Áp dụng template đúng supported variants/sizes/tones; không tạo controlled state giả cho static family.
- **Input:** Story templates và published props inventory. **Dependencies:** P08-02.
- **Files:** `stories/{general,display,feedback}/`.
- **Output:** Batch stories có playground và customization.
- **Acceptance:** Loading/icon-only, table caption, static/live Alert, Spinner decorative, empty vi/en đều có example; source snippets dùng public imports.
- **Tests:** Render all stories, accessible names, theme/state screenshots sample; TypeScript compile.
- **Ước lượng:** 4–6 giờ.

### P08-04 — Stories Data Entry

- **Mô tả / Yêu cầu:** Field/Input/Textarea/NativeSelect/Checkbox/RadioGroup/Switch; controlled/uncontrolled, native forms/reset, errors và long Vietnamese labels. Không đưa custom Select chưa publish vào docs.
- **Input:** Story templates và form contracts. **Dependencies:** P08-02.
- **Files:** `stories/entry/`.
- **Output:** Native form story pack.
- **Acceptance:** Controls không phá React controlled mode; mixed Checkbox nhưng không mixed Switch; readOnly/disabled/invalid được phân biệt; form submit hiển thị FormData thật.
- **Tests:** Story play typing/select/reset, label/error associations, keyboard smoke, snippet compile.
- **Ước lượng:** 4–6 giờ.

### P08-05 — Stories Tabs, Dialog và scoped provider

- **Mô tả / Yêu cầu:** Tabs manual/automatic/vertical/dynamic/persistence; Dialog nested/long content/initial focus/trigger removed; two ThemeScopes và portal host trong canvas.
- **Input:** Story templates, interaction specifications. **Dependencies:** P08-02.
- **Files:** `stories/{navigation,overlay,providers}/`.
- **Output:** Interactive edge-case demonstrations.
- **Acceptance:** Keyboard-only stories usable; source code ghi rõ customization responsibilities; không portaled content vào manager document ngoài iframe.
- **Tests:** Play open/tab/close/restore, theme scope assertions, controlled/uncontrolled example tests.
- **Ước lượng:** 4–6 giờ.

### P08-06 — Bilingual docs và advanced examples

- **Mô tả / Yêu cầu:** Docs vi/en cài đặt, composition, override layers, form/reset, a11y, API defaults/limitations; source snippets import npm. Thêm Dialog/Tab/native form edge stories theo standard table.
- **Input:** Public docs/Story pack. **Dependencies:** P08-03, P08-04, P08-05.
- **Files:** `docs/{vi,en}/`, MDX/source examples, navigation.
- **Output:** Reader-facing documentation với locale navigation.
- **Acceptance:** Runtime locale và docs locale phân biệt; missing translation có label/fallback; examples compile; unsupported Select/DatePicker chưa xuất hiện như available.
- **Tests:** Docs link checks, snippet typecheck, vi diacritics rendering, keyboard browse.
- **Ước lượng:** 4–6 giờ.

### P08-07 — Storybook quality CI

- **Mô tả / Yêu cầu:** Add a11y addon checks, play interactions và Playwright visual regression; stable clocks/fonts/viewports; no external analytics by default. Chỉ install addon cần thiết.
- **Input:** Stories/docs. **Dependencies:** P08-06.
- **Files:** Showcase tests/configs/workflow/baselines.
- **Output:** PR preview artifact với reviewed screenshots.
- **Acceptance:** Failure a11y/interaction blocks merge; snapshots không auto accept; library package version pinned.
- **Tests:** Full static build, representative play functions, axe + visual, console errors fail.
- **Ước lượng:** 4–6 giờ.

### P08-08 — Version sync và website deployment readiness

- **Mô tả / Yêu cầu:** Update PR bot hoặc documented manual exact upgrade; validate registry origin/new version; static hosting config/base path/cache policy; concrete deploy artifact, không tự publish site nếu không được giao.
- **Input:** Passing showcase build. **Dependencies:** P08-07.
- **Files:** Update-bot config, deployment workflow/runbook, version badge.
- **Output:** Upgrade rehearsal và deployable static artifact.
- **Acceptance:** No @latest at build; new version PR runs all checks; rollback previous site artifact possible; previews không overwrite production.
- **Tests:** Upgrade known patch rehearsal; static serve with intended base path; broken-route check.
- **Ước lượng:** 3–5 giờ.

## P09 — Floating Infrastructure, Popover và Tooltip

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Mở rộng overlay có neo, tự viết interactions; dùng DOM geometry utility |
| Công việc / thứ tự | Adapter → Popover → Tooltip → integration/quality |
| Files | `src/adapters/floating.ts`, floating core, new components/tests/docs |
| Prerequisites | G07; G08 cần trước khi cập nhật docs showcase, không prerequisite cho code library |
| Outputs | Popover/Tooltip release candidate; dependency budget updated |
| Acceptance | Nested modal/popup behavior, collision và cleanup đủ matrix |
| Tests | Scroll/resize/zoom/RTL geometry; accessibility/keyboard/hover/touch |
| Rủi ro / xử lý | Chỉ “absolute top/left” không đủ → utility adapter; ARIA không auto chọn từ placement |
| Ước lượng | 7–10 ngày, cao |
| Gate G09 | Full DoD candidate pass; khi phát hành lặp P07-02…04 với version mới và quyền hiện hành; stories chỉ cập nhật sau registry verification |

### P09-01 — Floating DOM adapter

- **Mô tả / Yêu cầu:** Đánh giá exact @floating-ui/dom runtime dependency, import computePosition/offset/flip/shift/size cần thiết; own hook lifecycle, cancellation của async position, autoUpdate chỉ khi mounted/open.
- **Input:** Approved dependency record, overlay manager. **Dependencies:** P07-04.
- **Files:** Adapter/floating core, package/lockfile, geometry fixtures.
- **Output:** Internal positioning contract và size report.
- **Acceptance:** Không dùng Floating UI React interactions/focus/menu; close/unmount cleanup; late promise không update stale element; min viewport margins.
- **Tests:** Scrolled nested containers, transform ancestor, resize, zoom, RTL, callback after unmount, size budget.
- **Ước lượng:** 5–7 giờ.

### P09-02 — Popover nonmodal behavior

- **Mô tả / Yêu cầu:** Nonmodal dialog content, trigger owns open, dismissal/restoration rules, không trap focus hoặc inert whole page; focus entry/exit documented. Form content dùng normal tab order policy có kiểm chứng portals.
- **Input:** Floating adapter + ownership. **Dependencies:** P09-01, P05-03.
- **Files:** `components/popover/`, API spec, keyboard/portal tests.
- **Output:** Popover with explicit nonmodal contract.
- **Acceptance:** User có đường keyboard ra content; outside/focus dismissal không steal focus về trigger sau click outside; Escape restore trigger; child modal correct.
- **Tests:** Tab forward/back across portal, click outside target retains focus, Popover inside Dialog, nested popovers, AT smoke.
- **Ước lượng:** 5–7 giờ.

### P09-03 — Tooltip semantics và timers

- **Mô tả / Yêu cầu:** Hover/focus open, Escape suppress đến interaction session mới, delayed open, pointer qua tooltip vẫn giữ; no interactive content; own hover handlers, không dùng behavior package.
- **Input:** Floating + WCAG hover criteria/APG notes. **Dependencies:** P09-01.
- **Files:** `components/tooltip/`, timer/hover tests/docs.
- **Output:** Tooltip accessible supplementary description.
- **Acceptance:** Không dùng tooltip làm label duy nhất; no touch-critical information; disabled native trigger strategy documented; no open on keyboard Escape immediate reopen loop.
- **Tests:** Focus/blur, hover corridor, trigger↔tooltip, Escape, reduced motion, describedby refs, timer unmount.
- **Ước lượng:** 5–7 giờ.

### P09-04 — Floating integration và release gate

- **Mô tả / Yêu cầu:** Integrate nested popovers/tooltips trong Dialog, theme scopes; package/perf/a11y coverage; prepare changeset, lặp release verification. Storybook update task chỉ sau exact version published.
- **Input:** New families. **Dependencies:** P09-02, P09-03.
- **Files:** Integration tests, dependency/size reports, docs/changeset.
- **Output:** G09 quality report và reviewable release candidate.
- **Acceptance:** Button bundle vẫn không kéo geometry; no listener growth; no outstanding AT blockers; CSS closures complete.
- **Tests:** Browser3/AT targeted, axe/screenshots, tarball consumer, import graph, 100 cycles.
- **Ước lượng:** 5–7 giờ.

## P10 — Custom Select

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Single-select noneditable combobox, giữ NativeSelect độc lập |
| Công việc / thứ tự | Transition/API spec → collection/typeahead → component → form → hardening |
| Files | `components/select`, typeahead core, tests/docs/locales |
| Prerequisites | G09 |
| Outputs | Custom Select phù hợp APG với own behavior |
| Acceptance | Selection vs active tách biệt; no search/multi-select creep; screen reader reliable |
| Tests | Keyboard/typeahead/IME, portal, forms/reset, dynamic data, AT |
| Rủi ro / xử lý | Custom select không native autofill/validation equivalence → explicit docs, NativeSelect recommended khi cần native behavior |
| Ước lượng | 8–12 ngày, rất cao |
| Gate G10 | AT và browser contract pass trước export stable; phát hành lặp P07-02…04 khi được giao, không publish ngầm trong hardening |

### P10-01 — Select API và transition specification

- **Mô tả / Yêu cầu:** Chốt string|null, options uniqueness, textValue, label/name, active/selected/open; transition table cho arrows/type/Enter/Space/Escape/Tab/outside. Không triển khai trước spec review.
- **Input:** API §7.5. **Dependencies:** P09-04.
- **Files:** `docs/components/select.md`, type fixtures, state transition spec.
- **Output:** Reviewable contract cho Select.
- **Acceptance:** Mỗi event có commit/cancel policy; controlled parent rejection và removed option defined; async search/multi là non-goals.
- **Tests:** Table-driven expected transitions thiết kế trước implementation; TS example review.
- **Ước lượng:** 3–5 giờ.

### P10-02 — Typeahead và active descendant model

- **Mô tả / Yêu cầu:** Locale-aware prefix matching, repeated character cycling, timeout buffer, skip disabled; DOM focus tại trigger, active IDs mount; không intercept IME composition.
- **Input:** Select transitions + collections. **Dependencies:** P10-01, P03-05.
- **Files:** `src/core/collection/typeahead.ts`, Select reducer, unit/browser cases.
- **Output:** Tested selection navigation state.
- **Acceptance:** Deterministic vi diacritics policy qua Intl.Collator; active never references unmounted option; no virtualization v1.
- **Tests:** Repeated chars, timeout, accented labels, disabled/all-disabled/empty, dynamic remove, composition events.
- **Ước lượng:** 4–6 giờ.

### P10-03 — Select DOM, slots và floating interactions

- **Mô tả / Yêu cầu:** Trigger combobox/listbox/options, name/expanded/controls/activedescendant; text-only or ReactNode option labels with textValue; typed renderOption/slots; no nested interactive options.
- **Input:** Reducer + geometry/dismiss. **Dependencies:** P10-02, P09-01.
- **Files:** Select component/types/CSS/lab, exports pending gate.
- **Output:** Styled/unstyled Select candidate.
- **Acceptance:** Pointer/keyboard same commit semantics; choose option emits once; no parent Dialog dismissal; selected icon not duplicate announcement.
- **Tests:** Keyboard3 browsers, pointer drag, nested Dialog, long list scroll selected into view, customization.
- **Ước lượng:** 5–7 giờ.

### P10-04 — Select form, locale và controlled contracts

- **Mô tả / Yêu cầu:** Hidden form value, null/empty policy, reset, disabled/name, custom required validation messaging documented; explicit no native validation/autofill guarantee. Match en/vi announcements.
- **Input:** Select candidate. **Dependencies:** P10-03, P03-04.
- **Files:** Select form integration, locale keys, docs/tests.
- **Output:** Complete integration contract.
- **Acceptance:** FormData đúng một value; no duplicate hidden accessible control; reset callback reason predictable; controlled parent owns truth.
- **Tests:** External form owner, reset, disabled fieldset propagation policy, required invalid messages, locale swap while open.
- **Ước lượng:** 4–6 giờ.

### P10-05 — Select AT và release hardening

- **Mô tả / Yêu cầu:** NVDA/VoiceOver desktop/mobile full selection flows; Firefox/WebKit geometry; packed package/examples; new budgets measured, no silent increase. Add migration guide NativeSelect vs Select.
- **Input:** Complete Select. **Dependencies:** P10-04.
- **Files:** `docs/a11y/select.md`, browser/package regressions, changeset.
- **Output:** G10 pass evidence/release candidate.
- **Acceptance:** No critical AT issue; empty/invalid/disabled reported meaningfully; NativeSelect API unchanged; new styles no conflict.
- **Tests:** Manual matrix, axe, keyboard/typeahead, SSR, size/tree-shaking, exact tarball install.
- **Ước lượng:** 6–8 giờ active QA; fixes split as needed.

## P11 — Calendar và DatePicker

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | Gregorian date-only với locale/input/calendar interactions tự viết; ADR-006 |
| Công việc / thứ tự | Date adapter → calendar reducer → grid → text parser → composition → hardening |
| Files | date adapter, `components/{calendar,date-picker}`, locales/tests/docs |
| Prerequisites | G07 + Dialog/locale stable; scheduling mặc định sau G10 nhưng không logic dependency vào Select |
| Outputs | DatePicker date-only, no range/timezone/âm lịch |
| Acceptance | Value deterministic, strict parse, SSR referenceDate, calendar a11y verified |
| Tests | Leap/boundary/date invariants, locale/timezone matrix, keyboard grid, AT |
| Rủi ro / xử lý | DST/parse/calendar ambiguity → civil model, adapter, explicit referenceDate và format |
| Ước lượng | 12–18 ngày, rất cao |
| Gate G11 | Date errors không silently normalize; full AT/package DoD pass; phát hành lặp P07-02…04 khi được giao |

### P11-01 — Date model và utility adapter

- **Mô tả / Yêu cầu:** Dependency record cho @internationalized/date; public readonly {year,month,day}; strict validation năm1–9999; ISO form serialization; Gregorian-only import path. Không leak dependency public types.
- **Input:** ADR-006 + date API. **Dependencies:** P07-04, P02-04.
- **Files:** `src/adapters/date.ts`, `components/calendar/date.types.ts`, unit tests, dependency record.
- **Output:** Date arithmetic/format adapter.
- **Acceptance:** Reject impossible dates; no timezone conversion of date value; no Date.parse user text; public declaration không đòi consumer import utility type.
- **Tests:** Leap years/centuries, month/year boundaries, compare/add round-trip, timezone invariance UTC/Saigon/NewYork.
- **Ước lượng:** 4–6 giờ.

### P11-02 — Calendar navigation state

- **Mô tả / Yêu cầu:** Focused/selected/visible month tách riêng; arrows/home/end/page keys; month clamp rules; min/max/unavailable focusable policy; referenceDate deterministic.
- **Input:** Date adapter + calendar interaction spec. **Dependencies:** P11-01, P03-06.
- **Files:** `components/calendar/calendar-state.ts`, transition table/unit tests.
- **Output:** Pure calendar reducer.
- **Acceptance:** Month movement không overflow 31→tháng ngắn sai; no selected value change khi chỉ focus; boundaries rõ; empty value dùng referenceDate.
- **Tests:** Table-driven navigation, month/year roll, weekStartsOn vi/en, unsupported year boundary.
- **Ước lượng:** 4–6 giờ.

### P11-03 — Calendar grid semantics và announcements

- **Mô tả / Yêu cầu:** Grid/row/gridcell với day buttons và một tab stop; selected/unavailable/today distinctions; localized full day names, month live polite, heading controls. Focus đúng sau rerender tháng.
- **Input:** Reducer. **Dependencies:** P11-02.
- **Files:** Calendar DOM/CSS/lab/locales, browser keyboard tests.
- **Output:** Standalone Calendar candidate.
- **Acceptance:** Screen reader không nghe 42 ngày một lúc; unavailable không selectable; không chỉ dùng màu; month buttons names đầy đủ.
- **Tests:** Browser grid navigation, vi/en week labels, forced colors, controlled/uncontrolled date, multi-month non-goal assertion.
- **Ước lượng:** 5–7 giờ.

### P11-04 — Date input draft và strict parser

- **Mô tả / Yêu cầu:** Text draft khác committed value; dd/MM/yyyy và MM/dd/yyyy explicit, zero-padding policy; parse on blur/Enter, invalid text giữ để sửa; no freeform fuzzy parsing, no cursor formatting each key.
- **Input:** Date value/locale contracts. **Dependencies:** P11-01, P04-05.
- **Files:** `components/date-picker/date-input-state.ts`, parser tests, locale error keys.
- **Output:** Input contract không mất dữ liệu người dùng.
- **Acceptance:** Invalid draft không đổi value; controlled updates sync khi appropriate; locale đổi lúc draft invalid không diễn giải sai ngày âm thầm; IME respected.
- **Tests:** Ambiguous 01/02 per locale, incomplete/invalid/leap day, paste, selection, reset, locale changes.
- **Ước lượng:** 4–6 giờ.

### P11-05 — DatePicker Dialog composition

- **Mô tả / Yêu cầu:** Input+trigger+modal Calendar, select commit/close/restore input or trigger theo origin; hidden ISO form field và localized display input không duplicate submit name; min/max/error association.
- **Input:** Calendar + date draft + Dialog. **Dependencies:** P11-03, P11-04, P05-08.
- **Files:** DatePicker components/types/CSS/docs, composition browser cases.
- **Output:** Styled/unstyled DatePicker API candidate.
- **Acceptance:** open close/value independent controlled contracts; default empty requires referenceDate; no SSR today mismatch; custom slots không override semantic props.
- **Tests:** Enter input, trigger keyboard, Escape cancels selection preview, chosen date commit, form reset/name, nested Dialog, SSR across zones.
- **Ước lượng:** 5–7 giờ.

### P11-06 — DatePicker hardening và release

- **Mô tả / Yêu cầu:** Full keyboard/AT/mobile flow, contrast/long labels/zoom; date property tests bằng deterministic loops, no extra property-testing dependency nếu chưa cần; bundle and package audit.
- **Input:** Calendar/DatePicker candidates. **Dependencies:** P11-05.
- **Files:** A11y/date reports, tests/regressions, API docs, changeset.
- **Output:** G11 evidence và release candidate; stories sau npm version mới.
- **Acceptance:** Invalid dates cannot submit valid-looking wrong value; public types utility-independent; no timezone drift; AT usable, budgets justified.
- **Tests:** NVDA/VoiceOver iOS/macOS, timezone/locale/date boundary matrix, tarball SSR/types, size/tree-shaking, screenshots.
- **Ước lượng:** 6–8 giờ active QA, split bug fixes.

## P12 — Stabilization, 1.0 và Maintenance

| Thuộc tính phase | Nội dung |
| --- | --- |
| Mục tiêu / quyết định | API trưởng thành từ pilot, phát hành có trách nhiệm, maintainer workload hữu hạn |
| Công việc / thứ tự | Pilot evidence → API freeze → maintainer processes → 1.0 release gate |
| Files | ADR/API/migration docs, issue templates, security/release policies, reports |
| Prerequisites | Các stable versions đã dùng thật; 1.0 không bắt buộc mọi future component trong catalog |
| Outputs | Migration plan, maintenance policy, 1.0 readiness decision |
| Acceptance | Hai app pilot, ≥4 tuần không critical regression, versioned contracts rõ |
| Tests | Regression/consumer/API snapshots, upgrade rehearsals, release audit |
| Rủi ro / xử lý | Đếm component thay chất lượng → success metrics adoption/defects, không scope creep |
| Ước lượng | 5–8 ngày active + ít nhất 4 tuần observation |
| Gate G12 | Owner approved stable API và toàn release checklist; publish chỉ trong task authorized |

### P12-01 — Pilot integration và DX evidence

- **Mô tả / Yêu cầu:** Tích hợp ít nhất hai app độc lập khác theme/bundler hoặc form workflow; đo install/time-to-theme, issue taxonomy và docs gaps. Không gửi outreach/messages nếu chưa được giao.
- **Input:** Published library versions. **Dependencies:** P08-08; các family đã publish được pilot tương ứng.
- **Files:** `docs/reports/pilot-*.md`, regression cases/issues.
- **Output:** Evidence thực tế, prioritized defects, no fabricated adoption claims.
- **Acceptance:** Ghi versions/steps/time thực; critical defects có reproduction và owner; không sửa app ngoài authorized scope.
- **Tests:** Pilot smoke/upgrade tests, custom theme/portal/form workflows.
- **Ước lượng:** 4–6 giờ phân tích mỗi batch, observation riêng.

### P12-02 — Public API freeze và migrations

- **Mô tả / Yêu cầu:** Audit exported props/types/tokens/slots/data-state/CSS paths/behavior; remove accidental internals trước 1.0 bằng explicit pre-1.0 breaking release; document deprecations/migration.
- **Input:** Pilot evidence + all supported public APIs. **Dependencies:** P12-01.
- **Files:** `docs/public-api.md`, migration guides, ADR revisions, Changesets.
- **Output:** 1.0 API contract và supported-surface list.
- **Acceptance:** Không undocumented exports; stable naming/defaults; examples/consumer migrations compile; unsupported future catalog không giả available.
- **Tests:** API diff and type tests, upgrade from prior stable fixture, CSS/customization regression.
- **Ước lượng:** 4–6 giờ.

### P12-03 — Maintainer operations

- **Mô tả / Yêu cầu:** Issue/PR templates, accessibility bug severity, dependency update cadence, release support policy, responsible security disclosure, ownership/bus-factor handover. Không hứa SLA 24/7.
- **Input:** Pilot issue types + release workflow. **Dependencies:** P12-02.
- **Files:** CONTRIBUTING/SECURITY, issue templates, maintenance policy, triage labels spec.
- **Output:** Sustainable maintainer runbook.
- **Acceptance:** Update PR không auto merge major/runtime changes; security route có owner; backlog capacity policy; support branch/EOL explicit.
- **Tests:** Rehearse regression bug report, patch release checklist và dependency upgrade in clean branch.
- **Ước lượng:** 3–5 giờ.

### P12-04 — 1.0 readiness và authorized release

- **Mô tả / Yêu cầu:** Sau observation window, full gates và owner review; repeat exact tarball release workflow P07-02…04; update Storybook bằng registry version PR sau publish. Không tự gộp unreleased future work vào 1.0.
- **Input:** API freeze, pilot stability, maintenance policy. **Dependencies:** P12-03, P07-04.
- **Files:** Release report/changelog/version metadata; showcase update PR riêng.
- **Output:** 1.0 release nếu được authorized và pass; nếu không, readiness report blocked rõ nguyên nhân.
- **Acceptance:** Không unresolved breaking ambiguity/critical bug; registry smoke và docs sync successful; lifecycle owner assigned.
- **Tests:** Full regression/types/package/browser/a11y affected surfaces, exact-version clean install, upgrade rehearsal.
- **Ước lượng:** 4–6 giờ active, chờ owner/observation riêng.

## Mẫu lệnh bàn giao một phiên Codex

```text
Thực hiện task P03-01 trong ROADMAP.md.
Đọc AGENTS.md, ADR liên quan và COMPONENT_GUIDELINES.md trước khi sửa.
Chỉ triển khai useControllableState và tests/docs trong phạm vi task.
Không thêm dependency hoặc public export. Không bắt đầu task kế tiếp.
Nếu prerequisite chưa có evidence, báo blocker cụ thể và hoàn thiện phần
độc lập được phép; không tự coi prerequisite là pass.
Chạy targeted checks, kiểm diff và ghi docs/reports/P03-01.md.
Kết thúc với kết quả, commands thực tế, pending checks và rủi ro còn lại.
```

Task report tối thiểu:

```text
Task: P03-01
Status: DONE | IN_PROGRESS | BLOCKED
Inputs/evidence checked:
Files changed:
Behavior implemented:
Commands and actual outcomes:
Manual checks and reviewer:
Public API/dependency/bundle impact:
Deviations and ADR references:
Remaining risks/blockers:
Next eligible task:
```

Các task future P09–P12 là kế hoạch có thể bàn giao; mỗi lần bắt đầu phải xác minh lại versions/sources và kiến trúc đã được duyệt. Không được hiểu roadmap như quyền tự chạy toàn bộ dự án hoặc publish trong phiên phân tích hiện tại.
