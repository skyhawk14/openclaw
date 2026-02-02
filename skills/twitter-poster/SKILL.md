---
name: twitter-poster
description: "Twitter automation skill using mcporter + Playwright MCP for browser control. Post tweets with user consent using ROBUST JavaScript methods (not brittle refs)."
metadata: {"openclaw":{"emoji":"🐦","requires":{"bins":["mcporter"]}}}
---

# Twitter Poster (mcporter + Playwright MCP)

A Twitter automation skill that uses **mcporter** with the **Playwright MCP server** for browser control. Posts tweets using **robust JavaScript methods** that don't break when Twitter's UI changes.

## ⚠️ CRITICAL: Avoid Element Refs for Posting

**DO NOT use `browser_click(ref: "eXXX")` for posting tweets!**

Element refs (e.g., e88, e192, e254) change on every page load. This causes failures like:
- "Post button reference has shifted"
- "Element not found"
- "Stale element reference"

**ALWAYS use the JavaScript method with `data-testid` selectors instead.**

## Prerequisites

**Required Setup:**
1. **mcporter CLI** - MCP server manager (installed globally)
2. **Playwright MCP Server** - Configured in mcporter
3. **Twitter Login** - Already logged into Twitter in the Playwright browser session
4. **User Consent** - All posting actions require explicit user approval

## mcporter Setup

**1. Install mcporter globally:**
```bash
npm install -g mcporter
```

**2. Configure Playwright MCP server with persistent Twitter session:**
```bash
mcporter config add playwright \
  --command "npx" \
  --arg "-y" \
  --arg "@playwright/mcp" \
  --arg "--user-data-dir" \
  --arg "$HOME/.playwright-twitter" \
  --scope home
```

**3. Start the mcporter daemon:**
```bash
mcporter daemon start
```

**4. Test the connection:**
```bash
mcporter call 'playwright.browser_navigate(url: "https://x.com")'
```

**5. First-time login:** Navigate to Twitter and log in manually - the session will be saved in `~/.playwright-twitter`

## Robust Twitter Selectors (data-testid)

Twitter uses `data-testid` attributes that are **stable across page loads**:

| Element | Selector | Notes |
|---------|----------|-------|
| Tweet textbox | `[data-testid="tweetTextarea_0"]` | Main compose area |
| Tweet button | `[data-testid="tweetButton"]` | Submit button |
| Sidebar Post btn | `[data-testid="SideNav_NewTweet_Button"]` | Open compose |
| Reply textbox | `[data-testid="tweetTextarea_0"]` | Same as compose |
| Send reply | `[data-testid="tweetButtonInline"]` | Reply submit |

## THE ROBUST POSTING METHOD (ALWAYS USE THIS)

```bash
# Step 1: Navigate directly to compose page
mcporter call 'playwright.browser_navigate(url: "https://x.com/compose/post")'

# Step 2: Wait for compose elements (Playwright waitForSelector)
mcporter call playwright.browser_run_code code='async (page) => { await page.waitForSelector("[data-testid=tweetTextarea_0]", { timeout: 15000 }); }'
sleep 1
# (If this call ever fails, fall back to `sleep 3` and grab a snapshot before proceeding.)

# Step 3: Use JavaScript to type (bypasses brittle refs!)
mcporter call 'playwright.browser_evaluate(function: "
  const textbox = document.querySelector(\"[data-testid=tweetTextarea_0]\");
  if (textbox) {
    textbox.focus();
    document.execCommand(\"insertText\", false, \"YOUR_TWEET_CONTENT_HERE\");
  }
")'

# Optional: capture a snapshot to confirm the textbox picked up the content + record fresh refs
mcporter call 'playwright.browser_snapshot()'

# Step 4: Submit with Cmd+Enter (MOST RELIABLE - never click the Post button!)
mcporter call 'playwright.browser_press_key(key: "Meta+Enter")'
```

