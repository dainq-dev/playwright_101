# 🎭 Playwright 101 - Hướng Dẫn Toàn Diện Cho Người Mới

> **Tài liệu dựa trên:** [Playwright Official Docs](https://playwright.dev/docs/intro) - Cập nhật 2026

## 📚 Mục lục
1. [Playwright là gì?](#playwright-là-gì)
2. [Cài đặt và chạy test đầu tiên](#cài-đặt-và-chạy-test-đầu-tiên)
3. [Các khái niệm cơ bản](#các-khái-niệm-cơ-bản)
4. [Locators - Cách tìm elements](#locators---cách-tìm-elements)
5. [Actions - Tương tác với UI](#actions---tương-tác-với-ui)
6. [Assertions - Kiểm tra kết quả](#assertions---kiểm-tra-kết-quả)
7. [Auto-waiting và Actionability](#auto-waiting-và-actionability)
8. [Waiting Strategies](#waiting-strategies)
9. [Test Hooks và Fixtures](#test-hooks-và-fixtures)
10. [Page Object Model (POM)](#page-object-model-pom)
11. [Debugging và Tools](#debugging-và-tools)
12. [Best Practices](#best-practices)
13. [Ví dụ thực tế từ dự án](#ví-dụ-thực-tế-từ-dự-án)
14. [Troubleshooting - Xử lý lỗi thường gặp](#troubleshooting---xử-lý-lỗi-thường-gặp)

---

## Playwright là gì?

**Playwright** là framework **tự động hóa test end-to-end (E2E)** cho web applications, được phát triển bởi **Microsoft** (team cũ của Puppeteer).

### 🌟 Ưu điểm nổi bật:
- ✅ **Cross-browser**: Hỗ trợ Chromium, Firefox, WebKit (Safari)
- ✅ **Cross-platform**: Chạy trên Windows, Linux, macOS
- ✅ **Auto-waiting thông minh**: Tự động đợi elements sẵn sàng
- ✅ **Fast & Reliable**: Chạy nhanh, ổn định, ít flaky tests
- ✅ **Powerful API**: API mạnh mẽ, dễ sử dụng
- ✅ **TypeScript first-class**: Hỗ trợ TypeScript tuyệt vời
- ✅ **Parallel execution**: Chạy test song song mặc định
- ✅ **Built-in tools**: Codegen, Trace Viewer, UI Mode

### 🆚 So với các framework khác:
- **Selenium**: Playwright nhanh hơn, ổn định hơn, API hiện đại hơn
- **Cypress**: Playwright hỗ trợ nhiều browser hơn, test cross-domain tốt hơn
- **Puppeteer**: Playwright hỗ trợ nhiều browser, API phong phú hơn

---

## Cài đặt và chạy test đầu tiên

### 1. Cài đặt Playwright

```bash
# Khởi tạo project mới với Playwright
npm init playwright@latest

# Hoặc thêm vào project có sẵn
npm install -D @playwright/test
npx playwright install
```

**Lệnh `npm init playwright@latest` sẽ:**
- ✅ Tạo file config `playwright.config.ts`
- ✅ Tạo folder `tests/` với ví dụ
- ✅ Tạo GitHub Actions workflow (optional)
- ✅ Cài đặt browsers

### 2. Cấu trúc project

```
my-project/
├── tests/
│   └── example.spec.ts          # Test files
├── playwright.config.ts          # Cấu hình
├── package.json
└── test-results/                 # Kết quả test
```

### 3. Chạy test

```bash
# Chạy tất cả tests
npx playwright test

# Chạy 1 file cụ thể
npx playwright test tests/example.spec.ts

# Chạy ở chế độ UI (interactive)
npx playwright test --ui

# Chạy ở chế độ headed (thấy browser)
npx playwright test --headed

# Chạy trên browser cụ thể
npx playwright test --project=chromium
npx playwright test --project=firefox

# Chạy test có chứa từ khóa
npx playwright test -g "login"

# Debug mode
npx playwright test --debug
```

### 4. Xem kết quả

```bash
# Mở HTML report
npx playwright show-report

# Xem trace (chi tiết từng bước)
npx playwright show-trace trace.zip
```

---

## Các khái niệm cơ bản

### 1. **Test** - Một test case
```typescript
import { test, expect } from '@playwright/test';

test('tên test của bạn', async ({ page }) => {
  // Code test ở đây
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example/);
});
```

### 2. **Browser** - Trình duyệt
```typescript
// Playwright tự động quản lý browser
// Bạn không cần launch/close manually trong test
// Browser được khởi tạo từ fixtures { browser }
```

### 3. **BrowserContext** - Phiên làm việc độc lập
```typescript
// Context = incognito mode
// Mỗi test chạy trong context riêng
// Có cookies, localStorage riêng
// Tự động được tạo qua fixture { context }
```

### 4. **Page** - Trang web (Tab)
```typescript
test('ví dụ về page', async ({ page }) => {
  // page được inject tự động qua fixture
  await page.goto('https://example.com');

  // Mở tab mới
  const newPage = await context.newPage();
});
```

### 5. **Locator** - Công cụ tìm element
```typescript
// Locator KHÔNG tìm element ngay
// Nó chỉ lưu "cách tìm"
// Element chỉ được tìm khi thực hiện action
const button = page.locator('button.submit');

// Khi click mới thực sự tìm element
await button.click(); // ← Lúc này mới tìm và click
```

**Quan trọng:** Locator là **lazy** và **strict** - giúp test ổn định hơn!

---

## Các khái niệm cơ bản

### 1. **Browser** - Trình duyệt
```typescript
// Khởi tạo browser
const browser = await chromium.launch();
```

### 2. **Page** - Trang web
```typescript
// Tạo một trang mới (như mở tab mới)
const page = await browser.newPage();

// Điều hướng đến URL
await page.goto('https://example.com');
```

### 3. **Locator** - Công cụ tìm element
```typescript
// Locator là cách Playwright tìm và tương tác với elements
const button = page.locator('button.submit');
const input = page.getByRole('textbox');
```

**Quan trọng**: Locator chưa tìm element ngay lập tức, nó chỉ lưu "cách tìm". Element chỉ được tìm khi bạn thực hiện action (click, fill, etc.)

---

## Locators - Cách tìm elements

> **📖 Tài liệu chính thức:** [Playwright Locators](https://playwright.dev/docs/locators) | [Best Practices](https://playwright.dev/docs/best-practices)

### 🎯 Thứ tự ưu tiên (Theo Playwright recommends)

Playwright khuyến nghị sử dụng locators theo thứ tự sau (từ tốt nhất → ít tốt nhất):

```
1. getByRole()        ← Tốt nhất (user-facing, accessibility)
2. getByLabel()       ← Tốt (for forms)
3. getByPlaceholder() ← Tốt (for inputs)
4. getByText()        ← Khá tốt (for non-interactive)
5. getByTestId()      ← OK (khi không có cách khác)
6. CSS/XPath          ← Tránh (dễ break, phụ thuộc DOM)
```

---

### 1. **getByRole** - Tìm theo vai trò (⭐ KHUYẾN NGHỊ CAO NHẤT)

Tìm element theo **ARIA role**, gần nhất với cách người dùng và screen reader nhìn trang.

```typescript
// Tìm button theo tên
await page.getByRole('button', { name: 'Submit' }).click();
await page.getByRole('button', { name: /submit/i }).click(); // Regex, case insensitive

// Tìm textbox/input
await page.getByRole('textbox', { name: 'Email' }).fill('test@example.com');
await page.getByRole('textbox', { name: /email/i }).fill('test@example.com');

// Tìm link
await page.getByRole('link', { name: 'Home' }).click();

// Tìm heading
await page.getByRole('heading', { name: 'Welcome' });
await page.getByRole('heading', { level: 1 }); // <h1>

// Tìm checkbox
await page.getByRole('checkbox', { name: 'Accept terms' }).check();

// Tìm radio button
await page.getByRole('radio', { name: 'Male' }).click();

// Tìm listitem
await page.getByRole('listitem').filter({ hasText: 'Apple' }).click();
```

**Các role phổ biến:**
- `button`, `link`, `textbox`, `checkbox`, `radio`
- `heading`, `list`, `listitem`, `table`, `row`, `cell`
- `tab`, `tabpanel`, `dialog`, `menu`, `menuitem`

**Ưu điểm:**
- ✅ Ổn định nhất (không phụ thuộc DOM structure)
- ✅ Accessibility-friendly (đảm bảo app có thể dùng với screen reader)
- ✅ Gần với cách người dùng tương tác

**Khi nào dùng:** Luôn ưu tiên `getByRole` trước tiên!

---

### 2. **getByLabel** - Tìm input theo label (form fields)

```typescript
// HTML: <label>Email<input /></label>
await page.getByLabel('Email').fill('test@example.com');

// HTML: <label for="email">Email</label><input id="email" />
await page.getByLabel('Email').fill('test@example.com');

// Regex
await page.getByLabel(/email/i).fill('test@example.com');
```

**Khi nào dùng:** Khi làm việc với forms có labels

---

### 3. **getByPlaceholder** - Tìm theo placeholder

```typescript
// HTML: <input placeholder="Enter your email" />
await page.getByPlaceholder('Enter your email').fill('test@example.com');

// Regex
await page.getByPlaceholder(/email/i).fill('test@example.com');
```

**Khi nào dùng:** Khi input có placeholder rõ ràng

---

### 4. **getByText** - Tìm theo text hiển thị

```typescript
// Tìm text chính xác (exact match)
await page.getByText('Submit').click();
await page.getByText('Welcome to our site').click();

// Tìm text không chính xác (substring)
await page.getByText('Submit', { exact: false }).click(); // Match "Submit", "Submit now"

// Regex
await page.getByText(/submit/i).click(); // Case insensitive
```

**Khi nào dùng:** Cho text hiển thị (headings, paragraphs, labels)

**Lưu ý:** Chỉ dùng cho text hiển thị, không dùng cho interactive elements (button, link)

---

### 5. **getByTestId** - Tìm theo data-testid (PHỔ BIẾN trong dự án)

```typescript
// HTML: <button data-testid="submit-button">Submit</button>
await page.getByTestId('submit-button').click();

// Trong code của bạn:
await this.page.getByTestId('tab-item').click();
await this.page.getByTestId('button-them-kich-ban').click();
```

**Khi nào dùng:**
- ✅ Khi không có role/label/text rõ ràng
- ✅ Khi team dev thêm `data-testid` vào code
- ✅ Cho các element động, phức tạp

**Lưu ý:** Cần team dev hợp tác thêm attribute

---

### 6. **locator** - Tìm theo CSS selector (⚠️ Dùng khi cần thiết)

```typescript
// Tìm theo class
await page.locator('.submit-button').click();

// Tìm theo id
await page.locator('#user-name').fill('John');

// Tìm theo tag + class
await page.locator('button.primary').click();

// Tìm theo attribute
await page.locator('[data-testid="submit"]').click();

// Tìm theo nth-child (tránh nếu có thể)
await page.locator('ul > li:nth-child(3)').click();
```

**Khi nào dùng:** Khi không có cách nào khác (last resort)

**Tránh:** CSS phức tạp như `div.container > ul > li:nth-child(3) > button.primary`

---

### 7. **getByAltText** - Tìm img theo alt text

```typescript
// HTML: <img alt="Company logo" />
await page.getByAltText('Company logo').click();
```

---

### 8. **getByTitle** - Tìm theo title attribute

```typescript
// HTML: <button title="Close">×</button>
await page.getByTitle('Close').click();
```

---

### 🔧 Các method kết hợp

#### **filter()** - Lọc locator
```typescript
// Tìm tất cả buttons, lọc button có text "Delete"
await page.locator('button').filter({ hasText: 'Delete' }).click();

// Filter theo class
await page.getByRole('listitem').filter({ hasClass: 'active' }).click();

// Filter bằng locator khác
await page
  .getByRole('listitem')
  .filter({ has: page.getByRole('heading', { name: 'Item 1' }) })
  .click();

// Trong code của bạn:
const tab = this.tabItem.filter({ hasText: 'Kịch bản 1' });
```

#### **nth()** - Lấy element thứ N
```typescript
// Lấy button đầu tiên (index 0)
await page.getByRole('button').nth(0).click();

// Lấy button thứ 3 (index 2)
await page.getByRole('button').nth(2).click();

// Lấy button cuối cùng
await page.getByRole('button').nth(-1).click();

// Trong code của bạn:
return this.tabItem.nth(filter.index - 1); // index 1-based → 0-based
```

#### **first()** / **last()** - Lấy element đầu/cuối
```typescript
// Lấy button đầu tiên
await page.getByRole('button').first().click();

// Lấy button cuối cùng
await page.getByRole('button').last().click();
```

#### **count()** - Đếm số lượng
```typescript
// Đếm số button
const count = await page.getByRole('button').count();
console.log(`Có ${count} buttons`);

// Trong code của bạn:
async thayTongSoKichBan() {
  return await this.tabItem.count();
}
```

#### **all()** - Lấy tất cả elements thành array
```typescript
// Lấy tất cả buttons
const buttons = await page.getByRole('button').all();
for (const button of buttons) {
  await button.click();
}
```

#### **and()** / **or()** - Kết hợp điều kiện
```typescript
// AND: Element phải thỏa cả 2 điều kiện
await page
  .getByRole('button')
  .and(page.getByText('Submit'))
  .click();

// OR: Element thỏa 1 trong 2
await page
  .getByRole('button', { name: 'Submit' })
  .or(page.getByRole('button', { name: 'Send' }))
  .click();
```

---

### 🎯 Cheat Sheet - Locator nào cho trường hợp nào?

| Tình huống | Locator khuyến nghị | Ví dụ |
|-----------|---------------------|-------|
| Button, link, heading | `getByRole()` | `getByRole('button', { name: 'Submit' })` |
| Form field có label | `getByLabel()` | `getByLabel('Email')` |
| Input có placeholder | `getByPlaceholder()` | `getByPlaceholder('Enter email')` |
| Text hiển thị | `getByText()` | `getByText('Welcome')` |
| Image | `getByAltText()` | `getByAltText('Logo')` |
| Element có data-testid | `getByTestId()` | `getByTestId('submit-btn')` |
| Không có cách nào khác | `locator()` | `locator('.my-class')` |

---

## Actions - Tương tác với UI

> **📖 Docs:** [Actions](https://playwright.dev/docs/input) | [Actionability](https://playwright.dev/docs/actionability)

Tất cả actions đều có **auto-waiting** và **actionability checks** trước khi thực hiện.

---

### 1. **click()** - Click chuột

```typescript
// Click thường
await page.getByRole('button', { name: 'Submit' }).click();

// Click với options
await page.getByRole('button').click({
  button: 'right',      // Left (default) | right | middle
  clickCount: 2,        // Double click
  delay: 100,           // Delay giữa mousedown và mouseup (ms)
  position: { x: 10, y: 10 }, // Click vào vị trí cụ thể
  modifiers: ['Control'], // Giữ phím Control khi click
  force: true,          // Bỏ qua actionability checks (không khuyến nghị)
  noWaitAfter: true,    // Không đợi navigation sau click
});

// Double click
await page.getByRole('input').dblclick();

// Right click
await page.getByRole('div').click({ button: 'right' });

// Hover (di chuột qua)
await page.getByRole('button').hover();
```

**Lưu ý:** Click tự động đợi element:
- ✅ Attached to DOM
- ✅ Visible
- ✅ Stable (không di chuyển)
- ✅ Enabled (không disabled)
- ✅ Không bị che bởi element khác

---

### 2. **fill()** - Nhập text vào input (khuyến nghị)

```typescript
// Clear và nhập text mới (nhanh)
await page.getByLabel('Email').fill('test@example.com');

// Trong code của bạn:
await this.inputTenGiaTri.fill(ten);
await this.tenKichBan.fill('Kịch bản mới');

// Fill với options
await page.getByLabel('Email').fill('test@example.com', {
  force: true,      // Bỏ qua checks
  noWaitAfter: true, // Không đợi sau fill
});
```

**Đặc điểm:**
- ✅ Tự động clear value cũ
- ✅ Nhanh (không trigger keyboard events)
- ✅ Phù hợp cho hầu hết trường hợp

---

### 3. **type()** - Gõ từng ký tự (như người thật)

```typescript
// Gõ chậm từng ký tự (trigger keyboard events)
await page.getByLabel('Email').type('test@example.com', { delay: 100 });

// Khi nào dùng: Khi app cần keyboard events (autocomplete, onkeypress)
```

**Khác với fill():**
- `fill()`: Nhanh, không trigger events
- `type()`: Chậm, trigger keyboard events (keydown, keypress, keyup)

---

### 4. **press()** / **pressSequentially()** - Nhấn phím

```typescript
// Nhấn 1 phím
await page.getByLabel('Email').press('Enter');
await page.getByLabel('Email').press('Tab');
await page.getByLabel('Email').press('Escape');

// Nhấn tổ hợp phím
await page.getByLabel('Email').press('Control+A'); // Ctrl+A
await page.getByLabel('Email').press('Meta+A');    // Cmd+A (Mac)

// Nhấn từng phím liên tiếp
await page.getByLabel('Email').pressSequentially('Hello', { delay: 100 });

// Trong code của bạn:
await this.tenKichBan.press('Enter'); // Sau khi fill xong
```

**Các phím phổ biến:**
- `Enter`, `Tab`, `Escape`, `Backspace`, `Delete`
- `ArrowLeft`, `ArrowRight`, `ArrowUp`, `ArrowDown`
- `Control`, `Alt`, `Shift`, `Meta`

---

### 5. **check() / uncheck() / setChecked()** - Checkbox & Radio

```typescript
// Check checkbox
await page.getByRole('checkbox', { name: 'Accept terms' }).check();

// Uncheck checkbox
await page.getByRole('checkbox', { name: 'Accept terms' }).uncheck();

// Set trạng thái cụ thể (recommended)
await page.getByRole('checkbox', { name: 'Accept terms' }).setChecked(true);
await page.getByRole('checkbox', { name: 'Accept terms' }).setChecked(false);

// Trong code của bạn:
await this.options.bao_hiem_sinh_menh.check();

// Check radio button
await page.getByRole('radio', { name: 'Male' }).check();
```

**Lưu ý:** `setChecked()` an toàn hơn vì không lỗi nếu element đã ở trạng thái mong muốn

---

### 6. **selectOption()** - Select dropdown

```typescript
// Chọn option theo value
await page.locator('select').selectOption('vietnam');

// Chọn option theo label
await page.locator('select').selectOption({ label: 'Việt Nam' });

// Chọn option theo index
await page.locator('select').selectOption({ index: 2 });

// Chọn nhiều options (multi-select)
await page.locator('select').selectOption(['option1', 'option2']);
```

---

### 7. **upload files** - Upload file

```typescript
// Upload 1 file
await page.getByLabel('Upload').setInputFiles('path/to/file.pdf');

// Upload nhiều files
await page.getByLabel('Upload').setInputFiles([
  'file1.pdf',
  'file2.pdf',
]);

// Clear file input
await page.getByLabel('Upload').setInputFiles([]);

// Upload từ buffer
await page.getByLabel('Upload').setInputFiles({
  name: 'file.txt',
  mimeType: 'text/plain',
  buffer: Buffer.from('file content'),
});
```

---

### 8. **focus() / blur()** - Focus vào element

```typescript
// Focus vào input
await page.getByLabel('Email').focus();

// Blur (mất focus)
await page.getByLabel('Email').blur();
```

---

### 9. **clear()** - Xóa nội dung input

```typescript
// Xóa nội dung
await page.getByLabel('Email').clear();

// Note: fill() tự động clear rồi, không cần gọi clear() trước
```

---

### 10. **dragTo()** - Drag and drop

```typescript
// Drag element đến vị trí khác
await page.locator('#source').dragTo(page.locator('#target'));

// Drag với options
await page.locator('#source').dragTo(page.locator('#target'), {
  force: true,
  sourcePosition: { x: 10, y: 10 },
  targetPosition: { x: 20, y: 20 },
});
```

---

### 11. **scroll** - Scroll trang

```typescript
// Scroll element vào view
await page.getByRole('button', { name: 'Submit' }).scrollIntoViewIfNeeded();

// Scroll trang
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));

// Scroll trong element cụ thể
await page.locator('.scrollable').evaluate(el => el.scrollTop = 100);
```

---

### 12. **Lấy thông tin từ element**

```typescript
// Lấy text content
const text = await page.getByRole('heading').textContent();
console.log(text); // "Welcome"

// Lấy inner text (visible text only)
const innerText = await page.getByRole('heading').innerText();

// Lấy HTML
const html = await page.getByRole('heading').innerHTML();

// Lấy attribute
const href = await page.getByRole('link').getAttribute('href');
const dataId = await page.getByRole('button').getAttribute('data-id');

// Lấy input value
const value = await page.getByLabel('Email').inputValue();

// Check trạng thái
const isVisible = await page.getByRole('button').isVisible();
const isHidden = await page.getByRole('button').isHidden();
const isEnabled = await page.getByRole('button').isEnabled();
const isDisabled = await page.getByRole('button').isDisabled();
const isChecked = await page.getByRole('checkbox').isChecked();

// Count
const count = await page.getByRole('button').count();
```

---

## Assertions - Kiểm tra kết quả

> **📖 Docs:** [Assertions](https://playwright.dev/docs/test-assertions) | [Expect API](https://playwright.dev/docs/api/class-playwrightassertions)

Playwright assertions **tự động retry** cho đến khi điều kiện đúng hoặc timeout (default 5s).

---

### 🌟 Web-First Assertions (Auto-retrying)

Các assertion này **tự động retry** và **wait** → Test ổn định hơn!

#### **toBeVisible() / toBeHidden()** - Kiểm tra hiển thị

```typescript
// Element hiển thị
await expect(page.getByRole('button')).toBeVisible();

// Element ẩn
await expect(page.getByRole('button')).toBeHidden();

// Với timeout tùy chỉnh
await expect(page.getByRole('button')).toBeVisible({ timeout: 10000 }); // 10s
```

#### **toBeEnabled() / toBeDisabled()** - Kiểm tra enabled

```typescript
// Element enabled (có thể tương tác)
await expect(page.getByRole('button')).toBeEnabled();

// Element disabled
await expect(page.getByRole('button')).toBeDisabled();
```

#### **toBeChecked()** - Kiểm tra checkbox/radio

```typescript
// Checkbox đã check
await expect(page.getByRole('checkbox')).toBeChecked();

// Checkbox chưa check
await expect(page.getByRole('checkbox')).not.toBeChecked();
```

#### **toHaveText()** - Kiểm tra text

```typescript
// Text chính xác
await expect(page.getByRole('heading')).toHaveText('Welcome');

// Một phần text (substring)
await expect(page.getByRole('heading')).toHaveText('Wel', { useInnerText: true });

// Regex
await expect(page.getByRole('heading')).toHaveText(/welcome/i);

// Array of texts
await expect(page.getByRole('listitem')).toHaveText([
  'Item 1',
  'Item 2',
  'Item 3',
]);

// Ignore case
await expect(page.getByRole('heading')).toHaveText('welcome', {
  ignoreCase: true
});
```

#### **toContainText()** - Kiểm tra chứa text

```typescript
// Chứa substring
await expect(page.getByRole('paragraph')).toContainText('hello');

// Nhiều substrings
await expect(page.getByRole('listitem')).toContainText(['Item', 'Product']);

// Regex
await expect(page.getByRole('paragraph')).toContainText(/hello world/i);
```

#### **toHaveValue()** - Kiểm tra value của input

```typescript
// Input value
await expect(page.getByLabel('Email')).toHaveValue('test@example.com');

// Regex
await expect(page.getByLabel('Email')).toHaveValue(/@example\.com$/);
```

#### **toHaveCount()** - Kiểm tra số lượng

```typescript
// Có đúng 5 items
await expect(page.getByRole('listitem')).toHaveCount(5);

// Ít nhất 1 item
await expect(page.getByRole('listitem')).not.toHaveCount(0);
```

#### **toHaveAttribute()** - Kiểm tra attribute

```typescript
// Có attribute cụ thể
await expect(page.getByRole('link')).toHaveAttribute('href', '/home');

// Regex
await expect(page.getByRole('link')).toHaveAttribute('href', /^https:/);

// Chỉ kiểm tra tồn tại attribute
await expect(page.getByRole('button')).toHaveAttribute('disabled');
```

#### **toHaveClass()** - Kiểm tra CSS class

```typescript
// Có class cụ thể
await expect(page.getByRole('button')).toHaveClass('btn-primary');

// Chứa class trong danh sách classes
await expect(page.getByRole('button')).toHaveClass(/btn-primary/);

// Array of classes
await expect(page.getByRole('button')).toHaveClass(['btn', 'btn-primary']);
```

#### **toHaveCSS()** - Kiểm tra CSS property

```typescript
// Kiểm tra CSS property
await expect(page.getByRole('button')).toHaveCSS('background-color', 'rgb(255, 0, 0)');
await expect(page.getByRole('button')).toHaveCSS('font-size', '16px');
```

---

### 🌐 Page Assertions

#### **toHaveURL()** - Kiểm tra URL

```typescript
// URL chính xác
await expect(page).toHaveURL('https://example.com/home');

// Regex
await expect(page).toHaveURL(/.*\/home/);

// Chứa substring
await expect(page).toHaveURL(/example\.com/);
```

#### **toHaveTitle()** - Kiểm tra title

```typescript
// Title chính xác
await expect(page).toHaveTitle('Welcome - My Site');

// Regex
await expect(page).toHaveTitle(/Welcome/);
```

---

### 📊 Generic Assertions (Không auto-retry)

Các assertion này **KHÔNG auto-retry** → Chỉ check 1 lần!

```typescript
// So sánh giá trị
expect(value).toBe(5);
expect(value).toEqual({ name: 'John' });
expect(value).not.toBe(3);

// Truthy/Falsy
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeNull();
expect(value).toBeUndefined();
expect(value).toBeDefined();

// So sánh số
expect(value).toBeGreaterThan(5);
expect(value).toBeGreaterThanOrEqual(5);
expect(value).toBeLessThan(10);
expect(value).toBeLessThanOrEqual(10);

// String
expect(str).toContain('hello');
expect(str).toMatch(/hello/i);

// Array
expect(arr).toContain('item');
expect(arr).toHaveLength(5);

// Object
expect(obj).toHaveProperty('name');
expect(obj).toHaveProperty('name', 'John');
```

**Lưu ý:** Dùng generic assertions cho **logic checks**, không phải UI checks!

---

### 🔄 Negation - Phủ định

```typescript
// Thêm .not để phủ định
await expect(page.getByRole('button')).not.toBeVisible();
await expect(page.getByRole('button')).not.toBeDisabled();
await expect(page.getByRole('heading')).not.toHaveText('Old Title');
```

---

### 🕐 Timeout Configuration

```typescript
// Timeout cho 1 assertion (default: 5s)
await expect(page.getByRole('button')).toBeVisible({ timeout: 10000 }); // 10s

// Timeout cho tất cả assertions trong 1 test
test('my test', async ({ page }) => {
  test.setTimeout(30000); // 30s cho toàn bộ test

  await expect(page.getByRole('button')).toBeVisible();
});

// Cấu hình global trong playwright.config.ts
export default defineConfig({
  expect: {
    timeout: 10000, // 10s cho tất cả assertions
  },
});
```

---

### 💡 Soft Assertions - Tiếp tục test dù fail

```typescript
import { test, expect } from '@playwright/test';

test('multiple checks', async ({ page }) => {
  // Assertion bình thường: fail thì DỪNG ngay
  await expect(page.getByRole('h1')).toHaveText('Welcome'); // ← Fail → STOP

  // Soft assertion: fail vẫn CHẠY TIẾP
  await expect.soft(page.getByRole('h1')).toHaveText('Welcome'); // ← Fail → Continue
  await expect.soft(page.getByRole('h2')).toHaveText('Subtitle'); // ← Vẫn chạy
  await expect.soft(page.getByRole('p')).toHaveText('Content'); // ← Vẫn chạy

  // Test fail nếu có bất kỳ soft assertion nào fail
});
```

**Khi nào dùng:**
- ✅ Muốn check nhiều điều kiện trong 1 test
- ✅ Muốn thấy tất cả lỗi cùng lúc (không dừng ở lỗi đầu tiên)

---

### 🎯 Best Practices cho Assertions

```typescript
// ✅ TốT: Dùng web-first assertions (auto-retry)
await expect(page.getByRole('heading')).toHaveText('Welcome');
await expect(page.getByRole('button')).toBeVisible();

// ❌ TRÁNH: Dùng generic assertions cho UI checks
const text = await page.getByRole('heading').textContent();
expect(text).toBe('Welcome'); // ← Không retry, dễ fail

// ✅ TỐT: Specific matchers
await expect(page.getByRole('heading')).toHaveText('Welcome');

// ❌ TRÁNH: Generic matchers
const isVisible = await page.getByRole('heading').isVisible();
expect(isVisible).toBe(true); // ← Không retry

// ✅ TỐT: Check điều kiện trước khi interact
await expect(page.getByRole('button')).toBeEnabled();
await page.getByRole('button').click();

// ✅ TỐT: Dùng soft assertions cho multiple checks
await expect.soft(page.getByRole('h1')).toHaveText('Title');
await expect.soft(page.getByRole('h2')).toHaveText('Subtitle');
```

---

### 📋 Assertion Cheat Sheet

| Kiểm tra gì | Assertion | Auto-retry |
|------------|-----------|-----------|
| Element hiển thị | `toBeVisible()` | ✅ |
| Element ẩn | `toBeHidden()` | ✅ |
| Element enabled | `toBeEnabled()` | ✅ |
| Element disabled | `toBeDisabled()` | ✅ |
| Checkbox checked | `toBeChecked()` | ✅ |
| Text content | `toHaveText()` | ✅ |
| Chứa text | `toContainText()` | ✅ |
| Input value | `toHaveValue()` | ✅ |
| Số lượng | `toHaveCount()` | ✅ |
| Attribute | `toHaveAttribute()` | ✅ |
| CSS class | `toHaveClass()` | ✅ |
| CSS property | `toHaveCSS()` | ✅ |
| URL | `toHaveURL()` | ✅ |
| Title | `toHaveTitle()` | ✅ |
| So sánh giá trị | `toBe()` | ❌ |
| Array/Object | `toEqual()` | ❌ |

---

## Auto-waiting và Actionability

> **📖 Docs:** [Actionability](https://playwright.dev/docs/actionability) | [Auto-waiting](https://playwright.dev/docs/actionability)

### 🎯 Auto-waiting là gì?

**Playwright tự động đợi** element sẵn sàng trước mọi action → Test ổn định, không cần `sleep()`.

```typescript
// Playwright TỰ ĐỘNG đợi button:
// ✅ Attached to DOM
// ✅ Visible
// ✅ Stable (không di chuyển)
// ✅ Enabled
// ✅ Không bị element khác che
await page.getByRole('button').click();

// Bạn KHÔNG CẦN viết:
await page.waitForSelector('button');
await page.waitFor(1000);
await page.getByRole('button').click();
```

### 🔍 Actionability Checks

Trước mỗi action, Playwright check:

| Check | Mô tả |
|-------|-------|
| **Attached** | Element đã được attach vào DOM |
| **Visible** | Element có `width > 0`, `height > 0`, `opacity > 0` |
| **Stable** | Element không di chuyển (check 2 lần cách nhau 100ms) |
| **Receives Events** | Element không bị che bởi element khác |
| **Enabled** | Element không có `disabled` attribute (cho button, input) |
| **Editable** | Element không có `readonly` (cho input) |

```typescript
// Click tự động check: Attached, Visible, Stable, Receives Events, Enabled
await page.getByRole('button').click();

// Fill tự động check: Attached, Visible, Stable, Enabled, Editable
await page.getByLabel('Email').fill('test@example.com');

// Check checkbox: Attached, Visible, Stable, Receives Events
await page.getByRole('checkbox').check();
```

### ⏱️ Timeout

Mỗi action có timeout mặc định **30 giây**. Nếu element không sẵn sàng sau 30s → fail.

```typescript
// Timeout mặc định: 30s
await page.getByRole('button').click();

// Custom timeout cho 1 action
await page.getByRole('button').click({ timeout: 10000 }); // 10s

// Cấu hình global trong playwright.config.ts
export default defineConfig({
  timeout: 60000, // 60s cho mỗi test
  use: {
    actionTimeout: 10000, // 10s cho mỗi action
  },
});
```

### 🚫 Bỏ qua Actionability Checks (KHÔNG KHUYẾN NGHỊ)

```typescript
// Force click (bỏ qua tất cả checks)
await page.getByRole('button').click({ force: true });

// ⚠️ Chỉ dùng khi:
// - Element bị overlay che (modal, tooltip)
// - Element có opacity = 0 (animation)
// - Bạn CHẮC CHẮN element sẵn sàng
```

---

## Waiting Strategies

> **📖 Docs:** [Auto-waiting](https://playwright.dev/docs/actionability) | [Wait Types](https://www.browserstack.com/guide/playwright-wait-types)

Playwright **tự động đợi** element sẵn sàng. Nhưng đôi khi bạn cần đợi thêm.

### 1. **Auto-waiting** (Mặc định)
```typescript
// Playwright tự động đợi button:
// - Hiển thị (visible)
// - Enabled (không disabled)
// - Stable (không di chuyển)
await page.locator('button').click(); // ✅ Đợi tự động
```

### 2. **waitFor()** - Đợi element xuất hiện/biến mất
```typescript
// Đợi element hiển thị
await page.locator('.loading').waitFor({ state: 'visible' });

// Đợi element biến mất
await page.locator('.loading').waitFor({ state: 'hidden' });

// Đợi với timeout
await page.locator('.result').waitFor({ timeout: 5000 });

// Trong code của bạn:
await this.loading.waitFor({ state: 'hidden', timeout: 5000 });
```

### 3. **waitForResponse()** - Đợi API response
```typescript
// Đợi một API call cụ thể
await page.waitForResponse(response =>
  response.url().includes('/api/users') && response.status() === 200
);

// Trong code của bạn:
await this.page.waitForResponse(async (response) => {
  const data = response.request().postDataJSON();
  return data?.action === "CREATE" &&
         data?.resource === "wealth.customer.phan_tich_nhu_cau_bao_hiem";
});
```

**Giải thích code của bạn:**
- Đợi một request POST
- Request đó phải có `action: "CREATE"`
- Và `resource: "wealth.customer.phan_tich_nhu_cau_bao_hiem"`
- Đảm bảo data đã được tạo trên server

### 4. **waitForLoadState()** - Đợi page load
```typescript
// Đợi page load xong
await page.waitForLoadState('load');

// Đợi network idle (không còn request nào)
await page.waitForLoadState('networkidle');

// Đợi DOM sẵn sàng
await page.waitForLoadState('domcontentloaded');
```

### 5. **waitForTimeout()** - Đợi cứng (KHÔNG KHUYẾN NGHỊ)
```typescript
// Đợi 2 giây (tránh dùng cách này)
await page.waitForTimeout(2000);

// ❌ Tại sao không nên dùng:
// - Làm test chậm
// - Không ổn định (có khi cần 1s, có khi cần 3s)

// ✅ Nên dùng waitFor thay vào:
await page.locator('.result').waitFor({ state: 'visible' });
```

---

## Best Practices

### ✅ DO - Nên làm

1. **Dùng data-testid**
```typescript
// HTML
<button data-testid="submit-button">Submit</button>

// Test
await page.getByTestId('submit-button').click();
```

2. **Dùng Page Object Model (POM)** - Như code của bạn
```typescript
// ✅ Tốt: Tách biệt logic vào class
class LoginPage {
  constructor(private page: Page) {}

  async login(username: string, password: string) {
    await this.page.getByTestId('username').fill(username);
    await this.page.getByTestId('password').fill(password);
    await this.page.getByTestId('submit').click();
  }
}

// Test code gọn gàng
await loginPage.login('user', 'pass');
```

3. **Dùng auto-waiting, tránh sleep**
```typescript
// ❌ Không tốt
await page.click('button');
await page.waitForTimeout(2000); // Đợi cứng 2s

// ✅ Tốt
await page.click('button');
await page.locator('.success-message').waitFor({ state: 'visible' });
```

4. **Kiểm tra API response** - Như code của bạn
```typescript
// ✅ Tốt: Đảm bảo data đã lưu trước khi tiếp tục
await page.click('button');
await page.waitForResponse(response =>
  response.url().includes('/api/save')
);
```

### ❌ DON'T - Không nên làm

1. **Không nên dùng sleep/timeout cứng**
```typescript
// ❌ Tránh
await page.waitForTimeout(5000);

// ✅ Dùng
await page.locator('.loading').waitFor({ state: 'hidden' });
```

2. **Không nên dùng CSS selector phức tạp**
```typescript
// ❌ Khó maintain
await page.locator('div.container > ul > li:nth-child(3) > button').click();

// ✅ Dễ maintain
await page.getByTestId('delete-button').click();
```

3. **Không nên hardcode index**
```typescript
// ❌ Dễ break
await page.locator('button').nth(5).click();

// ✅ Tốt hơn
await page.locator('button').filter({ hasText: 'Delete' }).click();
```

---

## Ví dụ thực tế

### Phân tích đoạn code của bạn:

```typescript
// FILE: phan-tich-nhu-cau-bao-hiem-page.ts

// 1. Class chính - Entry point
export class PhanTichNhuCauBaoHiemPage {
  constructor(private page: Page) {}

  get tabs(): Tabs {
    return new Tabs(this.page);
  }
}

// 2. Class Tabs - Quản lý tabs
class Tabs {
  constructor(private page: Page) {}

  // Locator - Tìm tất cả tab items
  get tabItem(): Locator {
    return this.page.getByTestId("tab-item");
  }

  // Method đếm số tabs
  async thayTongSoKichBan() {
    return await this.tabItem.count(); // Đếm số elements
  }

  // Method thêm kịch bản mới
  async themMoiKichBan() {
    // Bước 1: Click button thêm
    await this.buttonThemKichBan.click();

    // Bước 2: Đợi API CREATE hoàn thành
    await this.page.waitForResponse(async (response) => {
      const data = response.request().postDataJSON();
      return data?.action === "CREATE" &&
             data?.resource === "wealth.customer.phan_tich_nhu_cau_bao_hiem";
    });

    // Bước 3: Đợi API READ hoàn thành (load data mới)
    await this.page.waitForResponse(async (response) => {
      const data = response.request().postDataJSON();
      return data?.action === "READ" &&
             data?.resource === "wealth.customer.phan_tich_nhu_cau_bao_hiem";
    });

    // Bước 4: Đợi loading spinner biến mất
    try {
      await this.loading.waitFor({ state: "hidden", timeout: 5000 });
    } catch (error) {}
  }
}

// 3. Class TabItem - Thao tác trên 1 tab
class TabItem {
  constructor(private item: Locator, private page: Page) {}

  // Method nhập tên kịch bản
  async nhapTenKichBan(ten: string) {
    await this.tenKichBan.dblclick();  // Double click để edit
    await this.tenKichBan.fill(ten);   // Fill text
    await this.tenKichBan.press("Enter"); // Nhấn Enter để lưu
  }

  // Method xóa kịch bản
  async xoaKichBan() {
    await this.menuOption.click();          // Mở menu
    await this.menuOptionXoa.click();       // Click "Xóa"
    await this.buttonXacNhanXoaKichBan.click(); // Confirm

    // Đợi API DELETE
    await this.page.waitForResponse(async (response) => {
      return response.request().postDataJSON()?.action === "DELETE";
    });
  }
}

// 4. BaseRow - Class cơ sở cho các row trong table
class BaseRow {
  constructor(protected row: Locator) {}

  // Private helper method để nhập giá trị
  private async nhapGiaTri(input: Locator, value: number) {
    await input.dblclick();                    // Double click cell
    await input.locator("input").fill(value.toString()); // Fill input
    await input.press("Enter");                 // Nhấn Enter
  }

  // Method nhập bảo hiểm sinh mệnh
  async nhapBaoHiemSinhMenh(value: number) {
    await this.nhapGiaTri(this.baoHiemSinhMenh, value);
  }
}
```

### Giải thích flow của `themMoiKichBan()`:

```typescript
async themMoiKichBan() {
  // 1. Click button "Thêm kịch bản"
  await this.buttonThemKichBan.click();

  // 2. Đợi server tạo kịch bản (API CREATE)
  //    Đảm bảo data đã được tạo trên backend
  await this.page.waitForResponse(async (response) => {
    const data = response.request().postDataJSON();
    return data?.action === "CREATE";
  });

  // 3. Đợi server trả về data mới (API READ)
  //    Frontend load data mới về
  await this.page.waitForResponse(async (response) => {
    const data = response.request().postDataJSON();
    return data?.action === "READ";
  });

  // 4. Đợi loading spinner biến mất
  //    UI đã render xong
  try {
    await this.loading.waitFor({ state: "hidden", timeout: 5000 });
  } catch (error) {
    // Bỏ qua nếu không có loading spinner
  }
}
```

**Tại sao phải đợi nhiều bước?**
1. Click → UI gửi request
2. Đợi CREATE → Server đã lưu data
3. Đợi READ → Frontend đã load data mới
4. Đợi loading biến mất → UI đã render xong

Nếu không đợi đủ → test sẽ fail vì thao tác tiếp theo chạy khi data chưa sẵn sàng!

---

## 🎯 Test Case ví dụ

```typescript
import { test, expect } from '@playwright/test';
import { PhanTichNhuCauBaoHiemPage } from './pages/phan-tich-nhu-cau-bao-hiem-page';

test('Tạo kịch bản mới và nhập dữ liệu', async ({ page }) => {
  // 1. Khởi tạo page object
  const phanTichPage = new PhanTichNhuCauBaoHiemPage(page);

  // 2. Đi đến trang
  await page.goto('/phan-tich-nhu-cau-bao-hiem');

  // 3. Kiểm tra số kịch bản ban đầu
  const soKichBanBanDau = await phanTichPage.tabs.thayTongSoKichBan();

  // 4. Thêm kịch bản mới
  await phanTichPage.tabs.themMoiKichBan();

  // 5. Kiểm tra số kịch bản đã tăng
  const soKichBanMoi = await phanTichPage.tabs.thayTongSoKichBan();
  expect(soKichBanMoi).toBe(soKichBanBanDau + 1);

  // 6. Lấy tab mới nhất
  const tabMoi = await phanTichPage.tabs.getTabItem({ index: soKichBanMoi });

  // 7. Đổi tên kịch bản
  await tabMoi.nhapTenKichBan('Kịch bản Test');

  // 8. Chọn khách hàng
  await phanTichPage.chonKhachHangSection.chonKhachHang('kh_1');

  // 9. Nhập số tiền bảo hiểm
  await phanTichPage.bangKichBan.soTienCanThietDuyTriMucSong
    .nhapSoTienBaoHiemSinhMenhDuyTriMucSong(5000000);

  // 10. Kiểm tra giá trị đã lưu
  const giaTriBaoHiem = await phanTichPage.bangKichBan
    .soTienCanThietDuyTriMucSong
    .soTienCanThietDuyTriMucSong
    .baoHiemSinhMenh
    .textContent();

  expect(giaTriBaoHiem).toContain('5,000,000');
});
```

---

## Test Hooks và Fixtures

> **📖 Docs:** [Test Hooks](https://playwright.dev/docs/api/class-test) | [Fixtures](https://playwright.dev/docs/test-fixtures)

### 🪝 Test Hooks - Chạy code trước/sau test

```typescript
import { test, expect } from '@playwright/test';

// beforeEach: Chạy TRƯỚC MỖI test
test.beforeEach(async ({ page }) => {
  console.log('Chạy trước mỗi test');
  await page.goto('/');
  await page.getByRole('button', { name: 'Login' }).click();
});

// afterEach: Chạy SAU MỖI test
test.afterEach(async ({ page }) => {
  console.log('Chạy sau mỗi test');
  await page.screenshot({ path: 'screenshot.png' });
});

// beforeAll: Chạy 1 LẦN TRƯỚC TẤT CẢ tests
test.beforeAll(async () => {
  console.log('Setup database...');
});

// afterAll: Chạy 1 LẦN SAU TẤT CẢ tests
test.afterAll(async () => {
  console.log('Cleanup database...');
});

test('test 1', async ({ page }) => {
  // Test code
});

test('test 2', async ({ page }) => {
  // Test code
});
```

**Order thực thi:**
```
beforeAll
  beforeEach
    test 1
  afterEach
  beforeEach
    test 2
  afterEach
afterAll
```

---

### 🎁 Fixtures - Tài nguyên được inject vào test

Playwright cung cấp sẵn fixtures:

```typescript
test('example', async ({
  page,        // Page object
  context,     // Browser context
  browser,     // Browser instance
  browserName, // 'chromium' | 'firefox' | 'webkit'
  request,     // API request context
}) => {
  // Sử dụng fixtures
  await page.goto('/');
  console.log(browserName); // chromium
});
```

**Custom Fixtures:**

```typescript
// fixtures.ts
import { test as base } from '@playwright/test';

type MyFixtures = {
  loggedInPage: Page;
};

export const test = base.extend<MyFixtures>({
  loggedInPage: async ({ page }, use) => {
    // Setup: Login trước
    await page.goto('/login');
    await page.getByLabel('Email').fill('user@example.com');
    await page.getByLabel('Password').fill('password');
    await page.getByRole('button', { name: 'Login' }).click();

    // Dùng page đã login
    await use(page);

    // Teardown: Logout sau (nếu cần)
    // await page.getByRole('button', { name: 'Logout' }).click();
  },
});

// test.spec.ts
import { test } from './fixtures';

test('test with logged in user', async ({ loggedInPage }) => {
  // loggedInPage đã login sẵn
  await loggedInPage.goto('/dashboard');
});
```

---

### 📦 Test Groups

```typescript
// Group tests với describe
test.describe('Login tests', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/login');
  });

  test('login với email', async ({ page }) => {
    // Test code
  });

  test('login với Google', async ({ page }) => {
    // Test code
  });
});

test.describe('Dashboard tests', () => {
  test('xem dashboard', async ({ page }) => {
    // Test code
  });
});
```

---

### 🏷️ Test Tags và Skip

```typescript
// Tag test
test('login @smoke', async ({ page }) => {
  // Test quan trọng, chạy mỗi commit
});

test('complex flow @slow', async ({ page }) => {
  // Test chậm, chỉ chạy trước release
});

// Chạy test theo tag:
// npx playwright test --grep @smoke

// Skip test
test.skip('test này bị skip', async ({ page }) => {
  // Không chạy
});

// Skip conditionally
test('test này', async ({ page, browserName }) => {
  test.skip(browserName === 'firefox', 'Chưa support Firefox');
  // Test code
});

// Only: Chỉ chạy test này (debug)
test.only('chỉ chạy test này', async ({ page }) => {
  // Chỉ test này chạy
});
```

---

## Page Object Model (POM)

> **📖 Docs:** [Page Object Model](https://playwright.dev/docs/pom)

### 🎯 POM là gì?

**Page Object Model (POM)** là design pattern tách biệt **test logic** và **page structure**.

**Lợi ích:**
- ✅ Code dễ maintain (UI thay đổi → chỉ sửa 1 chỗ)
- ✅ Code dễ đọc (test code clean, không lộn xộn selectors)
- ✅ Tái sử dụng (dùng lại methods ở nhiều tests)
- ✅ Dễ collaborate (team dễ hiểu code)

---

### 📐 Cấu trúc POM

```
e2e/
├── pages/                    # Page Objects
│   ├── login-page.ts
│   ├── dashboard-page.ts
│   └── phan-tich-nhu-cau-bao-hiem-page.ts
├── tests/                    # Test files
│   ├── login.spec.ts
│   └── dashboard.spec.ts
└── models/                   # Shared models (optional)
    └── dropdown.ts
```

---

### 💡 Ví dụ POM đơn giản

```typescript
// pages/login-page.ts
import { type Page, type Locator } from '@playwright/test';

export class LoginPage {
  // Page reference
  private readonly page: Page;

  // Locators (getters để lazy evaluation)
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly loginButton: Locator;
  readonly errorMessage: Locator;

  constructor(page: Page) {
    this.page = page;

    // Define locators
    this.emailInput = page.getByLabel('Email');
    this.passwordInput = page.getByLabel('Password');
    this.loginButton = page.getByRole('button', { name: 'Login' });
    this.errorMessage = page.getByRole('alert');
  }

  // Actions
  async goto() {
    await this.page.goto('/login');
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.loginButton.click();
  }

  async loginWithGoogle() {
    await this.page.getByRole('button', { name: 'Login with Google' }).click();
  }

  // Assertions (optional - hoặc để trong test)
  async expectErrorMessage(message: string) {
    await expect(this.errorMessage).toHaveText(message);
  }
}

// tests/login.spec.ts
import { test, expect } from '@playwright/test';
import { LoginPage } from '../pages/login-page';

test('login thành công', async ({ page }) => {
  const loginPage = new LoginPage(page);

  await loginPage.goto();
  await loginPage.login('user@example.com', 'password123');

  await expect(page).toHaveURL('/dashboard');
});

test('login thất bại - sai password', async ({ page }) => {
  const loginPage = new LoginPage(page);

  await loginPage.goto();
  await loginPage.login('user@example.com', 'wrongpassword');

  await loginPage.expectErrorMessage('Invalid credentials');
});
```

---

### 🏗️ POM nâng cao - Nested components

```typescript
// pages/phan-tich-nhu-cau-bao-hiem-page.ts (từ code của bạn)
export class PhanTichNhuCauBaoHiemPage {
  private readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  // Nested component: Tabs
  get tabs(): Tabs {
    return new Tabs(this.page);
  }

  // Nested component: Bang Kich Ban
  get bangKichBan(): BangKichBan {
    return new BangKichBan(this.page);
  }

  // Nested component: Chon Khach Hang
  get chonKhachHangSection(): ChonKhachHangSection {
    return new ChonKhachHangSection(
      this.page.getByTestId("user-select-container")
    );
  }
}

// Component: Tabs
class Tabs {
  private readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  get tabItem(): Locator {
    return this.page.getByTestId("tab-item");
  }

  async thayTongSoKichBan(): Promise<number> {
    return await this.tabItem.count();
  }

  async getTabItem(filter: { index?: number; ten?: string }): Promise<TabItem> {
    if (filter.index) {
      return new TabItem(this.tabItem.nth(filter.index - 1), this.page);
    }
    if (filter.ten) {
      return new TabItem(this.tabItem.filter({ hasText: filter.ten }), this.page);
    }
    throw new Error("filter is required");
  }
}

// Sử dụng trong test:
test('example', async ({ page }) => {
  const phanTichPage = new PhanTichNhuCauBaoHiemPage(page);

  // Gọi nested components
  const soKichBan = await phanTichPage.tabs.thayTongSoKichBan();
  const tab = await phanTichPage.tabs.getTabItem({ index: 1 });
});
```

---

### 🎨 POM Best Practices

```typescript
// ✅ TỐT: Dùng getters cho locators (lazy evaluation)
class MyPage {
  get submitButton() {
    return this.page.getByRole('button', { name: 'Submit' });
  }
}

// ❌ TRÁNH: Define locators trong constructor
class MyPage {
  submitButton: Locator;

  constructor(page: Page) {
    this.submitButton = page.getByRole('button', { name: 'Submit' }); // ← Sai
  }
}

// ✅ TỐT: Methods trả về high-level actions
async login(email: string, password: string) {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.loginButton.click();
}

// ❌ TRÁNH: Methods quá low-level
async fillEmail(email: string) {
  await this.emailInput.fill(email);
}
async fillPassword(password: string) {
  await this.passwordInput.fill(password);
}
async clickLogin() {
  await this.loginButton.click();
}

// ✅ TỐT: Assertions trong test, không trong page object
// (hoặc tạo method riêng cho assertions nếu cần)

// ✅ TỐT: Tái sử dụng components
class Header {
  constructor(private page: Page) {}

  async logout() {
    await this.page.getByRole('button', { name: 'Logout' }).click();
  }
}

class DashboardPage {
  readonly header: Header;

  constructor(page: Page) {
    this.header = new Header(page);
  }
}
```

---

## Debugging và Tools

> **📖 Docs:** [Debugging](https://playwright.dev/docs/debug) | [Codegen](https://playwright.dev/docs/codegen) | [Trace Viewer](https://playwright.dev/docs/trace-viewer)

### 🐛 Debug Mode

```bash
# Chạy test ở debug mode
npx playwright test --debug

# Debug test cụ thể
npx playwright test login.spec.ts --debug

# Debug từ dòng code (thêm vào test)
await page.pause(); # ← Dừng tại đây, mở Playwright Inspector
```

**Playwright Inspector:**
- ✅ Step through test từng bước
- ✅ Xem locators, highlight elements
- ✅ Execute commands trong console
- ✅ Xem screenshots, logs

---

### 🎬 Codegen - Tự động generate code

```bash
# Generate code bằng cách record actions
npx playwright codegen https://example.com

# Generate code cho browser cụ thể
npx playwright codegen --browser=firefox https://example.com

# Generate code với device emulation
npx playwright codegen --device="iPhone 13" https://example.com
```

**Cách dùng:**
1. Chạy lệnh → Browser mở
2. Thao tác trên browser → Code tự động sinh ra
3. Copy code vào test

---

### 📹 Tracing - Record chi tiết test execution

```typescript
// Bật tracing trong playwright.config.ts
export default defineConfig({
  use: {
    trace: 'on-first-retry', // 'on' | 'off' | 'retain-on-failure' | 'on-first-retry'
  },
});

// Hoặc trong test
test('example', async ({ page, context }) => {
  await context.tracing.start({ screenshots: true, snapshots: true });

  // Test code
  await page.goto('/');
  await page.click('button');

  await context.tracing.stop({ path: 'trace.zip' });
});
```

```bash
# Xem trace
npx playwright show-trace trace.zip
```

**Trace Viewer hiển thị:**
- ✅ Timeline của tất cả actions
- ✅ Screenshots tại mỗi bước
- ✅ DOM snapshots
- ✅ Network requests
- ✅ Console logs
- ✅ Source code

---

### 📸 Screenshots & Videos

```typescript
// Screenshot trong test
await page.screenshot({ path: 'screenshot.png' });

// Screenshot full page
await page.screenshot({ path: 'screenshot.png', fullPage: true });

// Screenshot element
await page.getByRole('dialog').screenshot({ path: 'dialog.png' });

// Cấu hình global trong playwright.config.ts
export default defineConfig({
  use: {
    screenshot: 'only-on-failure', // 'off' | 'on' | 'only-on-failure'
    video: 'retain-on-failure',    // 'off' | 'on' | 'retain-on-failure' | 'on-first-retry'
  },
});
```

---

### 🌐 UI Mode - Interactive test runner

```bash
# Chạy tests trong UI mode
npx playwright test --ui
```

**Features:**
- ✅ Run/debug tests với UI
- ✅ Xem test results real-time
- ✅ Time travel debugging
- ✅ Watch mode (tự động re-run khi code thay đổi)

---

### 🔍 Selector Inspector

```bash
# Mở browser để test selectors
npx playwright open https://example.com
```

Trong browser console:
```javascript
// Test locator
playwright.locator('button').highlight()
playwright.locator('button').click()
```

---

## Troubleshooting - Xử lý lỗi thường gặp

### ❌ Lỗi: "Timeout exceeded" / "Element not found"

**Nguyên nhân:**
- Element chưa xuất hiện hoặc bị ẩn
- Selector sai
- Page load chậm

**Giải pháp:**

```typescript
// 1. Tăng timeout
await page.getByRole('button').click({ timeout: 60000 }); // 60s

// 2. Chờ element xuất hiện trước
await page.getByRole('button').waitFor({ state: 'visible' });
await page.getByRole('button').click();

// 3. Chờ page load xong
await page.waitForLoadState('networkidle');

// 4. Kiểm tra selector
// Dùng Playwright Inspector để test selector
await page.pause();
```

---

### ❌ Lỗi: "Strict mode violation: multiple elements found"

**Nguyên nhân:** Locator match nhiều elements

**Giải pháp:**

```typescript
// ❌ Sai: Match nhiều buttons
await page.getByRole('button').click();

// ✅ Đúng: Chỉ rõ button nào
await page.getByRole('button', { name: 'Submit' }).click();

// ✅ Đúng: Lấy button đầu tiên
await page.getByRole('button').first().click();

// ✅ Đúng: Filter
await page.getByRole('button').filter({ hasText: 'Submit' }).click();
```

---

### ❌ Lỗi: "Element is not visible"

**Nguyên nhân:**
- Element bị `display: none` hoặc `visibility: hidden`
- Element bị overlay che

**Giải pháp:**

```typescript
// 1. Chờ element visible
await page.getByRole('button').waitFor({ state: 'visible' });

// 2. Scroll element vào view
await page.getByRole('button').scrollIntoViewIfNeeded();

// 3. Force click (nếu chắc chắn)
await page.getByRole('button').click({ force: true });

// 4. Đóng overlay trước
await page.getByRole('button', { name: 'Close' }).click();
await page.getByRole('button', { name: 'Submit' }).click();
```

---

### ❌ Lỗi: "Element is outside of the viewport"

**Giải pháp:**

```typescript
// Scroll element vào view
await page.getByRole('button').scrollIntoViewIfNeeded();
await page.getByRole('button').click();
```

---

### ❌ Tests chạy chậm

**Giải pháp:**

```typescript
// 1. Chạy parallel (mặc định Playwright đã parallel)
// playwright.config.ts
export default defineConfig({
  workers: 4, // Số workers
});

// 2. Dùng fill() thay vì type()
await page.getByLabel('Email').fill('test@example.com'); // ✅ Nhanh
await page.getByLabel('Email').type('test@example.com'); // ❌ Chậm

// 3. Bỏ waitForTimeout() nếu có
// await page.waitForTimeout(2000); // ❌ Xóa đi

// 4. Dùng API để setup data thay vì UI
await request.post('/api/users', { data: userData }); // ✅ Nhanh
// Thay vì click qua nhiều trang để tạo user
```

---

### ❌ Tests flaky (đôi khi pass, đôi khi fail)

**Nguyên nhân phổ biến:**
- Race conditions (data chưa load xong)
- Animations chưa hoàn thành
- Network requests chưa xong

**Giải pháp:**

```typescript
// 1. Đợi API response
await page.getByRole('button', { name: 'Save' }).click();
await page.waitForResponse(response =>
  response.url().includes('/api/save') && response.status() === 200
);

// 2. Đợi element stable
await page.getByRole('button').click(); // Auto-wait cho stable

// 3. Tắt animations trong test
// playwright.config.ts
export default defineConfig({
  use: {
    // Tắt CSS animations & transitions
    viewport: { width: 1280, height: 720 },
    reducedMotion: 'reduce',
  },
});

// 4. Retry flaky tests
// playwright.config.ts
export default defineConfig({
  retries: 2, // Retry 2 lần nếu fail
});
```

---

### 🛠️ Debug Tips

```typescript
// 1. Thêm console.log
test('debug example', async ({ page }) => {
  console.log('Current URL:', page.url());

  const text = await page.getByRole('heading').textContent();
  console.log('Heading text:', text);
});

// 2. Pause test để investigate
await page.pause();

// 3. Screenshot khi có lỗi
test.afterEach(async ({ page }, testInfo) => {
  if (testInfo.status !== 'passed') {
    await page.screenshot({ path: `failure-${testInfo.title}.png` });
  }
});

// 4. Chạy headed mode để thấy browser
npx playwright test --headed

// 5. Slow motion
npx playwright test --headed --slow-mo=1000 // Mỗi action delay 1s
```

---

## 📝 Tóm tắt

| Khái niệm | Giải thích | Ví dụ |
|-----------|-----------|-------|
| **Page** | Trang web (tab) | `await page.goto('...')` |
| **Locator** | Cách tìm element | `page.locator('button')` |
| **getByTestId** | Tìm theo data-testid | `page.getByTestId('submit')` |
| **click()** | Click chuột | `await button.click()` |
| **fill()** | Nhập text | `await input.fill('text')` |
| **waitFor()** | Đợi element | `await loading.waitFor({ state: 'hidden' })` |
| **waitForResponse()** | Đợi API | `await page.waitForResponse(...)` |
| **expect** | Kiểm tra | `await expect(el).toBeVisible()` |

---

## 🚀 Bước tiếp theo

### 📚 Tài liệu chính thức

#### **Playwright Official Docs** (Khuyến nghị đọc)
- [Getting Started](https://playwright.dev/docs/intro) - Bắt đầu với Playwright
- [Locators](https://playwright.dev/docs/locators) - Cách tìm elements
- [Assertions](https://playwright.dev/docs/test-assertions) - Kiểm tra kết quả
- [Auto-waiting](https://playwright.dev/docs/actionability) - Actionability checks
- [Best Practices](https://playwright.dev/docs/best-practices) - Best practices
- [Debugging](https://playwright.dev/docs/debug) - Debug tests
- [Trace Viewer](https://playwright.dev/docs/trace-viewer) - Xem trace
- [VS Code Extension](https://playwright.dev/docs/getting-started-vscode) - Dùng với VS Code

#### **Tutorials & Guides (2026)**
- [Playwright Tutorial - BrowserStack](https://www.browserstack.com/guide/playwright-tutorial)
- [Playwright TypeScript Guide](https://www.browserstack.com/guide/playwright-typescript)
- [Playwright Selectors Best Practices](https://www.browserstack.com/guide/playwright-selectors-best-practices)
- [Playwright Best Practices](https://www.browserstack.com/guide/playwright-best-practices)
- [Playwright Wait Types](https://www.browserstack.com/guide/playwright-wait-types)
- [Playwright Assertions Guide](https://www.browserstack.com/guide/playwright-assertions)

### 🎯 Thực hành trong dự án

1. **Chạy tests hiện có:**
   ```bash
   npm run test:e2e
   ```

2. **Chạy test ở UI mode:**
   ```bash
   npx playwright test --ui
   ```

3. **Generate code với Codegen:**
   ```bash
   npx playwright codegen http://localhost:3000
   ```

4. **Debug test:**
   ```bash
   npx playwright test --debug
   ```

5. **Xem test report:**
   ```bash
   npx playwright show-report
   ```

### 📖 Học thêm

- Tham khảo test cases trong folder `e2e/tests/`
- Tham khảo page objects trong folder `e2e/pages/`
- Đọc code của file [phan-tich-nhu-cau-bao-hiem-page.ts](e2e/pages/phan-tich-nhu-cau-bao-hiem-page.ts)
- Thử viết test mới cho tính năng mới

### 💡 Tips

- Dùng **Playwright Inspector** (`await page.pause()`) để test selectors
- Dùng **Trace Viewer** để debug failed tests
- Dùng **Codegen** để generate code nhanh
- Đọc error messages kỹ - Playwright có error messages rất chi tiết
- Join [Playwright Discord](https://discord.com/invite/playwright-807756831384403968) để hỏi đáp

---

## 🌟 Tổng kết

Bạn đã học:
- ✅ Playwright là gì và cách cài đặt
- ✅ Các khái niệm cơ bản: Page, Locator, Browser Context
- ✅ Cách tìm elements với priority đúng (getByRole > getByLabel > ...)
- ✅ Cách tương tác: click, fill, press, check, ...
- ✅ Assertions và auto-retrying
- ✅ Auto-waiting và Actionability checks
- ✅ Waiting strategies: waitFor, waitForResponse, ...
- ✅ Test hooks và fixtures
- ✅ Page Object Model (POM)
- ✅ Debugging tools: Inspector, Codegen, Trace Viewer
- ✅ Troubleshooting các lỗi thường gặp

**Playwright là framework mạnh mẽ, ổn định, và dễ sử dụng. Chúc bạn viết tests hiệu quả!** 🚀

---

Happy Testing! 🎉

---

**📌 Nguồn tham khảo:**
- [Playwright Official Documentation](https://playwright.dev/docs/intro)
- [BrowserStack Playwright Guides](https://www.browserstack.com/guide/playwright-tutorial)
- [GitHub - microsoft/playwright](https://github.com/microsoft/playwright)
