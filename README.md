# Design System

Khung dự án React + TypeScript, dùng Vite để chạy và build. Hiện tại chỉ setup project; chưa triển khai component, token, theme hoặc các phase trong kế hoạch.

## Chạy local

Yêu cầu Node.js 20.19+ hoặc 22.12+ và npm.

```bash
npm ci
npm run dev
```

Mở địa chỉ Vite hiển thị trong terminal, mặc định là http://localhost:5173.

## Các lệnh

| Lệnh | Mục đích |
| --- | --- |
| `npm run dev` | Chạy development server với HMR |
| `npm run typecheck` | Kiểm tra TypeScript |
| `npm run lint` | Kiểm tra source bằng Oxlint theo template Vite |
| `npm run build` | Kiểm tra TypeScript và build vào `dist/` |
| `npm run preview` | Xem bản build local |

## Cấu trúc

```text
src/
  App.tsx          # Màn hình khởi đầu
  main.tsx         # React entry point
  index.css        # CSS cơ bản của màn hình khởi đầu
  vite-env.d.ts    # Kiểu dữ liệu Vite
public/            # Static assets
react-ui-plan/     # Bộ kế hoạch hiện có, chưa triển khai
```

## Git

Repository: https://github.com/HuyNam-67k1/design-system

Remote `origin` dùng HTTPS. Có thể chuyển sang SSH khi máy đã cấu hình SSH key:

```bash
git remote set-url origin git@github.com:HuyNam-67k1/design-system.git
```

Project đang để `private: true`, chưa cấu hình phát hành npm.
