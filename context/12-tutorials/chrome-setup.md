# Chrome Setup Guide

> How to set up Chrome for WebMCP development — installing Chrome Canary 146+, enabling the WebMCP flag, verifying with feature detection, and installing essential developer extensions.

---

## Overview

WebMCP is currently available as an **early preview** behind an experimental flag in Chrome. This guide walks through the complete setup process for developing and testing WebMCP tools.

---

## Step 1: Install Chrome Canary 146+

WebMCP requires **Chrome version 146 or higher**, currently available in the Canary channel.

### Download Chrome Canary

- **macOS / Windows / Linux**: [https://www.google.com/chrome/canary/](https://www.google.com/chrome/canary/)

Chrome Canary installs alongside your regular Chrome installation — it won't replace your stable browser.

### Verify Your Version

1. Open Chrome Canary
2. Navigate to `chrome://version`
3. Confirm the version number starts with **146.0** or higher

```
Google Chrome    146.0.xxxx.x (Official Build) canary (arm64)
```

> **Note**: As WebMCP matures, it may become available in Chrome Dev, Beta, and eventually Stable channels. Check the [Chrome release schedule](https://chromiumdash.appspot.com/schedule) for updates.

---

## Step 2: Enable the WebMCP Flag

### Primary Flag (Chrome Canary)

1. Open Chrome Canary
2. Navigate to: `chrome://flags`
3. In the search box, type: **"WebMCP for testing"**
4. Find the flag and set it to **Enabled**
5. Click **Relaunch** at the bottom of the page

### Alternative Flag (Chrome Dev / Some Versions)

In some Chrome versions, the flag may be under a different name:

1. Navigate to: `chrome://flags`
2. Search for: **"Experimental Web Platform features"**
3. Set to **Enabled**
4. Click **Relaunch**

> **Community note**: In Canary builds, the dedicated "WebMCP for testing" flag has been confirmed. In some Dev channel builds, it may fall under "Experimental Web Platform features" instead. Try both if one doesn't appear.

### Command Line Flag (Alternative)

You can also enable WebMCP via command line:

```bash
# macOS
/Applications/Google\ Chrome\ Canary.app/Contents/MacOS/Google\ Chrome\ Canary \
  --enable-features=WebMCP

# Windows
"C:\Users\%USERNAME%\AppData\Local\Google\Chrome SxS\Application\chrome.exe" \
  --enable-features=WebMCP

# Linux
google-chrome-canary --enable-features=WebMCP
```

---

## Step 3: Verify WebMCP is Active

### DevTools Console Check

1. Open any webpage in Chrome Canary
2. Open DevTools (F12 or Cmd+Option+I on macOS)
3. In the Console tab, type:

```js
console.log("modelContext" in navigator);
// Expected output: true
```

If it returns `false`, the flag wasn't enabled correctly. Retry Step 2.

### Feature Detection Script

Add this to any page for a comprehensive check:

```js
(function checkWebMCP() {
  if (!("modelContext" in navigator)) {
    console.error("❌ WebMCP not available");
    console.info("Enable: chrome://flags → 'WebMCP for testing' → Enabled → Relaunch");
    return;
  }

  console.log("✅ WebMCP is available");
  console.log("Available methods:", Object.getOwnPropertyNames(
    Object.getPrototypeOf(navigator.modelContext)
  ));
})();
```

---

## Step 4: Install the Model Context Tool Inspector

The **Model Context Tool Inspector** is a Chrome extension built by the Chrome team for debugging WebMCP tools.

### Installation

1. Open the Chrome Web Store:
   [Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
2. Click **Add to Chrome**
3. Confirm the installation
4. The extension icon (🔧) appears in your toolbar

### What It Does

| Feature | Description |
|---------|-------------|
| **Tool listing** | Shows all registered tools on the current page |
| **Schema viewer** | Displays each tool's inputSchema with descriptions |
| **Manual execution** | Execute tools with custom inputs |
| **Gemini integration** | Test tools via natural language using Google Gemini API |
| **Declarative detection** | Automatically detects forms with `toolname` attributes |
| **Response viewer** | Shows tool return values |

### Using the Inspector

1. Navigate to a page with WebMCP tools
2. Click the extension icon in the toolbar
3. The inspector panel shows all registered tools
4. Click a tool to expand its schema
5. Fill in the input fields
6. Click **Execute** to invoke the tool
7. View the response in the output panel

### Gemini API Integration

The inspector can connect to Google's Gemini API for natural language testing:

1. Click the **Settings** gear icon in the inspector
2. Enter your [Gemini API key](https://aistudio.google.com/)
3. Type natural language queries like "Search for flights from SFO to JFK tomorrow"
4. The inspector uses Gemini to select the right tool and fill parameters

---

## Step 5: Join the Early Preview Program

For access to official documentation and demos:

1. Visit the [Chrome AI Early Preview Program](https://developer.chrome.com/docs/ai/join-epp)
2. Sign up with your Google account
3. You'll get access to:
   - Full API documentation
   - Hosted demos (including the travel demo)
   - Direct feedback channels to the Chrome team

---

## Step 6: Test with the Official Travel Demo

Verify your setup is complete with Chrome's official demo:

1. Navigate to: `https://travel-demo.bandarra.me/`
2. Open the Model Context Tool Inspector
3. You should see the `searchFlights` tool listed
4. Execute it with:
   - origin: `SFO`
   - destination: `JFK`
   - outbound_date: `2026-03-15`
5. The page should update with flight results

If this works, your WebMCP development environment is ready.

---

## Development Workflow

### Recommended Setup

```
Chrome Canary (with WebMCP flag)
├── Model Context Tool Inspector extension
├── DevTools Console (for debugging)
├── Your development page (localhost or file://)
└── Optional: Gemini API key for NL testing
```

### Local Development Server

Any local server works. Quick options:

```bash
# Python
python3 -m http.server 8080

# Node.js
npx serve .

# PHP
php -S localhost:8080
```

### DevTools Tips

1. **Console tab**: Test `navigator.modelContext` methods directly
2. **Sources tab**: Set breakpoints in your `execute` callbacks
3. **Network tab**: Monitor any API calls your tools make
4. **Elements tab**: Watch DOM changes from tool execution
5. **Application tab**: Check service worker status if using workers

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `navigator.modelContext` is undefined | Enable flag, relaunch Chrome, verify version ≥ 146 |
| Flag not found in `chrome://flags` | Try "Experimental Web Platform features" instead |
| Extension doesn't show tools | Refresh page after extension install; check console for JS errors |
| Tools appear but don't execute | Check for errors in DevTools console |
| Declarative form not detected | Ensure both `toolname` AND `tooldescription` are present |
| Chrome Canary won't install | Ensure you're downloading from the official source |
| Multiple Chrome versions conflict | Canary runs independently from Stable Chrome |

---

## Browser Support Status

| Browser | Status | Notes |
|---------|--------|-------|
| Chrome Canary 146+ | ✅ Behind flag | Full imperative + declarative API |
| Chrome Dev | 🔄 Rolling out | May require "Experimental Web Platform features" flag |
| Chrome Beta | ❌ Not yet | Expected in future milestone |
| Chrome Stable | ❌ Not yet | No timeline announced |
| Edge | ❌ Not yet | Microsoft co-authored the spec; expect future support |
| Firefox | ❌ Not yet | No public position |
| Safari | ❌ Not yet | No public position |

For non-Chrome browsers, see the [Polyfill Guide](./polyfill-guide.md).

---

## See Also

- [Getting Started](./getting-started.md) — Create your first tool after setup
- [Testing Tools](./testing-tools.md) — Advanced testing patterns
- [Polyfill Guide](./polyfill-guide.md) — Using WebMCP in non-Chrome browsers
- [What Is WebMCP](../01-overview/what-is-webmcp.md) — Core concepts
- [Browser support status](../01-overview/browser-support.md) — Browser support status
- [DevTools quickstart](../08-ecosystem/official-tools/devtools-quickstart.md) — DevTools quickstart
- [Model Context Tool Inspector](../08-ecosystem/official-tools/model-context-tool-inspector.md) — Model Context Tool Inspector
- [Google Chrome's role](../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [Complete API reference](../15-reference/api-reference.md) — Complete API reference
- [WebMCP playground](../08-ecosystem/demos/playground-webmcp-sh.md) — WebMCP playground