**Why this works:**
- `data-testid="tweetTextarea_0"` is stable across page loads
- `document.execCommand("insertText")` triggers Twitter's input handlers
- `Meta+Enter` (Cmd+Enter) is the keyboard shortcut for posting
- No element refs needed!

## Consent-Based Posting

**CRITICAL: Always ask for user consent before any posting action.**

**Consent Flow Pattern:**
```
1. AI generates content
2. Send preview to user via messaging channel:
   
   🐦 Ready to post tweet:
   
   "{tweet_content}"
   
   ✅ Reply 'yes' to post
   📝 Reply 'edit: [new content]' to modify
   ❌ Reply 'no' to cancel

3. Wait for user response
4. Execute mcporter commands ONLY after approval
5. Confirm action completion to user
```

## Complete Posting Example

**User Request:** "Post a tweet about AI agents"

**1. Generate content:**
```
"Building personal AI agents with @OpenClaw_AI - fitness coaching, coding assistance, and automated research. The future is agentic! 🚀🤖 #AI #Agents"
```

**2. Ask consent via channel**

**3. On approval, execute ROBUST commands:**

```bash
# Navigate directly to compose
mcporter call 'playwright.browser_navigate(url: "https://x.com/compose/post")'

# Wait for page
mcporter call playwright.browser_run_code code='async (page) => { await page.waitForSelector("[data-testid=tweetTextarea_0]", { timeout: 15000 }); }'
sleep 1
# If this call errors, use `sleep 3` instead and continue with the fallback steps.

# Type using JavaScript (stable!)
mcporter call 'playwright.browser_evaluate(function: "
  const textbox = document.querySelector(\"[data-testid=tweetTextarea_0]\");
  if (textbox) {
    textbox.focus();
    document.execCommand(\"insertText\", false, \"Building personal AI agents with @OpenClaw_AI - fitness coaching, coding assistance, and automated research. The future is agentic! 🚀🤖 #AI #Agents\");
  }
")'

# Optional: snapshot for confirmation + fallback refs
mcporter call 'playwright.browser_snapshot()'

# Submit with keyboard shortcut
mcporter call 'playwright.browser_press_key(key: "Meta+Enter")'
```

**4. Confirm:** "✅ Tweet posted successfully!"

## Available Playwright Tools

| Tool | Command | Description |
|------|---------|-------------|
| `browser_navigate` | `mcporter call 'playwright.browser_navigate(url: "...")'` | Navigate to a URL |
| `browser_snapshot` | `mcporter call 'playwright.browser_snapshot()'` | Get page structure |
| `browser_click` | `mcporter call 'playwright.browser_click(ref: "e123")'` | Click element (avoid for posting!) |
| `browser_type` | `mcporter call 'playwright.browser_type(ref: "e123", text: "...")'` | Type text (avoid for posting!) |
| `browser_press_key` | `mcporter call 'playwright.browser_press_key(key: "Meta+Enter")'` | Press keyboard shortcut |
| `browser_evaluate` | `mcporter call 'playwright.browser_evaluate(function: "...")'` | Run JavaScript (PREFERRED!) |
| `browser_run_code` | `mcporter call 'playwright.browser_run_code(code: "...")'` | Run Playwright snippets (great for waits) |

## Dynamic Recovery Steps (when tools misbehave)

Most runs should succeed with the JavaScript path above. If you hit tooling gaps (e.g., the `browser_run_code` wait call times out or `browser_evaluate` rejects syntax), fall back temporarily:

1. **Manual wait:** `sleep 3` (or similar) gives compose time to render when no wait helper exists.
2. **Snapshot before typing:** `mcporter call 'playwright.browser_snapshot()'` both captures current UI state and surfaces fresh `ref=` handles.
3. **Fallback typing:** If JavaScript injection keeps failing, use the textbox ref from the snapshot with `browser_type`. Example:
  ```bash
  mcporter call 'playwright.browser_type(ref: "e99", text: "Your tweet content here")'
  ```
4. **Verify with another snapshot:** Confirm the textbox shows the intended copy and grab updated refs if the DOM re-rendered.
5. **Submit with Meta+Enter:** Even when you typed via a ref, stick to the keyboard shortcut for posting.

