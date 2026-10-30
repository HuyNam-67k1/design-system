# Quy chuẩn phát triển Component

Trạng thái: contract đề xuất, áp dụng sau khi ADR liên quan được duyệt. Canonical architecture ở `ARCHITECTURE.md`; task scope ở `ROADMAP.md`. Không coi ví dụ types dưới đây là source đã tồn tại.

## 1. Trước khi viết component

Viết specification theo family: mục đích/non-goals; anatomy slots; semantic DOM; props/defaults; state transition table; keyboard interactions; focus entry/exit; native forms; locale keys; token/state attributes; edge cases; test cases. Component phức tạp không được bắt đầu bằng JSX rồi mới bổ sung ARIA.

Specification và tests phải chỉ ra rõ state do ai sở hữu. Parent là nguồn sự thật ở controlled mode; consumer labels không tự dịch; validation nghiệp vụ do app làm. Nội bộ không truy cập router, network hoặc persistent storage.

## 2. Naming và tổ chức file

Component/exports/types: PascalCase (`Button`, `ButtonProps`, `DialogContent`). Hook: `use*`; helpers: camelCase; folders/files kebab-case. Export compound parts phẳng, ví dụ `DialogRoot`, `DialogTrigger`; không namespace object mặc định. CSS classes `.ui-Button`, `.ui-DialogContent`; public tokens `--ui-*`, private implementation variables `--_ui-*`.

Một family có component file, CSS, tests, specification và index export explicit. Chỉ tách state/types/context khi đủ logic để việc tách tăng readability. Không export toàn bộ file bằng `export *` vì tiện; type-only exports dùng `export type`.

Không import root `@scope/ui` hoặc `src/index.ts` từ nội bộ. Core không import components. Component không import CSS trực tiếp vào JS distribution; CSS build theo manifest. Component cha dùng private helper/behavior controller, tránh reuse một styled component nếu làm ép DOM hoặc tạo imports quá lớn.

## 3. TypeScript

`strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, `verbatimModuleSyntax`; build không `skipLibCheck` để che vấn đề declarations của chính thư viện. Consumer fixtures cũng test không skip. Dùng `unknown` rồi narrow; không public `any`; casts tại boundary phải có invariant/test.

Props dựa native attributes phù hợp. Không dùng generic HTML attributes làm mất cụ thể ref/event. Input giữ native `size: number`, dùng `controlSize`. NativeSelect v1 giới hạn single selection: omit native `multiple` khỏi public props, không silently nhận rồi bỏ qua; `size` vẫn là số hàng native. Checkbox omit native checked/defaultChecked khỏi derived props trước khi khai báo union mixed; chỉ adapter đổi mixed thành DOM boolean + indeterminate. Không public enum runtime khi string union đủ. Không generic polymorphic `ElementType` cho toàn bộ API. Nếu một component không có behavior state thì không tạo hook/controller rỗng.

```ts
// Minh họa contract, không phải implementation.
type ControlSize = "sm" | "md" | "lg";
type ButtonVariant = "primary" | "secondary" | "outline" | "ghost";
type Tone = "neutral" | "brand" | "danger";
type UIStyle = React.CSSProperties & {
  [name: `--ui-${string}`]: string | number | undefined;
};

type InputProps = React.ComponentPropsWithoutRef<"input"> & {
  controlSize?: ControlSize;
  invalid?: boolean;
  unstyled?: boolean;
};

