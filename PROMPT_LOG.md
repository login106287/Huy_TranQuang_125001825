# NHẬT KÝ PROMPT ENGINEERING (VIBE CODING LOG)

- **Họ và tên:** Trần Quang Huy
- **MSSV:** 125001825
- **Tên đề tài Website:** Landing Page quán cà phê "Cafe Lạc Hồng" (phong cách ấm áp, tối giản, tone xanh rêu & kem)
- **Công cụ AI đã sử dụng:** Google Antigravity / Gemini 3.8 (viết và sửa code); Claude (review chéo mã nguồn, nhật ký và soạn prompt cho Lần 3)

> Tôi giữ nguyên văn toàn bộ các khối prompt đã sử dụng trong quá trình thực hiện bài tập. Phần "Kết quả" được tôi ghi nhận theo đúng trạng thái mã nguồn thực tế nộp bài, trong đó có một số điểm tôi chủ động điều chỉnh khác so với prompt ban đầu để tối ưu kỹ thuật và trợ năng (chi tiết được tôi đối chiếu ở Lần 3).

---

## 1. Yêu cầu ban đầu (System/Initial Prompt)

> *Prompt tổng quan dùng để khởi tạo khung website.*

```text
Bạn là một Front-end Developer chuyên nghiệp. Hãy tạo cho tôi một Landing Page tĩnh cho quán cà phê tên "Cafe LacHong", phong cách [ấm áp, tối giản, màu xanh - kem], dành cho bài kiểm tra giữa kỳ môn Thiết kế Web.

RÀNG BUỘC BẮT BUỘC:
- Chỉ gồm 3 phần: index.html, style.css, thư mục assets/ (ảnh dùng link Unsplash hoặc placeholder, không dùng ảnh có bản quyền).
- TUYỆT ĐỐI KHÔNG tự viết JavaScript, không tạo file script.js. Chỉ được nhúng thư viện qua CDN. Riêng AOS bắt buộc có đúng 1 dòng khởi tạo AOS.init() theo hướng dẫn chính thức. Ngoài ra không có JS nào khác.
- Danh sách sản phẩm viết tĩnh bằng HTML, không dùng JS để render.

CẤU TRÚC SEMANTIC HTML5 (dùng đúng ngữ nghĩa, hạn chế <div> thừa):
- <header> chứa logo và <nav> menu (Trang chủ, Menu, Về chúng tôi, Đánh giá, Liên hệ)
- <main> chứa các <section>/<article>:
  1. Hero: tiêu đề lớn, slogan, nút CTA "Đặt bàn ngay"
  2. Menu: 6 sản phẩm dạng card (<article>) gồm ảnh (<figure>), tên, mô tả ngắn, giá
  3. Câu chuyện thương hiệu: ảnh + đoạn văn, bố cục 2 cột
  4. Đánh giá khách hàng: 3 card
  5. Giờ mở cửa & Liên hệ
- <footer> chứa địa chỉ, số điện thoại, mạng xã hội, copyright
- Có lang="vi", meta viewport, thuộc tính alt cho mọi ảnh.

CSS:
- Dùng CSS Variables trong :root cho: màu chính, màu phụ, màu nền, màu chữ, font chữ (Google Fonts), spacing (--spacing-sm/md/lg), bo góc (--radius), bóng đổ (--shadow). Toàn bộ file CSS dùng var(...), không hardcode màu lặp lại.
- Layout bằng CSS Grid (danh sách card, dùng repeat(auto-fit, minmax(250px, 1fr))) và Flexbox (header, nav, footer).
- Responsive cho mobile (<768px). Menu mobile dùng kỹ thuật checkbox hack bằng CSS thuần, không JS.
- html { scroll-behavior: smooth; }

HIỆU ỨNG:
- Animate.css (CDN): tiêu đề hero dùng animate__fadeInDown, slogan animate__fadeInUp, nút CTA animate__pulse (lặp vô hạn).
- AOS (CDN css + js): gắn data-aos cho từng section và card, dùng data-aos-delay tăng dần (100, 200, 300) để card hiện lần lượt.
- Tự viết bằng CSS: :hover và :focus cho nút (đổi màu, nhấc lên bằng transform, transition) và cho card (phóng nhẹ + đổ bóng). Thêm :focus-visible rõ ràng.

YÊU CẦU ĐẦU RA:
- Code sạch, có comment tiếng Việt ngắn gọn cho từng khối.
- Xuất đầy đủ nội dung index.html và style.css, nội dung tiếng Việt tự nhiên.
```

**Kỹ thuật Prompt áp dụng:**
- **Role Prompting:** gán vai trò Front-end Developer.
- **Negative Constraints:** cấm `script.js`, cấm JS ngoài `AOS.init()`, cấm hard-code màu lặp lại.
- **Chỉ định cấu trúc:** nêu rõ từng thẻ Semantic cần dùng (`<header>`, `<nav>`, `<main>`, `<article>`, `<figure>`, `<footer>`).
- **Design Token:** liệt kê trước các biến `:root` cần có.

**Kết quả AI tạo ra:** `index.html` đủ 5 section, `style.css` có bảng biến màu xanh rêu (`#2D5A43`) và kem (`#C89666`, `#FAF7F2`), menu mobile bằng checkbox hack, thư mục `assets/`.

**Vấn đề tôi phát hiện sau khi đọc kết quả:**
- `<article>` bị dùng cho các khối không phải bài viết độc lập (`.about-content`, `.booking-card`).
- Lưới `auto-fit/minmax(250px, 1fr)` trên màn hình lớn chia thành 4 card hàng trên và 2 card hàng dưới (lệch 4+2).
- Chữ trắng trên nền `--color-secondary` (`#c89666`) chỉ đạt khoảng 2.6:1, chưa đạt WCAG AA.
- Ảnh hotlink từ Unsplash, phụ thuộc mạng ngoài.
- AOS gắn lồng nhau trên cả `<section>` cha và card con.

