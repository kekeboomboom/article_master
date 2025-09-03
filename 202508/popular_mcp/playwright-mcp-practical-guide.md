# Playwright MCP: The Hands-On Guide to Browser Automation with AI

## Quick Start: Your First Automation in 5 Minutes

### What is Playwright MCP?

Playwright MCP (Model Context Protocol) is a bridge between AI assistants and browser automation. Think of it as giving Claude, Cursor, or VS Code the ability to control a real browser - clicking buttons, filling forms, and taking screenshots just like a human would. The key difference? Instead of using screenshots and computer vision, it uses the browser's accessibility tree for rock-solid, deterministic control.

### Installation: Pick Your Tool

**For Claude Desktop (Most Popular):**
```json
// Edit claude_desktop_config.json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```
After saving, completely restart Claude Desktop. You MUST also kill any background Claude processes in Task Manager (Windows) or Activity Monitor (Mac).

**For VS Code with Claude Code:**
```bash
claude mcp add playwright npx -- @playwright/mcp@latest
```

**For Cursor IDE:**
1. Go to Settings → MCP → Add new MCP Server
2. Name it "playwright"
3. Use command: `npx @playwright/mcp@latest`

### Your First Automation

Once installed, here's what actually happens:

**In Claude Desktop:**
```
You: "Use playwright mcp to open a browser to example.com"
```
A Chrome window opens in front of you - not headless, but visible. Claude now controls this browser.

**Important:** You must say "playwright mcp" the first time, or Claude might try using bash commands instead.

### Real Setup Issues and Fixes

**Problem 1: "Playwright MCP tools not available"**
```bash
# Fix: The server shows green but no tools? You forgot to restart.
# Kill ALL Claude processes, not just close the window.
# On Mac: pkill -f Claude
# On Windows: Use Task Manager to end all Claude tasks
```

**Problem 2: WSL Users - Chromium Sandbox Fails**
```bash
# Add this to your config:
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--", "--no-sandbox"]
    }
  }
}
```

## Real-World Browser Automation

### Scenario 1: Login That Actually Works

Here's how to handle a real login flow with session persistence:

```
You: "Use playwright mcp to navigate to github.com/login"
Claude: [Opens browser to GitHub login]

You: "I'll login manually now"
[You type your credentials]

You: "Now navigate to github.com/settings/profile and update my bio"
Claude: [Uses the authenticated session to access settings]
```

The magic: Cookies persist for the entire session. No need to handle auth tokens or OAuth flows - just login once manually and let Claude work with the authenticated session.

### Scenario 2: Form Filling with Dynamic Validation

Real example from testing a signup form:

```
You: "Fill out the registration form on example-app.com/register"

What Claude actually does:
1. Navigates to the page
2. Waits for form to load
3. Identifies form fields using accessibility labels
4. Fills each field
5. Handles dynamic validation (waits for "username available" message)
6. Clicks submit
7. Verifies success message appears
```

If validation fails, Claude sees it immediately through the accessibility tree - no screenshot analysis needed.

### Scenario 3: E-commerce Checkout Flow

Here's a complete checkout automation that actually runs:

```
Step 1: "Navigate to shop.example.com and search for 'laptop'"
- Browser opens, search executes

Step 2: "Add the first product to cart"
- Claude identifies product cards, clicks "Add to Cart" on first item
- Waits for cart counter to update

Step 3: "Proceed to checkout"
- Clicks cart icon
- Verifies item in cart
- Clicks "Checkout"

Step 4: "Fill shipping information"
- Claude fills form fields using actual field labels
- Handles dropdown selections for country/state
- Manages address autocomplete if present

Step 5: "Complete the order"
- Selects payment method
- Fills test credit card (4242 4242 4242 4242)
- Clicks "Place Order"
- Captures order confirmation number
```

### Handling AJAX and Dynamic Content

Real problem: Testing a React app with lazy-loaded content.

```
You: "Click 'Load More' until you see a product named 'Special Edition'"

Claude's approach:
1. Clicks "Load More"
2. Waits for new content to appear in DOM
3. Checks if "Special Edition" is present
4. Repeats until found or no more content

The actual code Claude generates:
while (true) {
  await page.click('button:has-text("Load More")');
  await page.waitForTimeout(1000); // Wait for content
  if (await page.locator('text=Special Edition').count() > 0) break;
  if (await page.locator('button:has-text("Load More")').isDisabled()) break;
}
```

