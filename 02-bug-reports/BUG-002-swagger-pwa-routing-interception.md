# Bug Report: BUG-002

## Metadata
- **Bug ID**: `BUG-002`
- **GitHub Issue**: [#27](https://github.com/Nyanns/lumiina/issues/27)
- **Jira Issue**: `LUM-6`
- **Associated Test Case**: `TC_DOCS_001`
- **Module**: Documentation & Infrastructure (`/swagger`)
- **Reported By**: Sandi (QA & SDET Engineering)
- **Reported Date**: 2026-09-14
- **Status**: Open
- **Severity**: Minor
- **Priority**: Medium (P3)

---

## Title
[Docs/PWA] Swagger UI fails to load due to PWA Service Worker navigation interception and missing /swagger redirect

---

## Environment
- **Target URL**: `https://lumiina-art.vercel.app/swagger` & `https://lumiina-art.vercel.app/swagger/index.html`
- **Browser**: Google Chrome 128+ with registered Service Worker
- **Frontend Stack**: Vite, React, `vite-plugin-pwa` (Workbox)
- **Backend Stack**: Go 1.22+, Gin, `gin-swagger`

---

## Steps to Reproduce (STR)
1. Open Google Chrome and visit `https://lumiina-art.vercel.app` to register the PWA Service Worker.
2. Navigate to `https://lumiina-art.vercel.app/swagger/index.html`.
3. In a separate tab/window, navigate to `https://lumiina-art.vercel.app/swagger` (without `/index.html`).

---

## Expected Result
1. Navigating to `https://lumiina-art.vercel.app/swagger` should automatically return an HTTP 301/302 redirect to `/swagger/index.html`.
2. The PWA Service Worker should bypass `/swagger/*` requests, allowing the browser to load Swagger UI assets directly from the backend server without SPA interception.

---

## Actual Result
1. Under an active Service Worker, Workbox navigation fallback intercepts the request and serves the React SPA root (`index.html`), causing a blank screen or route failure because React Router has no route for `/swagger/*`.
2. Navigating to `/swagger` or `/swagger/` directly returns an unhandled HTTP 404 `Not Found` error from the Go Gin router.

---

## Root Cause Analysis (White-Box Code Audit)
1. **Frontend (`web/vite.config.js`)**:
   `VitePWA` Workbox options lack `navigateFallbackDenylist`. By default, all HTML document navigations under scope `/` are intercepted and routed to the React app shell (`/index.html`).
2. **Backend (`internal/router/router.go`)**:
   The router only registers `r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))`. Requests to `/swagger` do not match the wildcard prefix, resulting in a 404 response.

---

## Suggested Remediation
1. **Frontend (`web/vite.config.js`)**:
   Add `navigateFallbackDenylist` to Workbox options:
   ```javascript
   workbox: {
     navigateFallbackDenylist: [/^\/swagger/, /^\/api/],
     globPatterns: ['**/*.{js,css,html,ico,png,svg,jpg}'],
     // ...
   }
   ```
2. **Backend (`internal/router/router.go`)**:
   Add an explicit canonical redirect handler:
   ```go
   r.GET("/swagger", func(c *gin.Context) {
       c.Redirect(http.StatusMovedPermanently, "/swagger/index.html")
   })
   ```