---

## 2. Quá trình tinh chỉnh & Giải quyết sự cố (Iteration)

### Lần 1: Chuẩn hóa Semantic HTML, CSS Variables, trợ năng và ảnh cục bộ

- **Prompt đã dùng:**

```text
# VAI TRÒ
Bạn là một Senior Front-end Developer, chuyên về Semantic HTML5, CSS hiện đại và accessibility. Hãy review và sửa mã nguồn website landing page "Cafe Lạc Hồng" trong thư mục hiện tại (index.html, style.css).

# BỐI CẢNH
Đây là bài thi giữa kỳ môn Thiết kế Web. Website đã hoàn chỉnh, nay cần tinh chỉnh để đạt điểm tối đa theo các tiêu chí sau:
- Semantic HTML5 đúng nghĩa, không lạm dụng thẻ.
- CSS Variables nhất quán, dùng Flexbox/Grid, responsive tốt.
- Animate.css và AOS dùng mượt, không gây rối mắt.

# RÀNG BUỘC (BẮT BUỘC TUÂN THỦ)
1. Giữ nguyên thiết kế tổng thể: tone xanh rêu + kem, font Playfair Display / Plus Jakarta Sans, nội dung các section.
2. Giữ nguyên các thư viện đang dùng (Animate.css 4.1.1, AOS 2.3.4, Font Awesome). Không thêm framework mới.
3. Không thêm JavaScript ngoài AOS.init({...}). Mobile menu vẫn dùng checkbox hack, trừ khi mục 2.4 yêu cầu khác đi.
4. Mọi màu, font, spacing mới phải khai báo bằng CSS Variables trong :root, không hard-code.
5. Chỉ sửa những gì được liệt kê bên dưới. Không refactor thêm, không đổi tên class đang dùng trừ khi cần thiết.
6. Comment trong code viết bằng tiếng Việt, ngắn gọn. Xóa các comment có dấu vết prompt, ví dụ: "ĐÚNG 1 DÒNG DUY NHẤT THEO YÊU CẦU", "theo yêu cầu bài tập".

# DANH SÁCH CÔNG VIỆC
## 1. Semantic HTML (index.html)
1.1. Sửa lỗi chính tả: "Ghét Thăm Chúng Tôi" thành "Ghé Thăm Chúng Tôi".
1.2. Đổi <article> thành thẻ phù hợp hơn ở các khối không phải nội dung độc lập:
   - .about-content, .contact-info-card, .booking-card đổi sang <div>.
   - Giữ <article> cho .product-card và .review-card.
   - Cập nhật selector CSS nếu có chỗ phụ thuộc vào tên thẻ.
1.3. .reviewer-avatar đang là <figure>, đổi sang <div>. Giữ <figure> cho ảnh sản phẩm và ảnh about.
1.4. Sửa thứ bậc heading, không nhảy cấp:
   - Trong .about-points, <h4> đổi thành <h3>.
   - Trong .contact-detail, <h4> đổi thành <h3>, vì các khối này đã nằm dưới <h2>.
   - .reviewer-name đổi <h4> thành <p> hoặc <strong>, vì nó không phải tiêu đề mục.
   - Cập nhật CSS tương ứng để giao diện không đổi.
1.5. Thêm skip link ngay sau <body>: <a href="#main-content" class="skip-link">Bỏ qua đến nội dung chính</a>.
   - Thêm id="main-content" cho <main>.
   - Viết CSS .skip-link: ẩn ngoài màn hình, hiện khi :focus.
1.6. Thêm favicon bằng emoji SVG data URI:
   <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>☕</text></svg>">

## 2. CSS (style.css)
2.1. Lưới sản phẩm lệch (4+2): sửa .products-grid thành:
   - Desktop (≥1025px): repeat(3, 1fr).
   - Tablet (601–1024px): repeat(2, 1fr).
   - Mobile (≤600px): 1fr.
2.2. Thêm breakpoint tablet @media (max-width: 1024px) để chỉnh .about-grid, .contact-grid, .footer-content, .reviews-grid hợp lý ở màn hình trung bình.
2.3. Sửa tương phản màu (WCAG AA ≥ 4.5:1):
   - Chữ trắng trên nền --color-secondary (#c89666) chỉ đạt ~2.6:1.
   - Với .btn-secondary, đổi nền sang một biến mới tối hơn, ví dụ --color-secondary-strong: #8a5a2b, hoặc đổi chữ sang --color-text. Chọn phương án đẹp hơn và đạt chuẩn.
   - Kiểm tra lại hover/focus của nút này.
2.4. Cải thiện mobile menu (vẫn không dùng JS):
   - Thay display: none của .menu-toggle bằng kỹ thuật ẩn trực quan nhưng vẫn focus được (position: absolute; opacity: 0; width: 1px; height: 1px;).
   - Thêm .menu-toggle:focus-visible ~ .menu-btn { outline: 3px solid var(--color-secondary); outline-offset: 4px; }.
   - Khi menu đóng trên mobile, các link trong .navbar không được focus bằng Tab. Dùng visibility: hidden kèm transition, rồi visibility: visible khi :checked.
2.5. Biến hóa font-size: thêm các biến --fs-hero, --fs-h2, --fs-lead, --fs-body, --fs-small vào :root. Thay các giá trị font-size hard-code lặp lại (3.5rem, 2.25rem, 1.25rem, 0.85rem...) bằng biến. Giữ nguyên giá trị hiển thị.
2.6. Prefers-reduced-motion: thêm khối ở cuối file:
   @media (prefers-reduced-motion: reduce) {
     *, *::before, *::after {
       animation-duration: 0.01ms !important;
       animation-iteration-count: 1 !important;
       transition-duration: 0.01ms !important;
       scroll-behavior: auto !important;
     }
   }

## 3. Hiệu ứng (index.html)
3.1. Bỏ AOS bị lồng nhau: xóa data-aos="fade-up" khỏi các thẻ <section> (#menu, #about, #reviews, #contact). Chỉ giữ AOS trên các phần tử con (header của section, card, cột nội dung). Thêm data-aos="fade-up" cho .section-header ở #menu, #reviews, #contact để tiêu đề section vẫn có hiệu ứng.
3.2. Cấu hình AOS: thay AOS.init(); bằng AOS.init({ duration: 800, easing: 'ease-out-cubic', once: true, offset: 80 });.
3.3. Hero xuất hiện theo nhịp:
   - badge: animate__fadeIn.
   - h1: animate__fadeInDown animate__delay-1s.
   - slogan: animate__fadeInUp animate__delay-1s, và đảm bảo xuất hiện sau h1.
   - nút CTA: animate__fadeInUp animate__delay-2s.
3.4. Nút CTA pulse: bỏ animate__infinite. Thay bằng animate__repeat-2, chạy sau khi hero xuất hiện xong (dùng animation-delay khớp với delay ở mục 3.3).

## 4. Ảnh & tài nguyên
4.1. Hiện tại ảnh đều hotlink từ Unsplash. Hãy tạo thư mục assets/images/.
4.2. Nếu có truy cập mạng: tải các ảnh về (hero, 6 ảnh sản phẩm, ảnh about, 3 avatar), đặt tên có nghĩa (hero-cafe.jpg, product-ca-phe-muoi.jpg...). Cập nhật đường dẫn trong index.html và style.css (background-image của hero), giữ nguyên alt và loading="lazy".
4.3. Nếu không có mạng: giữ nguyên URL hiện tại và liệt kê trong báo cáo cuối các ảnh cần tải thủ công.
```