### Screenshot Debugging in Action

When a test fails, here's the debugging workflow:

```
You: "The checkout button isn't working. Take a screenshot and show me what's happening"

Claude:
1. Takes screenshot: checkout-debug-1703021234.png
2. Captures console errors
3. Checks network tab for failed requests
4. Reports: "Found 3 console errors and 2 failed API calls (403 on /api/checkout)"
```

## AI-Powered Test Generation

### From Manual Test Case to Automated Test

**Your Manual Test Case:**
```
1. User goes to login page
2. User enters invalid credentials
3. Error message should appear
4. User enters valid credentials  
5. User should see dashboard
```

**Tell Claude:**
```
"Use playwright mcp to convert this test case to an automated test:
[paste test case]"
```

**What Actually Happens:**
1. Claude navigates to the login page
2. Tries invalid credentials (generates random ones)
3. Verifies error message appears
4. Clears fields and enters valid test credentials
5. Verifies dashboard loads
6. Generates complete Playwright test code

**The Generated Test:**
```typescript
test('login flow with invalid and valid credentials', async ({ page }) => {
  await page.goto('/login');
  
  // Invalid login attempt
  await page.fill('[name="email"]', 'invalid@test.com');
  await page.fill('[name="password"]', 'wrongpass');
  await page.click('button[type="submit"]');
  await expect(page.locator('.error-message')).toContainText('Invalid credentials');
  
  // Valid login
  await page.fill('[name="email"]', 'valid@test.com');
  await page.fill('[name="password"]', 'correctpass');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

### Agent Mode: Finding Bugs Automatically

Real bug discovery example from production:

```
You: "Explore the search feature on movies.example.com and generate tests"

Claude's exploration:
1. Types "Star Wars" in search
2. Observes results
3. FINDS BUG: Search for "Star Wars" returns movie titled "Kill"
4. Documents the bug
5. Generates regression test to catch this issue
```

The bug was real - a production issue missed by manual testing but caught by AI exploration in under 30 seconds.

### Plain English to Test Code

**You write:**
```
"Test that users can't checkout with an empty cart"
```

**Claude does:**
1. Navigates to shop
2. Goes directly to cart (empty)
3. Looks for checkout button
4. Verifies it's disabled or shows error
5. Generates test ensuring this validation exists

**Result:**
```typescript
test('prevent checkout with empty cart', async ({ page }) => {
  await page.goto('/cart');
  const checkoutBtn = page.locator('[data-testid="checkout-button"]');
  
  // Either button is disabled or clicking shows error
  if (await checkoutBtn.isEnabled()) {
    await checkoutBtn.click();
    await expect(page.locator('.error')).toContainText('cart is empty');
  } else {
    await expect(checkoutBtn).toBeDisabled();
  }
});
```

## Practical Debugging & Troubleshooting

### The Connection Problem Saga

**Symptom:** "Playwright MCP tools not showing up"

**Debug Steps:**
1. Check MCP status: In Claude, type `/mcp`
2. Look for green dot next to playwright
3. If red, check logs:
   ```bash
   # Mac/Linux
   tail -f ~/Library/Logs/Claude/mcp*.log
   
   # Windows
   Get-Content "$env:APPDATA\Claude\logs\mcp*.log" -Tail 20 -Wait
   ```

**Common Fix #1: Port Conflict**
```bash
# Check if port 3000 is in use
netstat -an | grep 3000

# Use different port in config:
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--port=3456"]
    }
  }
}
```

**Common Fix #2: Import Warnings**
If you see: "ExperimentalWarning: Use 'importAttributes' instead of 'importAssertions'"
```bash
# Update to latest version
npm update @playwright/mcp
```

### Using Trace Viewer for Failed Tests

When a test fails in CI, here's the actual debugging workflow:

1. **Get the trace file:**
```bash
# CI saves trace on failure
artifacts/test-results/login-test/trace.zip
```

2. **Open trace locally:**
```bash
npx playwright show-trace trace.zip
```

3. **What you actually see:**
- Timeline of every action
- DOM snapshot at each step
- Network requests (with failures highlighted)
- Console logs
- Screenshot at point of failure

**Real Example:**
Test failed because "Submit" button wasn't clicked. Trace shows:
- Button was present in DOM
- But covered by a cookie banner (z-index issue)
- Console had error: "Cannot click element at point (400,300)"

### Network Monitoring Magic

**Catching API failures:**
```
You: "Run the checkout test and monitor all API calls"