type ChangeDetails<Reason extends string> = {
  reason: Reason;
  // Null cho programmatic/reset request; không giữ event để đọc sau async.
  event: Event | React.SyntheticEvent | null;
};
```

Các props styles public dùng `Omit<NativeProps, "style"> & {style?: UIStyle}` khi cần, không `as any`. Mỗi family định nghĩa finite reason union thực tế, không public một union khổng lồ cho mọi component.

## 4. Controlled và uncontrolled

| State | Controlled | Uncontrolled | Callback |
| --- | --- | --- | --- |
| Native Input/Textarea/NativeSelect | value | defaultValue | native onChange(event) |
| Checkbox | checked: boolean hoặc mixed | defaultChecked | onCheckedChange(next, details) |
| Switch | checked: boolean | defaultChecked | onCheckedChange(next, details) |
| RadioGroup/Tabs | value: string | defaultValue | onValueChange(next, details) |
| Select | value: string hoặc null | defaultValue | onValueChange(next, details) |
| Dialog/Select popup/DatePicker popup | open: boolean | defaultOpen | onOpenChange(next, details) |
| DatePicker/Calendar | CalendarDateValue hoặc null | defaultValue | onValueChange(next, details) |

Không truyền cả value/defaultValue hoặc checked/defaultChecked. Type API có discriminated alternatives khi không phá native usability; runtime dev warning bổ sung. Native Input uncontrolled để DOM quản lý; không ép useControllableState lên mọi input. Checkbox mixed public value là `"mixed"`; DOM `checked=false`, `indeterminate=true` theo documented reset policy; mixed không tự submit giá trị checked. RadioGroup value object/number không hỗ trợ v1.

Nếu expose native onChange cùng custom onCheckedChange: native handler chạy trước, rồi internal action theo cancellation policy; chỉ một logical value callback mỗi event. Không gọi callback trong state initializer/reducer/render hoặc state updater có thể bị chạy lại. `Object.is` kiểm cùng giá trị; complex date equality dùng compare date fields, không object reference.

Mode cố định suốt mount. Default thay đổi không reset. Controlled callback là request, không mutate prop. Form reset uncontrolled về initial default; custom controlled callback reason form-reset để app quyết. Document native required behavior riêng custom controls, không tạo expectation sai về hidden inputs.

## 5. Events và refs

Cancelable actions: click-trigger toggling, value commit, dismiss request. User handler trước; nếu defaultPrevented thì internal action tương ứng không chạy. Non-cancelable invariants: unsubscribe/cleanup, DOM ref bookkeeping, protected ARIA associations. Không để user cancel cleanup.

`stopPropagation` chỉ dùng khi interaction specification thật sự cần, không để overlay bắt document toàn cục rồi chặn mọi key. React event bubbling đi theo React tree kể cả portal; outside detection phải xét DOM composedPath + owned branches. Không coi child event là outside vì DOM sibling.

Dùng `forwardRef` trong giai đoạn hỗ trợ React18; React19 có ref-as-prop nhưng đổi implementation common baseline chưa cần. Typecheck refs với typings18/19. Callback ref cleanup: wrapper không vô tình trả HTMLElement từ assignment expression; chuyển cleanup/null theo version-compatible contract. Stable ref objects dùng làm targets, không public imperative API không cần thiết.

## 6. Composition và slots

Simple component: top-level className/style cho root; `slotProps` cho label/icon/spinner khi family có. Compound: props trực tiếp lên part, không root slotProps trùng ownership. Nội dung children có semantics đúng, không clone tùy ý để nhét handlers. Không `asChild` v1.

Render adapter ở DialogTrigger/Close chỉ có một target native button hoặc component forward ref đầy đủ. Props callback bao gồm ref, type, ARIA, handlers, disabled state; consumer spread toàn bộ lên node điều khiển. Không button bọc Button. Có positive/negative examples và test type về ref mismatch.

| Slot props | Có thể nhận | Không nhận từ customization channel |
| --- | --- | --- |
| Decorative icon/label | className, style, data-* | role của control, tabIndex mở thêm tab stop |
| Select listbox/option | class/style, documented visual attrs | aria-selected, role, id điều khiển active descendant |
| Dialog Content | class/style, native compatible events, explicit name strategy | aria-modal=false trong modal-only v1 |
| Native Input | Native props gồm aria-label/description | Không có slotProps vì root chính là input |

State attribute contract ví dụ: Button `data-loading`, `data-disabled`; Checkbox `data-state=checked|unchecked|mixed`; Tabs trigger `data-state=active|inactive`; Dialog content `data-state=open|closed`. Không cần data-state trên static Badge nếu không có state machine. Consumer selectors dựa documented states được coi là public API.

## 7. Styling

Không CSS-in-JS runtime. Theme dùng CSS Variables; no inline styles cho mọi variant. Inline style chỉ measurement cần thiết (geometry) hoặc explicit consumer style. Không tạo utility class động kiểu `bg-${tone}-600`; maps/semantic selectors hữu hạn. Tailwind @apply chỉ static utility không phụ thuộc hidden initialization; tokens/ring/shadow/motion dùng CSS rõ ràng.

`cx` chỉ nối class strings, không giải xung đột utility. Không tailwind-merge/CVA cho default architecture. Variant/tone/size là axes có ý nghĩa; không ép mọi component có cùng tất cả values. Native disabled và loading có styling khác nhau; hover chỉ khi pointer thích hợp; focus-visible không xóa default outline nếu chưa có replacement.

Ví dụ customization contract:

```css
/* App CSS, unlayered; đây là theme tùy biến, values cần contrast QA. */
.brand-scope {
  --ui-color-action: #6d28d9;
  --ui-color-on-action: #ffffff;
  --ui-color-text: #111827;
  --ui-color-surface: #ffffff;
  --ui-font-family: system-ui, sans-serif;
  --ui-radius-2: 0.5rem;
  --ui-button-radius: 999px;
}
.brand-scope[data-ui-theme="dark"] {
  --ui-color-action: #c4b5fd;
  --ui-color-on-action: #1e1b4b;
  --ui-color-text: #f9fafb;
  --ui-color-surface: #111827;
}
.checkout-button { min-inline-size: 12rem; }
.checkout-button[data-loading] { cursor: progress; }
```

Reference tokens overridden trong nested scope cần semantic alias được khai báo/rebind ở scope đó; không mặc định inherited resolved alias trên root sẽ tự tính lại khi reference thay đổi ở child. Tests phải bao gồm trường hợp này.

Unstyled bỏ theme/decorative classes, giữ slot attributes và structural classes như visually-hidden, hidden panels, overlay positioning foundations. Consumer vẫn import structural CSS qua normal/per-component CSS entry. Không hứa “no CSS at all”. All components phải usable khi app không có Tailwind/Preflight.

## 8. Accessibility và locale

Prefer native button/input/select/table. Accessible name bắt buộc cho controls; placeholder/tooltip không thay label. Optional helper/error IDs chỉ được reference khi node tồn tại. Invalid không chỉ màu. Không dùng aria-disabled thay disabled cho native controls trừ loading policy cố ý giữ focus; khi aria-disabled, phải chặn activation thật.

No positive tabindex. Roving focus và active-descendant là hai mô hình khác nhau, không trộn tùy ý. Dialog restore focus có fallback, không để focus rơi body do unmount. Escape ở top layer; Tab không bị trap trong nonmodal popup. Motion tôn trọng reduced-motion, forced-colors dùng system colors khi cần.

Mọi built-in visible label/accessibility label đi qua typed locale schema, en/vi cùng test gate. User copy/children không tự dịch. Locale override có precedence rõ; no navigator detection trong render. Third locale hỗ trợ LTR trước; RTL mới phải audit keyboard/layout, không chỉ `dir=rtl`.

## 9. React và performance

Render thuần; no event registration, random IDs, DOM reads hoặc state updates trong render. Effects có cleanup đối xứng, strict-mode safe. Không dựa effect chạy một lần. Sử dụng ownerDocument/defaultView từ target, không hard-code window cho embedded contexts.

Không duplicate derived state, no useEffect để copy controlled props vào local state. Chỉ memo context/actions khi cần; profile trước thêm React.memo. Không subscribe mỗi item vào global provider cho state không liên quan. Async geometry ignores stale results; use observers chỉ khi open và cleanup khi unmount.

## 10. Documentation và Definition of Done

Mỗi family document purpose/non-goals, installation CSS path, props/defaults/types, slots, state attributes, tokens, native form behavior, locale, keyboard map, SSR/portal limitations, controlled/uncontrolled và customization example. Compile examples against packed package.

DoD bắt buộc ở `TESTING_STRATEGY.md`: code + types + unit/component/browser + a11y/visual thích hợp + docs + package + no dependency drift. Đổi documented prop/default/token/slot/behavior cần Changeset và SemVer review. Không gọi family “stable” chỉ vì screenshot đẹp.