- **Kết quả AI tạo ra:**
  - Tải về 11 ảnh vào `assets/images/` (hero, 6 sản phẩm, about, 3 avatar).
  - Đổi `.about-content`, `.contact-info-card`, `.booking-card` sang `<div>`.
  - Thêm skip link và favicon emoji ☕.
  - Lưới sản phẩm 3 cột trên desktop, 2 cột trên tablet, 1 cột trên mobile.
  - Thêm biến `--color-secondary-strong: #8a5a2b` (tương phản khoảng 5.5:1).
  - Gỡ AOS lồng nhau, cấu hình `once: true`.
  - Thêm khối `prefers-reduced-motion`.

- **Nhận xét & Tinh chỉnh của SV:**
  - Prompt này tôi chia thành 4 nhóm việc đánh số (HTML, CSS, Hiệu ứng, Ảnh) và kèm sẵn đoạn mã mẫu (skip link, favicon, media query giảm chuyển động) để AI không phải đoán.
  - Tôi ghi rõ ngưỡng WCAG AA (≥ 4.5:1) thay vì chỉ nói "tăng tương phản".
  - Tôi thêm ràng buộc "chỉ sửa những gì liệt kê, không refactor thêm" để AI không tự ý viết lại những phần đã ổn.

- **Thách thức gặp phải:** Sau lần này, khi thử trên trình duyệt tôi thấy:
  1. AOS gán `transform: translate3d(0,0,0)` lên card sau khi animate xong, làm mất hiệu ứng `:hover { transform: translateY(-8px) }`.
  2. `animate__pulse` giữ `animation-fill-mode: both` nên nút CTA ở Hero không nhấc lên khi hover.
  3. Chuỗi animation Hero quá dài (khoảng 2.8 giây mới hiện xong).
  4. Menu mobile không tự đóng khi bấm vào một mục điều hướng.

- **Cách sửa prompt (Debug prompt):** Ở lần sau tôi không nói chung chung "hover bị lỗi" mà mô tả đúng nguyên nhân kỹ thuật của từng lỗi trên và chỉ định hướng sửa (xem Lần 2).

---

### Lần 2: Sửa xung đột AOS / Animate.css với hiệu ứng tự viết, responsive và form

- **Nguồn bản review:** Được tạo thông qua công cụ phân tích mã nguồn AI (Claude / ChatGPT) kết hợp rà soát thực tế trên trình duyệt.
- **Prompt đã dùng:**

