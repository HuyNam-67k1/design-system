# Hướng dẫn đóng góp và vận hành repository

Đây là quy trình đề xuất cho repository sẽ được tạo; chưa có remote/issue tracker/release nào. Các URL và owner thực tế được điền ở P07-01, không invent contact.

## Bắt đầu một thay đổi

Đọc ARCHITECTURE, ADR liên quan, COMPONENT_GUIDELINES và TESTING_STRATEGY. Chọn một Task ID hoặc issue có scope/acceptance rõ. Bug fix cần reproduction trước khi sửa; feature mới cần specification và vị trí trong catalog/roadmap. Không triển khai catalog tương lai chỉ vì file đã liệt kê tên.

Nhánh đề xuất: `feat/P04-04-button`, `fix/dialog-focus-restore`, `docs/theme-overrides`. Không force-push shared main, không sửa unrelated user changes. Một PR một lý do thay đổi; tránh reformat cả repo khi sửa behavior nhỏ.

## Thiết lập môi trường

Node/npm exact versions theo docs/versions và .node-version. npm ci dùng lockfile, không tự upgrade major hoặc regenerate lockfile do thuận tiện. Vite lab là môi trường phát triển library; không Storybook trong repo library. Packed consumer tests là nguồn chứng minh distribution, không dev lab.

Scripts chuẩn ở TESTING_STRATEGY. Nếu script chưa được tạo vì task foundation chưa hoàn thành, báo prerequisite thay vì viết report test pass. Không thêm CI credentials vào local config, source, logs hoặc screenshots.

## Coding và review

Native HTML/React semantics trước abstraction; TypeScript strict; style tokens không inline hardcoded brand values; imports theo layer; no hidden global state; no runtime network. All public surfaces typed/documented. Tests user-facing; snapshot visual thay đổi cần giải thích.

PR description mở đầu bằng vấn đề và behavior sau thay đổi. Bao gồm Task ID, public API/dependency/CSS impact, tests thực chạy, manual a11y evidence khi cần, migration/changeset và known limitations. Không kể toàn bộ lịch sử thử sai nếu không ảnh hưởng reviewer.

Review checklist: scope đúng; API consistent; native props giữ nghĩa; SSR/ref/controlled contracts; CSS cascade/portal theme; keyboard/a11y; cleanup; declaration/package exports; size; docs; SemVer. Reviewer được quyền yêu cầu hoãn component nếu quality gate chưa đạt.

## Dependency policy

Runtime allow-list MVP: tabbable; peer React/ReactDOM. Floating/date chỉ thêm ở approved phase. Không thêm UI primitives/behavior wrappers bị cấm; không bọc lại library có sẵn rồi gọi là tự triển khai.

Dependency proposal phải có exact name/version/license, runtime/dev/peer classification, use case, alternatives/self-build cost, transitive graph, measured bundle impact, maintenance/security status, entry paths và removal plan. Dev tool có runtime cost 0 nhưng vẫn ảnh hưởng install time/security/update workload. Không “npm install library” trong PR không liên quan.

Dev dependencies pin exact qua lockfile. Runtime version range ưu tiên tested compatible major/minor policy có lý do; release CI kiểm resolved versions. External runtime utilities không bundle lặp vào từng entry; consumer bundler xử lý và size tests tính cả transitives. Không broad `*` peers hoặc override peer conflict để giấu incompatibility.

## Git commits

Conventional Commits: `feat(button): add loading state`, `fix(dialog): restore focus after trigger removal`, `test(select): cover typeahead cycling`, `docs(theme): explain portal scope`, `build(package): validate CSS exports`, `chore(deps): update approved dev tool`.

Breaking change dùng `!` hoặc footer `BREAKING CHANGE:` và Changeset/migration. Commit message không thay Changeset. Không commit node_modules, test traces, temporary tarballs hoặc secrets; retain selected reports/baselines theo policy. Generated token CSS nếu committed phải khớp generator output và có check; dist thường build ở CI, không source of truth.

## Version và changelog

Changesets theo PR có public effect. Pure internal refactor không public effect có thể không cần Changeset nhưng giải thích. 0.x breaking tăng minor, patch nonbreaking; 1.x SemVer. Documented tokens/slots/states/CSS paths là API. Visual tweak có thể breaking nếu phá layout contract; reviewer quyết theo impact, không mặc định patch.

Changelog nêu user-visible behavior, fixes, migration và limitations. Deprecation window mục tiêu hai minor hoặc 90 ngày trước major removal khi khả thi; security/correctness exception phải có rationale. Không thay nội dung version đã publish.

## Release runbook

1. Maintainer chọn release scope và owner, kiểm G06/updated gates.
2. Release PR ghi version/changelog/migrations; owner chốt metadata/license.
3. CI clean install, full checks, packed artifact validation, manual evidence version relevance.
4. Build/pack một lần cho release commit, giữ tarball + hash; không đổi source sau verification.
5. Publish exact artifact khi được ủy quyền bằng trusted publisher/2FA bootstrap được ghi rõ.
6. Verify registry metadata, integrity, exact-version clean consumer; fail thì không mở Storybook gate.
7. Update Storybook repository bằng PR exact dependency version; chạy stories/visual trước deploy.
8. Nếu regression: deprecate bad version khi phù hợp, dist-tag rollback theo quyền riêng, patch mới; không dựa unpublish để rollback thường lệ.

Trusted publishing configuration và CLI requirements phải kiểm lại với npm official docs tại thời điểm release. OIDC publish permission không tự đồng nghĩa dist-tag permission. Không chạy release từ fork PR có code chưa tin cậy.

## Issue triage và bảo trì

Bug report: package/react/browser/OS versions, repro tối thiểu, expected/actual, accessible user impact, theme/custom CSS, logs không chứa secrets. Accessibility issue ghi AT version và key sequence. Không yêu cầu người báo công khai dữ liệu riêng tư để tái hiện.

Ưu tiên: exploitable security và mất khả năng keyboard/AT → regression gây sai dữ liệu → supported-browser function → DX/docs → cosmetic. Maintainer một người không hứa phản hồi tức thời; công bố lịch review thực tế. Mỗi tháng review dependencies/backlog, mỗi quý audit public API/support matrix; major update riêng PR, không auto-merge runtime changes.

Security disclosure route và người nhận phải do owner thiết lập trước release. Issue công khai không chứa exploit details hoặc secrets; contribution guidelines dẫn đến route thật. License đề xuất MIT, chỉ chốt khi owner duyệt; dependencies/assets phải tương thích license thực tế.
