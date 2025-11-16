const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();

  try {
    await page.goto('https://www.saucedemo.com/');

    await page.fill('#user-name', 'standard_user');
    await page.fill('#password', 'secret_sauce');
    await page.click('#login-button');
    await page.waitForSelector('.inventory_item');

    const firstProduct = page.locator('.inventory_item').first();
    const storedProductNameRaw = await firstProduct.locator('.inventory_item_name').textContent();
    const storedProductName = storedProductNameRaw ? storedProductNameRaw.trim() : '';

    await page.click('button[id="add-to-cart-sauce-labs-backpack"]');
    await page.click('.shopping_cart_link');

    await page.waitForSelector('.cart_item .inventory_item_name');
    const cartProductNameRaw = await page.textContent('.cart_item .inventory_item_name');
    const cartProductName = cartProductNameRaw ? cartProductNameRaw.trim() : '';

    if (storedProductName !== cartProductName) {
      process.exitCode = 1;
    }

    await page.click('#react-burger-menu-btn');
    await page.click('#logout_sidebar_link');
  } catch (err) {
    process.exitCode = 2;
  } finally {
    await browser.close();
  }
})();