```text
# VAI TRÒ
Bạn là Senior Front-end Engineer. Hãy cải tiến website landing page "Cafe Lạc Hồng" (file index.html, style.css trong workspace) dựa trên bản review bên dưới. Đây là bài thi giữa kỳ môn Thiết kế Web, nên phải giữ nguyên các yêu cầu bắt buộc của đề.

# RÀNG BUỘC BẮT BUỘC (KHÔNG ĐƯỢC PHÁ VỠ)
- Giữ Semantic HTML5: header, nav, main, section/article, footer; không lạm dụng div vô nghĩa.
- Giữ CSS Variables trong :root quản lý màu, font, spacing; layout bằng Flexbox/Grid.
- Giữ tích hợp Animate.css (hero) và AOS (các section), cùng hiệu ứng :hover, :focus tự viết cho nút và card.
- Không thêm framework/thư viện mới. Không dùng JS ngoài đoạn nhỏ cần thiết (tối đa ~15 dòng, đặt cuối index.html).
- Không đổi nội dung chữ, màu thương hiệu (xanh rêu + kem), tên file ảnh trong assets/images/.
- Chỉ sửa trong index.html và style.css. Thay đổi nhỏ, có chủ đích; không viết lại toàn bộ file.

# NHIỆM VỤ (làm theo thứ tự ưu tiên)
## P1. Sửa lỗi tương tác giữa thư viện và hiệu ứng
1. AOS đè hover của card: .product-card và .review-card mất translateY(-8px) khi hover do AOS gán transform sau khi animate, và data-aos-delay làm hover phản hồi chậm. Thêm rule ưu tiên cao hơn, ví dụ:
   .product-card[data-aos].aos-animate:hover, .review-card[data-aos].aos-animate:hover { transform: translateY(-8px); transition-delay: 0s; }
2. Nút CTA hero mất hover do animate__pulse giữ fill-mode: both. Thêm .hero-actions .btn-primary { animation-fill-mode: backwards; }.
3. Rút ngắn chuỗi animation hero: hiện H1 ẩn 1s, slogan 1.4s, nút 2s, pulse 2.8s. Đổi còn khoảng: badge 0s, H1 0.2s, slogan 0.5s, nhóm nút 0.8s, pulse 1.6s (dùng biến CSS hoặc class delay, đồng bộ giữa HTML và CSS).
4. prefers-reduced-motion: bổ sung animation-delay: 0s !important; transition-delay: 0s !important; để nội dung không bị tàng hình khi người dùng tắt chuyển động.
5. Stagger AOS theo cột: đổi data-aos-delay của 6 card sản phẩm thành 100/200/300 lặp lại (hàng 2 cũng 100/200/300) để hàng dưới không phải chờ thêm.
6. Fallback khi AOS không chạy: thêm <noscript><style>[data-aos]{opacity:1!important;transform:none!important}</style></noscript> trong <head>.

## P2. Responsive & Menu mobile
7. Nâng breakpoint chuyển sang hamburger từ 768px lên 960px (hoặc 1024px) để không bị tràn header ở khoảng 769–900px (iPad dọc). Kiểm tra lại các rule liên quan menu trong media query.
8. Thêm JS nhỏ để menu mobile tự đóng khi bấm link:
   document.querySelectorAll('.nav-link, .nav-cta').forEach(a => a.addEventListener('click', () => { document.getElementById('menu-toggle').checked = false; }));
9. Hero dùng min-height: 85svh (giữ fallback 85vh ở dòng trước).
10. Dọn .reviews-grid: bỏ chồng chéo giữa auto-fit/minmax và các repeat(2,1fr)/1fr trong media query.

## P3. Chất lượng CSS & Variables
11. Đổi html { font-size: 16px } thành font-size: 100%.
12. Thay --transition: all ... bằng danh sách thuộc tính cụ thể (transform, box-shadow, background-color, border-color, color, opacity); tương tự --transition-fast. Bỏ transition thừa trên .site-header.
13. Đưa các giá trị cứng lặp lại vào biến: chiều cao ảnh sản phẩm/about (--img-h-product, --img-h-about), --container-max: 1200px, các cỡ chữ lẻ gom về thang --fs-*. Ở media query, đổi giá trị biến (--fs-hero, --fs-h2) thay vì ghi đè font-size trực tiếp. Xóa biến không dùng (--color-secondary-dark).
14. Hover link footer: thay padding-left (gây layout shift) bằng transform: translateX(4px).

## P4. Semantic HTML & Accessibility
15. Sửa thứ bậc heading: trong mục Liên hệ, các h3 con ("Giờ Hoạt Động", "Địa Chỉ", "Điện Thoại", "Hộp Thư Điện Tử") đổi thành h4; ở footer, tiêu đề cột dùng h2 (hoặc h3 kèm h2 ẩn trực quan bằng class .visually-hidden), giữ nguyên giao diện bằng CSS.
16. Lời review dùng <blockquote>; giữ nguyên giao diện.
17. Thông tin liên hệ bọc trong <address>; reset font-style: normal trong CSS.
18. Footer: bọc danh sách "Khám Phá" trong <nav aria-label="Liên kết chân trang">.
19. Thay div.hero-overlay rỗng bằng .hero-section::before; bỏ div khỏi HTML.
20. <figcaption class="about-badge"> đổi thành <span> hoặc <p>.
21. Thêm aria-hidden="true" cho toàn bộ icon <i class="fa-..."> mang tính trang trí.
22. Thêm aria-labelledby cho từng <section>.
23. Bỏ aria-label thừa trên <input id="menu-toggle"> hoặc <label>.

## P5. Form đặt bàn
24. Thêm thuộc tính min cho #booking-date bằng ngày hôm nay (JS 2–3 dòng).
25. Thêm phản hồi sau khi bấm "Xác Nhận Giữ Chỗ": chặn submit mặc định, hiển thị thông báo thành công trong một <p role="status"> dưới form, rồi form.reset().
```

- **Kết quả AI tạo ra:**
  - Card sản phẩm và review hiện đã nhấc lên khi hover sau khi AOS chạy xong, nút CTA Hero cũng hover được.
  - Hero hiện đủ nội dung trong chưa đầy 1 giây (badge 0s, H1 0.2s, slogan 0.5s, nút 0.8s, pulse 1.6s).
  - Thêm `<noscript>` fallback cho AOS, `<blockquote>` cho review, `<address>` cho thông tin liên hệ, `::before` thay `div` overlay rỗng.
  - Thẻ `figcaption.about-badge` vẫn được giữ nguyên là `<figcaption>` trong mã nguồn cuối (hợp lệ bên trong `<figure>`), không đổi sang `<span>` như prompt Lần 2 từng yêu cầu.
  - Form đặt bàn có ngày tối thiểu là hôm nay và có thông báo `role="status"`.
  - Chuyển `--transition` từ `all` sang danh sách thuộc tính cụ thể, đưa kích thước ảnh và `--container-max` vào biến.

