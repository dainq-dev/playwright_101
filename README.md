# 🎭 Playwright 101 - Hướng Dẫn Cơ Bản

## 📚 Mục lục
1. [Playwright là gì?](#playwright-là-gì)
2. [Các khái niệm cơ bản](#các-khái-niệm-cơ-bản)
3. [Cách tìm elements](#cách-tìm-elements)
4. [Tương tác với UI](#tương-tác-với-ui)
5. [Assertions (Kiểm tra)](#assertions)
6. [Waiting Strategies](#waiting-strategies)
7. [Best Practices](#best-practices)
8. [Ví dụ thực tế](#ví-dụ-thực-tế)

---

## Playwright là gì?

**Playwright** là framework tự động hóa test cho web applications, được phát triển bởi Microsoft.

### Ưu điểm:
- ✅ Hỗ trợ nhiều browser: Chrome, Firefox, Safari
- ✅ Auto-waiting thông minh
- ✅ Chạy nhanh và ổn định
- ✅ Có API rất mạnh để tương tác với web
- ✅ Hỗ trợ TypeScript tốt

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

## Cách tìm elements

### 1. **getByRole** - Tìm theo vai trò (KHUYẾN NGHỊ)
```typescript
// Tìm button
await page.getByRole('button', { name: 'Submit' }).click();

// Tìm textbox
await page.getByRole('textbox', { name: 'Email' }).fill('test@example.com');

// Tìm link
await page.getByRole('link', { name: 'Home' }).click();
```

**Ưu điểm**: Accessibility-friendly, ổn định nhất

### 2. **getByTestId** - Tìm theo data-testid (PHỔ BIẾN trong dự án)
```typescript
// HTML: <button data-testid="submit-button">Submit</button>
await page.getByTestId('submit-button').click();

// Trong code của bạn:
await this.page.getByTestId('tab-item').click();
```

**Khi nào dùng**: Khi dev team thêm `data-testid` vào code

### 3. **locator** - Tìm theo CSS selector
```typescript
// Tìm theo class
await page.locator('.submit-button').click();

// Tìm theo id
await page.locator('#user-name').fill('John');

// Tìm theo tag + class
await page.locator('button.primary').click();

// Tìm theo attribute
await page.locator('[data-testid="submit"]').click();
```

### 4. **getByText** - Tìm theo text
```typescript
// Tìm element chứa text chính xác
await page.getByText('Submit').click();

// Tìm element chứa text (partial match)
await page.getByText('Sub', { exact: false }).click(); // Tìm "Submit", "Subscribe"
```

### 5. **filter** - Lọc locator
```typescript
// Tìm tất cả buttons, lọc button có text "Delete"
await page.locator('button').filter({ hasText: 'Delete' }).click();

// Trong code của bạn:
const tab = this.tabItem.filter({ hasText: 'Kịch bản 1' });
```

### 6. **nth** - Lấy element thứ N
```typescript
// Lấy button đầu tiên (index 0)
await page.locator('button').nth(0).click();

// Lấy button thứ 3 (index 2)
await page.locator('button').nth(2).click();

// Trong code của bạn:
return this.tabItem.nth(filter.index - 1); // index 1-based -> 0-based
```

---

## Tương tác với UI

### 1. **click()** - Click chuột
```typescript
// Click thường
await page.locator('button').click();

// Double click
await page.locator('input').dblclick();

// Right click
await page.locator('div').click({ button: 'right' });
```

### 2. **fill()** - Nhập text vào input
```typescript
// Clear và nhập text mới
await page.locator('input[name="email"]').fill('test@example.com');

// Trong code của bạn:
await this.inputTenGiaTri.fill(ten);
```

### 3. **type()** - Gõ từng ký tự (như người thật)
```typescript
// Gõ chậm từng ký tự
await page.locator('input').type('Hello', { delay: 100 });
```

### 4. **press()** - Nhấn phím
```typescript
// Nhấn Enter
await page.locator('input').press('Enter');

// Nhấn Tab
await page.locator('input').press('Tab');

// Trong code của bạn:
await this.tenKichBan.press('Enter'); // Sau khi fill xong
```

### 5. **check() / uncheck()** - Checkbox
```typescript
// Check checkbox
await page.locator('input[type="checkbox"]').check();

// Uncheck checkbox
await page.locator('input[type="checkbox"]').uncheck();

// Trong code của bạn:
await this.options.bao_hiem_sinh_menh.check();
```

### 6. **selectOption()** - Select dropdown
```typescript
// Chọn option theo value
await page.locator('select').selectOption('option-value');

// Chọn option theo label
await page.locator('select').selectOption({ label: 'Option Label' });
```

### 7. **count()** - Đếm số lượng elements
```typescript
// Đếm số button
const count = await page.locator('button').count();

// Trong code của bạn:
async thayTongSoKichBan() {
  return await this.tabItem.count();
}
```

---

## Assertions

### 1. **expect** - Kiểm tra điều kiện
```typescript
import { expect } from '@playwright/test';

// Kiểm tra element có hiển thị
await expect(page.locator('button')).toBeVisible();

// Kiểm tra element bị ẩn
await expect(page.locator('.loading')).toBeHidden();

// Kiểm tra text
await expect(page.locator('h1')).toHaveText('Welcome');

// Kiểm tra value của input
await expect(page.locator('input')).toHaveValue('test@example.com');

// Kiểm tra số lượng
await expect(page.locator('li')).toHaveCount(5);

// Kiểm tra URL
await expect(page).toHaveURL('https://example.com/home');
```

### 2. **Soft Assertions** - Tiếp tục test dù fail
```typescript
// Assertion bình thường: fail thì dừng ngay
await expect(page.locator('h1')).toHaveText('Welcome'); // Fail -> Dừng

// Soft assertion: fail vẫn chạy tiếp
await expect.soft(page.locator('h1')).toHaveText('Welcome'); // Fail -> Vẫn chạy tiếp
await expect.soft(page.locator('h2')).toHaveText('Subtitle'); // Vẫn chạy
```

---

## Waiting Strategies

Playwright **tự động đợi** element sẵn sàng trước khi tương tác. Nhưng đôi khi bạn cần đợi thêm.

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

1. Đọc [Playwright Docs](https://playwright.dev/docs/intro)
2. Chạy thử test trong project: `npm run test:e2e`
3. Dùng [Playwright Inspector](https://playwright.dev/docs/inspector) để debug
4. Tham khảo thêm test cases trong folder `e2e/`

Happy Testing! 🎉