Claude reports:
- POST /api/cart: 200 OK (143ms)
- GET /api/shipping: 200 OK (89ms)  
- POST /api/payment: 500 Internal Server Error
- Error body: {"error": "Payment processor timeout"}
```

This pinpoints backend issues instantly - no guessing if it's frontend or backend.

### Performance Gotchas

**Problem:** Tests take forever
**Investigation:** 
```
You: "Why is the test so slow? Check performance metrics"

Claude's analysis:
- Page load: 8.3s (slow!)
- Largest Contentful Paint: 7.2s
- 47 unoptimized images loading
- 3 JavaScript files over 1MB each
```

**Solution:** Add wait strategies:
```typescript
// Instead of arbitrary waits
await page.waitForTimeout(5000); // Bad!

// Wait for specific conditions
await page.waitForLoadState('networkidle'); // Good!
await page.waitForSelector('.products-loaded'); // Better!
```

## Advanced Use Cases

### Cross-Browser Testing Loop

**Actual working script:**
```bash
browsers=("chromium" "firefox" "webkit")
for browser in "${browsers[@]}"; do
  echo "Testing in $browser"
  
  # Tell Claude to use specific browser
  "Use playwright mcp with $browser to test login flow"
  
  # Claude runs same test in each browser
  # Captures browser-specific screenshots
  # Reports browser-specific issues
done
```

**Real issues found:**
- Safari (webkit): Date picker doesn't work
- Firefox: CSS Grid layout breaks on checkout page
- Chrome: Everything works (of course)

### Visual Regression That Actually Works

```
You: "Take a screenshot of the homepage, then compare after deploying changes"

Step 1: Baseline
- Screenshot saved: homepage-baseline.png

Step 2: After deployment  
- Screenshot saved: homepage-new.png
- Claude reports: "Detected 423 pixel differences"
- Shows diff image highlighting changes
- Main change: Hero image updated (expected)
- Unexpected: Footer links color changed
```

### API + Browser Testing Combo

**Real scenario:** Testing data consistency

```
Step 1: "Create a user via API"
Claude executes:
POST /api/users
Body: {"name": "Test User", "email": "test@example.com"}
Response: {"id": 12345}

Step 2: "Now verify the user appears in the admin panel"
Claude:
- Navigates to /admin/users
- Searches for "test@example.com"  
- Verifies user ID 12345 is displayed
- Confirms all fields match API response
```

### Multi-Client Load Testing

```
You: "Simulate 5 users browsing the site simultaneously"

Claude's approach:
- Opens 5 browser tabs
- Each tab navigates to different sections
- Monitors shared resources (cart, inventory)
- Reports when race conditions occur
- Example finding: "Cart count desyncs when 2 users add same item within 100ms"
```

### Production Monitoring Pattern

**Weekly automation check:**
```
Every Monday at 9 AM:
1. Claude opens production site
2. Runs smoke test suite:
   - Login works
   - Search returns results
   - Cart operations function
   - Checkout process completes (test mode)
3. Takes screenshots of key pages
4. Compares with last week's screenshots
5. Reports any visual or functional changes
```

## Key Takeaways

1. **Always say "playwright mcp"** first time or Claude uses bash
2. **Login manually once** - cookies persist for the session
3. **Traces beat screenshots** for debugging every time
4. **Let AI explore** - it finds bugs humans miss
5. **Browser stays visible** - you can intervene anytime
6. **Network tab is gold** - shows exactly why things fail
7. **Cross-browser issues are real** - test all three engines

Remember: Playwright MCP isn't about replacing testers - it's about multiplying what one person can accomplish. You still need to understand your app, but now you can test it 10x faster with AI assistance.