- **Nhận xét & Tinh chỉnh của SV:**
  - Điểm khác biệt so với Lần 1 là tôi **nêu đúng nguyên nhân gốc** (AOS ghi đè `transform`, `pulse` giữ `fill-mode: both`, `padding-left` gây layout shift) và đưa luôn rule gợi ý, nên AI sửa đúng chỗ mà không phá thư viện.
  - Tôi sắp xếp việc theo mức ưu tiên P1 → P5 (lỗi tương tác trước, ngữ nghĩa và tính năng phụ sau) và giới hạn JS "tối đa khoảng 15 dòng" để giữ tinh thần trang tĩnh.
  - Tôi tự điều chỉnh chỉ đạo của mình so với Lần 1: bỏ `animate__infinite` của nút CTA, đổi sang `animate__repeat-2` kèm delay khớp với chuỗi hero để không gây rối mắt.

- **Thách thức gặp phải:**
  1. Rule hover thông thường không thắng được rule của AOS (`[data-aos].aos-animate`) vì độ đặc hiệu thấp hơn.
  2. Chỉ đạo về heading của tôi **không nhất quán** giữa hai lần: Lần 1 yêu cầu `h4` → `h3` trong `.contact-detail`, Lần 2 lại yêu cầu `h3` → `h4`. Kết quả cuối cùng tôi chuyển các nhãn này thành `<p class="contact-label">` vì chúng là nhãn thông tin chứ không phải tiêu đề mục.
  3. Prompt yêu cầu JS tối đa khoảng 15 dòng, nhưng khi bổ sung đủ chức năng thì đoạn JS cuối vượt giới hạn này (xem Lần 3).

- **Cách sửa prompt (Debug prompt):** Thay vì "hover bị lỗi", tôi viết: *"AOS gán transform sau khi animate nên đè `:hover`; thêm rule `[data-aos].aos-animate:hover` đặt `transform: translateY(-8px)` và `transition-delay: 0s`"*. Mô tả cơ chế lỗi kèm giải pháp giúp AI cho kết quả đúng ngay lần đầu.

---

### Lần 3: Review chéo và hoàn thiện cuối (sửa lỗi còn sót, đồng bộ nhật ký với mã nguồn)

Sau Lần 2, tôi nhờ Claude đọc lại toàn bộ `index.html`, `style.css`, `PROMPT_LOG.md` và chấm theo thang điểm của đề. Từ danh sách lỗi nhận được, tôi chọn các mục cần sửa và dùng Claude soạn hai prompt bên dưới. Tôi chạy hai prompt này trên Google Antigravity / Gemini 3.8 để sửa code (Prompt 3A) và sửa các chỗ sai sự thật trong nhật ký (Prompt 3B). Những chỗ cần lời của riêng tôi (lý do chọn thiết kế, nguồn bản review) tôi tự viết, không giao cho AI.

