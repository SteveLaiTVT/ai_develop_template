# Agent-Browser Skills

浏览器自动化技能 - Web 开发中至关重要的测试和交互能力。

This skill defines browser automation capabilities essential for web development testing and interaction.

---

## Overview

Agent-Browser skills enable AI agents to interact with web applications through automated browser control. This is critical for:

- **End-to-end testing** of web applications
- **Visual verification** of UI changes
- **User flow validation** before deployment
- **Screenshot capture** for documentation
- **Form filling and interaction testing**

---

## Core Capabilities

### 1. Navigation

```typescript
// Navigate to a URL
await browser.navigate('https://example.com/login');

// Wait for page to load
await browser.waitForNavigation();

// Go back/forward
await browser.goBack();
await browser.goForward();

// Refresh page
await browser.refresh();
```

### 2. Element Interaction

```typescript
// Click elements
await browser.click('#submit-button');
await browser.click('button:has-text("Sign In")');

// Type text
await browser.type('#username', 'testuser@example.com');
await browser.type('#password', 'SecurePass123');

// Fill forms
await browser.fill({
  '#email': 'user@example.com',
  '#password': 'password123',
  '#name': 'John Doe',
});

// Select dropdown options
await browser.select('#country', 'United States');

// Check/uncheck checkboxes
await browser.check('#terms-checkbox');
await browser.uncheck('#newsletter-checkbox');
```

### 3. Wait Strategies

```typescript
// Wait for element to appear
await browser.waitForSelector('.success-message');

// Wait for element to be visible
await browser.waitForVisible('#modal');

// Wait for element to be hidden
await browser.waitForHidden('.loading-spinner');

// Wait for specific text
await browser.waitForText('Welcome back!');

// Wait for network idle
await browser.waitForNetworkIdle();

// Custom timeout
await browser.waitForSelector('#result', { timeout: 10000 });
```

### 4. Assertions

```typescript
// Check element exists
const exists = await browser.exists('#welcome-message');

// Check element is visible
const visible = await browser.isVisible('#modal');

// Check element text
const text = await browser.getText('#user-name');
expect(text).toBe('John Doe');

// Check element attributes
const href = await browser.getAttribute('#link', 'href');
expect(href).toBe('/dashboard');

// Check page title
const title = await browser.getTitle();
expect(title).toBe('Dashboard - MyApp');

// Check URL
const url = await browser.getUrl();
expect(url).toContain('/dashboard');
```

---

## Screenshot Capabilities

### Capturing Screenshots

```typescript
// Full page screenshot
await browser.screenshot('full-page.png');

// Viewport screenshot
await browser.screenshot('viewport.png', { fullPage: false });

// Element screenshot
await browser.screenshot('element.png', { element: '#hero-section' });

// With custom quality (JPEG)
await browser.screenshot('page.jpg', { 
  type: 'jpeg', 
  quality: 80 
});
```

### Screenshot Best Practices

| Use Case | Recommendation |
|----------|----------------|
| Documentation | Full page, PNG format |
| Bug reports | Viewport + console logs |
| Visual testing | Element-specific screenshots |
| Performance | JPEG with quality 70-80 |

---

## Common Testing Patterns

### Login Flow Testing

```typescript
async function testLoginFlow() {
  // Navigate to login page
  await browser.navigate('https://app.example.com/login');
  
  // Wait for login form
  await browser.waitForSelector('#login-form');
  
  // Fill credentials
  await browser.type('#email', 'test@example.com');
  await browser.type('#password', 'TestPassword123');
  
  // Click login button
  await browser.click('#login-button');
  
  // Wait for redirect
  await browser.waitForNavigation();
  
  // Verify successful login
  const welcomeMessage = await browser.getText('.welcome-message');
  expect(welcomeMessage).toContain('Welcome');
  
  // Take screenshot for verification
  await browser.screenshot('login-success.png');
}
```

### Form Submission Testing

```typescript
async function testFormSubmission() {
  await browser.navigate('https://app.example.com/contact');
  
  // Fill form fields
  await browser.fill({
    '#name': 'John Doe',
    '#email': 'john@example.com',
    '#subject': 'Test Subject',
    '#message': 'This is a test message',
  });
  
  // Submit form
  await browser.click('button[type="submit"]');
  
  // Wait for success message
  await browser.waitForSelector('.success-notification');
  
  // Verify submission
  const notification = await browser.getText('.success-notification');
  expect(notification).toContain('successfully');
}
```

### Navigation Testing

```typescript
async function testNavigation() {
  await browser.navigate('https://app.example.com');
  
  // Test main navigation
  const navLinks = ['Home', 'Products', 'About', 'Contact'];
  
  for (const link of navLinks) {
    await browser.click(`nav a:has-text("${link}")`);
    await browser.waitForNavigation();
    
    // Verify page loaded
    const title = await browser.getTitle();
    expect(title).toContain(link);
    
    // Take screenshot
    await browser.screenshot(`nav-${link.toLowerCase()}.png`);
  }
}
```

---

## Error Handling

### Handling Common Issues

```typescript
// Handle element not found
try {
  await browser.click('#non-existent', { timeout: 5000 });
} catch (error) {
  if (error.message.includes('Element not found')) {
    console.log('Element does not exist on page');
  }
}

// Handle navigation timeout
try {
  await browser.navigate('https://slow-site.com', { timeout: 30000 });
} catch (error) {
  if (error.message.includes('timeout')) {
    console.log('Page load timed out');
  }
}

// Handle stale elements
await browser.waitForSelector('#dynamic-element');
await browser.waitForStable('#dynamic-element');
await browser.click('#dynamic-element');
```