Return to the JavaScript path on the next run; the fallback is a stopgap, not the default.

## Trending Topics Monitoring

```bash
# Navigate to trending page
mcporter call 'playwright.browser_navigate(url: "https://x.com/explore/tabs/trending")'

# Extract with JavaScript
mcporter call 'playwright.browser_evaluate(function: "Array.from(document.querySelectorAll(\"[data-testid=trend]\")).map(el => el.textContent).join(\"\\n\")")'
```

## Reply Monitoring

```bash
# Navigate to mentions
mcporter call 'playwright.browser_navigate(url: "https://x.com/notifications/mentions")'

# Get snapshot to see mentions
mcporter call 'playwright.browser_snapshot()'
```

## Error Handling

| Error | Solution |
|-------|----------|
| Not logged in | Navigate to x.com/login, prompt user to log in |
| Element not found | Use JavaScript method, not refs |
| Rate limited | Wait and retry, reduce frequency |
| Session expired | Re-authenticate in browser |
| mcporter not running | `mcporter daemon start` |

## Quick Reference

**Post a Tweet (ALWAYS use this method):**
```bash
# 1. Generate content
# 2. Ask consent: "🐦 Ready to post: [content] - Reply yes/no"
# 3. On 'yes': Execute ROBUST commands

mcporter call 'playwright.browser_navigate(url: "https://x.com/compose/post")'
mcporter call playwright.browser_run_code code='async (page) => { await page.waitForSelector("[data-testid=tweetTextarea_0]", { timeout: 15000 }); }'
sleep 1
mcporter call 'playwright.browser_evaluate(function: "
  const textbox = document.querySelector(\"[data-testid=tweetTextarea_0]\");
  if (textbox) {
    textbox.focus();
    document.execCommand(\"insertText\", false, \"Your tweet content here\");
  }
")'
mcporter call 'playwright.browser_snapshot()'
mcporter call 'playwright.browser_press_key(key: "Meta+Enter")'

# 4. Confirm: "✅ Tweet posted!"
```

**⚠️ Never use browser_click/browser_type with element refs for posting - refs change every page load!**

## mcporter Tips

1. **Use JavaScript for posting** - `browser_evaluate` with CSS selectors is stable
2. **Use `Meta+Enter` to submit** - More reliable than clicking Post button
3. **Wait for compose to render** - `mcporter call playwright.browser_run_code code='async (page) => { await page.waitForSelector("[data-testid=tweetTextarea_0]", { timeout: 15000 }); }'`
4. **Check daemon status** - `mcporter daemon status`
5. **Restart if connection lost** - `mcporter daemon stop && mcporter daemon start`
6. **Keep browser session alive** - Don't close between actions
7. **Session persists in** `~/.playwright-twitter` - Login once, use forever

## Spinner Troubleshooting

If the compose dialog shows a perpetual **Loading** spinner:

- Call `mcporter call playwright.browser_run_code code='async (page) => { await page.waitForSelector("[data-testid=tweetTextarea_0]", { timeout: 15000 }); }'` to block until the textbox renders.
- If the wait times out, grab a snapshot (`browser_snapshot`) to confirm whether Twitter redirected to login or surfaced a modal. Clear any `beforeunload` prompts with `browser_press_key(key: "Escape")`.
- Bounce the daemon when the browser becomes unresponsive: `mcporter daemon stop && mcporter daemon start`.
- Re-authenticate the stored profile when you see the login screen by running:
  1. `mcporter daemon stop`
  2. `npx playwright open --user-data-dir="$HOME/.playwright-twitter" https://x.com/login`
  3. Log in manually (handle 2FA), close the window, then `mcporter daemon start`

## mcporter Configuration Location

**Config file:** `~/.mcporter/mcporter.json`
```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": [
        "-y",
        "@playwright/mcp",
        "--user-data-dir",
        "/Users/YOUR_USERNAME/.playwright-twitter"
      ]
    }
  }
}
```