**Các lỗi tôi chọn sửa sau review:**
- Nút mạng xã hội ở footer khi hover vẫn dùng chữ trắng trên nền `--color-secondary` (#c89666), tức là đúng lỗi tương phản 2.6:1 đã sửa ở nút khác nhưng bị bỏ sót.
- Nút CTA ở Hero (nền xanh rêu) hòa vào lớp phủ xanh đen của ảnh nền.
- Hai khối `@media (max-width: 1024px)` tách riêng.
- Một số giá trị cứng (`1.5px`, `40px`, `44px`, `12px`, `440px`...) chưa đưa vào biến; `--transition` còn chứa `width` không cần thiết.
- Ảnh sản phẩm dùng `div.product-figure` dù đề yêu cầu `<figure>`.
- Form chọn giờ 06:30–23:00 trong khi T2–T6 quán mở 07:00–22:30; lựa chọn "Trên 5 người" bỏ sót nhóm đúng 5 người.
- Pulse của nút CTA bắt đầu ở 1.6s, chồng lên lúc nhóm nút còn đang xuất hiện (xong ở khoảng 1.8s).

- **Prompt 3A (sửa code) đã dùng:**

```text
# VAI TRÒ
Bạn là Senior Front-end Engineer. Hãy sửa lỗi cho landing page "Cafe Lạc Hồng" (index.html, style.css trong workspace) theo danh sách bên dưới. Đây là bài thi giữa kỳ môn Thiết kế Web nên phải giữ đúng các yêu cầu bắt buộc của đề.

# RÀNG BUỘC (BẮT BUỘC)
1. Chỉ sửa index.html và style.css. KHÔNG sửa PROMPT_LOG.md, không đụng vào assets/.
2. Chỉ sửa đúng các mục liệt kê. Không refactor thêm, không đổi tên class, không đổi nội dung chữ, không đổi màu thương hiệu (xanh rêu + kem).
3. Giữ nguyên Semantic HTML5, CSS Variables, Flexbox/Grid, Animate.css 4.1.1, AOS 2.3.4, hiệu ứng :hover/:focus tự viết, và toàn bộ khối <script> hiện có (không thêm JS mới).
4. Mọi giá trị mới (màu, kích thước, khoảng cách) phải khai báo bằng CSS Variables trong :root, không hard-code.
5. Comment viết bằng tiếng Việt, ngắn gọn.

# DANH SÁCH CÔNG VIỆC

## A. Tương phản và giao diện (style.css)
A1. .social-btn:hover, .social-btn:focus-visible đang dùng chữ trắng trên nền var(--color-secondary) (#c89666), tương phản chỉ khoảng 2.6:1. Đổi nền sang var(--color-secondary-strong) (hoặc biến tương đương đạt >= 4.5:1). Tính lại tỉ lệ tương phản và ghi kết quả trong báo cáo cuối.
A2. Nút .hero-actions .btn-primary (nền xanh rêu) đang hòa vào lớp phủ xanh đen của hero. Làm nút nổi bật hơn mà không đổi bảng màu: thêm viền sáng (ví dụ var(--color-secondary-light)) hoặc đổi nền sang var(--color-secondary-strong) cho riêng nút này ở hero. Chọn phương án đẹp hơn, đảm bảo chữ đạt >= 4.5:1 và viền/nút đạt >= 3:1 so với nền xung quanh. Kiểm tra lại hover/focus.
A3. Hai khối @media (max-width: 1024px) (mục 12 và 13) đang tách riêng. Gộp thành MỘT khối duy nhất, giữ nguyên toàn bộ rule, sắp xếp theo nhóm có comment (lưới, hamburger menu). Cập nhật lại comment tiêu đề mục.
A4. Bỏ "width 0.3s ..." khỏi biến --transition (không phần tử nào cần transition width). Trước khi xóa, tìm toàn bộ nơi dùng var(--transition) để chắc chắn không phần tử nào bị ảnh hưởng.
A5. Đưa các giá trị cứng còn sót vào biến :root và thay lại trong CSS, giữ nguyên giá trị hiển thị:
   - letter-spacing 1.5px, kích thước logo-mark 40px, icon 44px/42px, nút social 38px, avatar 48px, top/right 12px của .product-tag, bottom/left 20px của .about-badge, max-height 440px của menu mobile.
   - Đặt tên biến rõ nghĩa (ví dụ --size-logo-mark, --size-icon, --size-social, --size-avatar, --menu-max-h, --letter-spacing-wide).
   - Không cần biến hóa các giá trị 0/1px/2px/3px dùng cho viền, outline.

## B. Semantic HTML (index.html)
B1. Đổi div.product-figure thành <figure class="product-figure"> ở cả 6 card sản phẩm (ảnh bọc trong figure đúng ngữ nghĩa). Giữ nguyên <span class="product-tag"> bên trong. Kiểm tra CSS .product-figure vẫn hiển thị đúng (reset margin của figure đã có ở rule *).
B2. Giữ nguyên <figcaption class="about-badge"> trong figure ảnh about (hợp lệ). Không đổi.
B3. Trong các comment HTML, thay "&amp;" thành "&" (comment không cần escape). Chỉ sửa trong comment, KHÔNG sửa "&amp;" nằm trong nội dung hiển thị hoặc thuộc tính (title, href, Google Fonts URL...).

## C. Form đặt bàn (index.html)
C1. Giờ mở cửa T2-T6 là 07:00-22:30 nhưng input giờ cho chọn 06:30-23:00. Đổi min="07:00", max="22:30" và nhãn thành "Khung giờ (07:00 – 22:30)", rồi thêm vào nhãn hoặc một dòng chú thích nhỏ dưới ô: "T7 - CN mở cửa sớm 06:30, đến 23:00, vui lòng ghi chú nếu cần". Không thêm JS.
C2. <option value="5+">Trên 5 người (nhóm)</option> loại bỏ nhóm đúng 5 người. Đổi chữ thành "5 người trở lên (nhóm)".

## D. Hiệu ứng Hero (style.css)
D1. Nhóm nút hero bắt đầu ở 0.8s và fadeInUp kéo dài 1s nên xong ở khoảng 1.8s, trong khi pulse của nút CTA bắt đầu ở 1.6s (chồng lên). Đổi animation-delay của .hero-actions .btn-primary thành 1.8s, giữ animation-fill-mode: backwards. Cập nhật comment cho khớp.

# KIỂM TRA TRƯỚC KHI BÁO CÁO
- Mở index.html và kiểm tra HTML hợp lệ (không thẻ lỗi, id không trùng).
- Rà lại toàn bộ style.css: không còn selector nào trỏ tới thứ đã đổi tên/xóa.
- Grep style.css xem còn giá trị màu hex hoặc rgba hard-code ngoài :root không.

# BÁO CÁO CUỐI
Liệt kê ngắn gọn theo từng mục A1-D1: đã sửa gì, ở dòng/selector nào, và tỉ lệ tương phản mới cho A1, A2. Nếu mục nào không làm được hoặc làm khác chỉ dẫn, nói rõ lý do.
```

- **Prompt 3B (sửa các chỗ sai sự thật trong nhật ký) đã dùng:**

```text
# VAI TRÒ
Bạn là biên tập viên kỹ thuật. Hãy chỉnh PROMPT_LOG.md (nhật ký prompt của sinh viên) chỉ ở các điểm bên dưới.

# RÀNG BUỘC
- KHÔNG sửa các khối prompt nằm trong ``` (giữ nguyên văn).
- KHÔNG tự bịa prompt, lý do hay nội dung cho các chỗ SV phải tự điền (dấu [SV ...]). Giữ nguyên các placeholder đó.
- Văn phong ngôi thứ nhất ("tôi"), tự nhiên, bỏ giọng giải thích như người ngoài viết.

# CÔNG VIỆC
1. Lần 3, phần "Thách thức gặp phải": bỏ ý "Các phần tử Hero thiếu animation-fill-mode: backwards có nguy cơ nhấp nháy". Lý do: class animate__animated của Animate.css 4.1.1 đã đặt animation-fill-mode: both. Thay bằng một ý đúng: hai khối @media (max-width: 1024px) đã được gộp ở bước sau (nếu đã làm theo bản sửa code).
2. Lần 3, bảng đối chiếu: sửa dòng "JS inline" cho khớp số dòng thực tế của khối <script> trong index.html (đếm lại, kể cả comment).
3. Lần 2, "Kết quả AI tạo ra": thêm một dòng ngắn nói rõ đoạn "figcaption.about-badge" vẫn là <figcaption> trong mã cuối (hợp lệ trong <figure>), không đổi sang <span> như prompt Lần 2 từng yêu cầu. Thêm vào bảng ở Lần 3 một dòng "Ảnh sản phẩm": prompt Lần 1 yêu cầu giữ <figure>, mã trước đó dùng div.product-figure, mã cuối dùng <figure class="product-figure">.
4. Phần đầu file: viết lại hai đoạn ghi chú meta ("Lưu ý về cách đọc nhật ký" và "Ghi chú trung thực") thành 2-3 câu ngôi thứ nhất, ngắn gọn, giữ ý chính là kết quả mô tả theo mã nguồn cuối và có chỗ mã cuối khác prompt.
5. Lần 2: ngay trước khối prompt, thêm dòng placeholder: "[SV điền: bản review ở prompt này do ai/công cụ nào tạo ra]". Không tự điền.

# BÁO CÁO
Liệt kê các dòng đã sửa và các placeholder còn lại cần SV điền.
```

- **Kết quả AI tạo ra (đối chiếu với mã nguồn cuối):**
  - `.social-btn:hover` đổi sang nền `--color-secondary-strong` (#8a5a2b), chữ trắng đạt khoảng 5.5:1.
  - Nút CTA Hero có viền `--color-secondary-light` nên tách khỏi lớp phủ; hover đổi viền sang trắng, nhấc lên bằng `transform`.
  - Hai khối `@media (max-width: 1024px)` được gộp thành một khối, chia hai nhóm có comment (lưới, hamburger menu).
  - Bỏ `width` khỏi `--transition`; thêm các biến `--letter-spacing-wide`, `--size-logo-mark`, `--size-point-icon`, `--size-contact-icon`, `--size-social`, `--size-avatar`, `--product-tag-offset`, `--about-badge-offset`, `--menu-max-h`.
  - 6 ảnh sản phẩm đổi sang `<figure class="product-figure">`; comment HTML không còn `&amp;`.
  - Ô giờ đặt bàn giới hạn 07:00–22:30 kèm ghi chú cho T7–CN (`.form-note`); lựa chọn nhóm đổi thành "5 người trở lên (nhóm)".
  - `animation-delay` của nút CTA Hero là 1.8s, nối tiếp ngay khi nhóm nút xuất hiện xong.

| Hạng mục | Prompt Lần 2 yêu cầu | Mã nguồn cuối nộp bài |
|---|---|---|
| Menu mobile | Checkbox hack + JS bỏ tick `#menu-toggle` | `<button class="menu-btn" aria-expanded aria-controls>` điều khiển bằng JS, tự đóng khi bấm link hoặc nhấn Escape, cập nhật `aria-label` |
| Breakpoint hamburger | 960px (hoặc 1024px) | 1024px (gộp cùng breakpoint tablet trong một khối `@media`) |
| Heading ở mục Liên hệ | `h4` | `<p class="contact-label">` (không dùng `h4`) |
| Ảnh sản phẩm | Prompt Lần 1 yêu cầu giữ `<figure>`, mã trước đó dùng `div.product-figure` | `<figure class="product-figure">` ở cả 6 card sản phẩm |
| `figcaption.about-badge` | Đổi sang `<span>` hoặc `<p>` | Giữ `<figcaption>` (hợp lệ trong `<figure>`) |
| Hover card sau AOS | Rule `[data-aos].aos-animate:hover` | Thêm class `.is-settled` do JS gán sau khi AOS chạy xong, để hover và rời chuột phản hồi nhanh |
| Fallback khi CDN AOS lỗi | `<noscript>` | `<noscript>` + class `.no-js` trên `<html>` khi `window.AOS` không tồn tại |
| Delay AOS trên màn hình hẹp | Chưa đề cập | JS gỡ `data-aos-delay` khi viewport ≤ 1024px |
| Pulse nút CTA Hero | Bắt đầu ở 1.6s | Bắt đầu ở 1.8s, sau khi nhóm nút xuất hiện xong |
| Giờ đặt bàn | Chưa đề cập | Giới hạn 07:00–22:30 theo giờ T2–T6, có ghi chú cho T7–CN |
| JS inline | Tối đa khoảng 15 dòng | 70 dòng kể cả comment và cặp thẻ script (AOS có guard, menu, ngày tối thiểu theo giờ địa phương, phản hồi form) |

- **Nhận xét & Tinh chỉnh của SV:**
  - Menu chuyển từ checkbox hack sang `<button>` vì nút thật có `aria-expanded`, nhận biết trạng thái cho trình đọc màn hình và xử lý phím Escape tốt hơn.
  - Đoạn JS vượt giới hạn "~15 dòng" mà tôi tự đặt ở Lần 2. Tôi chấp nhận vì các phần thêm đều phục vụ độ ổn định và trợ năng (guard khi CDN lỗi, menu bằng bàn phím), không dùng JS để render nội dung. Danh sách sản phẩm vẫn viết tĩnh bằng HTML.
  - Ở Lần 3 tôi chủ động dùng một AI khác (Claude) để review chéo thay vì tự hỏi lại chính công cụ đã sinh code, vì cùng một công cụ dễ bỏ sót lại đúng lỗi nó đã mắc. Cách này tìm ra lỗi tương phản ở nút mạng xã hội mà tôi đã bỏ qua.
  - Khi đọc bản review, tôi kiểm tra từng nhận định thay vì làm theo toàn bộ. Ví dụ, ý "thiếu `animation-fill-mode: backwards` ở các phần tử Hero có thể gây nhấp nháy" không đúng, vì class `animate__animated` của Animate.css 4.1.1 đã đặt `animation-fill-mode: both`, nên tôi không đưa mục này vào prompt sửa.
  - Tôi giữ ràng buộc "chỉ sửa đúng các mục liệt kê, không refactor thêm" và yêu cầu AI báo cáo từng mục, nhờ đó phần giao diện đã ổn không bị viết lại.

- **Thách thức gặp phải:**
  1. Mã nguồn đã khá phức tạp (nhiều rule tăng độ đặc hiệu như `[data-aos][data-aos]`, nhiều lớp xử lý AOS), nên mỗi lần sửa phải kiểm tra để không phá rule hover đã có.
  2. Hai khối `@media (max-width: 1024px)` từng bị tách riêng ở phần tinh chỉnh lưới và menu mobile; sau đó tôi đã gộp thành một khối duy nhất để quản lý tập trung và tránh trùng lặp.
  3. Thông tin giờ mở cửa và form đặt bàn ban đầu mâu thuẫn nhau (form cho chọn 06:30–23:00 trong khi T2–T6 mở 07:00–22:30), chứng tỏ cần đối chiếu cả nội dung chứ không chỉ cấu trúc code.

- **Cách sửa prompt (Debug prompt):** Với mỗi lỗi, tôi ghi rõ **vị trí, nguyên nhân kỹ thuật, kết quả mong muốn và tiêu chí kiểm tra** thay vì chỉ nói "sửa cho đẹp hơn". Ví dụ ở mục A1 tôi viết: *"`.social-btn:hover` dùng chữ trắng trên nền `--color-secondary` (#c89666), tương phản chỉ khoảng 2.6:1; đổi nền sang biến đạt >= 4.5:1 và ghi lại tỉ lệ mới trong báo cáo"*; ở mục D1: *"nhóm nút xong ở khoảng 1.8s nhưng pulse bắt đầu ở 1.6s nên chồng lên nhau; đổi `animation-delay` thành 1.8s"*. Tôi cũng yêu cầu AI tự kiểm tra (HTML hợp lệ, không selector mồ côi, không màu hard-code ngoài `:root`) và báo cáo theo từng mục để tôi đối chiếu lại với mã nguồn.

---

## 3. Đánh giá cá nhân về quá trình Vibe Coding

**AI đã giúp tôi tiết kiệm thời gian ở phần nào nhất?**
- Dựng nhanh khung HTML/CSS đủ 5 section và bộ biến `:root` ngay từ prompt đầu tiên.
- Các việc lặp lại và dễ sai tay: đổi tên thẻ trên nhiều chỗ, thêm `aria-hidden` cho toàn bộ icon, thêm `aria-labelledby` cho từng section, tải và đổi tên 11 ảnh, đưa giá trị cứng vào biến CSS.
- Viết các rule xử lý xung đột thư viện khi tôi đã chỉ ra nguyên nhân.

**Những lỗi AI đã mắc mà tôi phải phát hiện và chỉ đạo sửa:**
1. Lưới sản phẩm `auto-fit/minmax` làm lệch 4+2 card trên màn hình lớn.
2. Màu chữ trắng trên nền caramel chỉ đạt khoảng 2.6:1, chưa đạt WCAG AA (lỗi này còn tái xuất hiện ở nút mạng xã hội và chỉ được phát hiện ở lượt review chéo).
3. Dùng `<article>` cho các khối không độc lập và gắn AOS lồng nhau trên cả section lẫn card.
4. AOS ghi đè `transform` của `:hover`, và `animate__pulse` giữ `fill-mode: both` làm mất hover của nút.
5. Nội dung không nhất quán: giờ trong form đặt bàn khác giờ mở cửa; ảnh sản phẩm không dùng `<figure>` như đề yêu cầu.

**Bài học rút ra khi đặt câu hỏi (Prompt) cho AI trong thiết kế web:**
1. **Mô tả nguyên nhân kỹ thuật, không chỉ mô tả triệu chứng.** "Hover bị lỗi" cho kết quả mơ hồ; "AOS ghi đè `transform` sau khi animate" cho kết quả đúng ngay.
2. **Lặp từng bước thay vì đòi một prompt hoàn hảo.** Dựng khung trước, rồi rà chuẩn ngữ nghĩa và trợ năng, rồi mới tinh chỉnh tương tác.
3. **Dùng ràng buộc phủ định và giới hạn phạm vi** ("không thêm framework", "không đổi màu thương hiệu", "chỉ sửa những gì liệt kê") để AI không tự ý viết lại những phần đã ổn.
4. **Phải kiểm tra lại kết quả trên trình duyệt và cập nhật nhật ký.** Có những thay đổi sau cùng (menu dùng nút `<button>`, đoạn JS dài hơn dự kiến) đi lệch khỏi prompt ban đầu; nếu không ghi lại thì nhật ký sẽ không còn phản ánh đúng sản phẩm.
5. **Review chéo bằng một AI khác và tự kiểm chứng nhận định của nó.** AI viết code thường bỏ sót chính lỗi nó đã mắc, nhưng AI review cũng có thể nhận định sai (như vụ `fill-mode`), nên mọi gợi ý đều phải đối chiếu với tài liệu thư viện trước khi đưa vào prompt.
 