### Retry Strategies

```typescript
async function clickWithRetry(selector: string, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      await browser.click(selector);
      return; // Success
    } catch (error) {
      if (attempt === maxRetries) throw error;
      // Exponential backoff: 1s, 2s, 4s...
      const delay = 1000 * Math.pow(2, attempt - 1);
      await browser.wait(delay);
    }
  }
}
```

---

## Responsive Testing

### Testing Multiple Viewports

```typescript
const viewports = [
  { name: 'mobile', width: 375, height: 667 },
  { name: 'tablet', width: 768, height: 1024 },
  { name: 'desktop', width: 1440, height: 900 },
];

async function testResponsive() {
  for (const viewport of viewports) {
    await browser.setViewport(viewport.width, viewport.height);
    await browser.navigate('https://app.example.com');
    await browser.screenshot(`home-${viewport.name}.png`);
  }
}
```

### Mobile Emulation

```typescript
// Emulate mobile device
await browser.emulate('iPhone 12');

// With touch support
await browser.setViewport({
  width: 390,
  height: 844,
  isMobile: true,
  hasTouch: true,
});
```

---

## Performance Considerations

### Optimizing Browser Tests

```
✅ DO:
- Reuse browser instances when possible
- Use appropriate wait strategies
- Set reasonable timeouts
- Clean up after tests

❌ DON'T:
- Create new browser for each test
- Use fixed sleep times
- Ignore cleanup
- Test on production directly
```

### Parallel Execution

```typescript
// Run tests in parallel browsers
const browsers = await Promise.all([
  launchBrowser(),
  launchBrowser(),
  launchBrowser(),
]);

await Promise.all([
  runTestSuite1(browsers[0]),
  runTestSuite2(browsers[1]),
  runTestSuite3(browsers[2]),
]);
```

---

## Integration with AI Workflow

### B Session Self-Testing

```
┌─────────────────────────────────────────────────────────────────┐
│ B SESSION BROWSER TESTING WORKFLOW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│ 1. Start development server                                      │
│    npm run dev                                                   │
│                                                                  │
│ 2. Launch browser (Playwright MCP or similar)                   │
│    browser.navigate('http://localhost:3000')                    │
│                                                                  │
│ 3. Verify core functionality                                    │
│    - Page loads without errors                                  │
│    - Main elements visible                                      │
│    - Core user flow works                                       │
│                                                                  │
│ 4. Capture evidence                                             │
│    - Screenshot key pages                                       │
│    - Document any console errors                                │
│                                                                  │
│ 5. Report results                                               │
│    - Include screenshots in PR                                  │
│    - Note any issues found                                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### C Session Review Testing

The reviewer (C Session) can use browser automation to verify:

1. **Visual regression** - Compare screenshots before/after
2. **Cross-browser compatibility** - Test in multiple browsers
3. **Accessibility** - Run automated a11y checks
4. **Performance** - Measure load times

---

## Common Selectors Reference

### CSS Selectors

```
#id                    - Select by ID
.class                 - Select by class
element                - Select by tag
[attribute=value]      - Select by attribute
element > child        - Direct child
element descendant     - Any descendant
element:first-child    - First child element
element:nth-child(n)   - Nth child element
```

### Text-Based Selectors

```typescript
// Element containing text
'button:has-text("Submit")'

// Exact text match
'button:text("Submit")'

// Case-insensitive text
'button:text-is("submit")'

// Label-associated elements
'input[aria-label="Email"]'
'input:near(:text("Email"))'
```

### Recommended Selectors

| Priority | Selector Type | Example |
|----------|---------------|---------|
| 1 | Test IDs | `[data-testid="submit-btn"]` |
| 2 | ARIA labels | `[aria-label="Close"]` |
| 3 | Semantic HTML | `button[type="submit"]` |
| 4 | Text content | `button:has-text("Submit")` |
| 5 | CSS classes | `.submit-button` |

---

## Tools Integration

### Playwright MCP

```typescript
// Using Playwright MCP for browser automation
import { playwrightMcp } from '@playwright/mcp';

const browser = await playwrightMcp.connect();
await browser.navigate('https://example.com');
await browser.click('#button');
const screenshot = await browser.screenshot();
```

### External Browser Services

| Service | Use Case |
|---------|----------|
| Playwright Cloud | Cross-browser testing |
| BrowserStack | Real device testing |
| Sauce Labs | Enterprise testing |
| LambdaTest | Parallel execution |

---

## Quick Reference

### Essential Commands

```typescript
// Navigation
browser.navigate(url)
browser.waitForNavigation()
browser.goBack()

// Interaction
browser.click(selector)
browser.type(selector, text)
browser.fill(fields)
browser.select(selector, value)

// Waiting
browser.waitForSelector(selector)
browser.waitForVisible(selector)
browser.waitForText(text)

// Assertions
browser.exists(selector)
browser.isVisible(selector)
browser.getText(selector)
browser.getAttribute(selector, attr)

// Screenshots
browser.screenshot(filename)
browser.screenshot(filename, { element: selector })
```

### Testing Checklist

```
✓ Login/authentication flow works
✓ Main navigation is functional
✓ Forms validate and submit correctly
✓ Error states display properly
✓ Loading states are visible
✓ Mobile responsive design works
✓ No console errors on load
✓ Critical user journeys complete
```
