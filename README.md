# ChromeMCP

Here’s your complete `README.md` for a Chrome extension acting as an **MCP (Model Context Provider):**

---

````markdown
# 🧠 Chrome Extension as a Model Context Provider (MCP)

This project implements a Chrome extension that simulates the functionality of a **Model Context Provider (MCP)**—similar to what Azure Copilot uses, but entirely browser-based.

## 🚀 Overview

This extension captures contextual data from the user’s current tab, processes it, and sends it to a cloud or local LLM backend. It then receives a grounded response and displays it in a Chrome UI (popup, sidebar, or directly injected into the webpage).

---

## 🧩 Features

- ✅ Extracts page content and metadata
- ✅ Sends contextual payloads to an LLM API
- ✅ Displays results via popup or sidebar
- ✅ Secure context processing via service worker
- ✅ Easily customizable for summaries, copilots, or research tools

---

## 🧱 Architecture

| Component        | Role                                               |
|------------------|----------------------------------------------------|
| `manifest.json`  | Declares permissions and extension entry points    |
| `content.js`     | Collects context from the webpage                  |
| `background.js`  | Acts as the context broker (MCP core logic)        |
| `popup.html/.js` | UI layer to display LLM results                    |
| `llm-backend`    | Your local or remote LLM endpoint                  |

---

## 🧠 Flow Diagram

```text
[ Page Content ]
     ↓
[ Content Script ] → Sends context →
[ Background Worker (MCP) ] → Calls LLM API →
     ↓                              ↓
[ Injects Result ]          [ Popup / UI Renders ]
````

---

## 📁 File Structure

```text
chrome-mcp-extension/
├── manifest.json
├── background.js
├── content.js
├── popup.html
├── popup.js
```

---

## 📄 `manifest.json` (v3)

```json
{
  "name": "Chrome MCP",
  "version": "1.0",
  "manifest_version": 3,
  "permissions": ["tabs", "scripting", "storage"],
  "host_permissions": ["<all_urls>"],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"]
    }
  ]
}
```

---

## 📄 `content.js`

```js
chrome.runtime.sendMessage({
  type: "PAGE_CONTEXT",
  url: window.location.href,
  content: document.body.innerText.slice(0, 1000),
});
```

---

## 📄 `background.js`

```js
chrome.runtime.onMessage.addListener((msg, sender) => {
  if (msg.type === "PAGE_CONTEXT") {
    const payload = {
      url: msg.url,
      snippet: msg.content,
      timestamp: new Date().toISOString()
    };

    fetch("https://your-llm-backend.com/query", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ context: payload })
    })
    .then(res => res.json())
    .then(data => {
      chrome.tabs.sendMessage(sender.tab.id, {
        type: "LLM_RESULT",
        result: data
      });
    });
  }
});
```

---

## 📄 `popup.html`

```html
<!DOCTYPE html>
<html>
  <head><title>MCP Result</title></head>
  <body>
    <h1>AI Response</h1>
    <pre id="output">Waiting for result...</pre>
    <script src="popup.js"></script>
  </body>
</html>
```

---

## 📄 `popup.js`

```js
chrome.runtime.onMessage.addListener((msg) => {
  if (msg.type === "LLM_RESULT") {
    document.getElementById("output").innerText = msg.result.text || JSON.stringify(msg.result);
  }
});
```

---

## 🔐 Permissions and Security

* `host_permissions`: Restrict domains to only those needed
* `storage`: Store user settings or tokens
* `identity`: (Optional) Add OAuth for secure, scoped identity access

---

## 💡 Use Case Ideas

* **Page Summarizer**: LLM summarizes the current webpage
* **Developer Copilot**: Reads dev tools and suggests fixes
* **Research Assistant**: Tracks academic papers and grounds questions
* **Enterprise Browser Assistant**: Custom copilot for internal tools

---

## 🛠️ Setup

1. Clone this repo
2. Replace `https://your-llm-backend.com/query` with your actual LLM endpoint
3. Load the extension in Chrome:

   * Go to `chrome://extensions`
   * Enable **Developer Mode**
   * Click **Load Unpacked** and select the folder

---

## 📦 Optional: Backend (LLM) Endpoint

```bash
POST /query
Content-Type: application/json

{
  "context": {
    "url": "https://example.com",
    "snippet": "This is the first 1000 characters of the page...",
    "timestamp": "2025-07-25T13:00:00Z"
  }
}
```

LLM response:

```json
{
  "text": "This article describes how protein folding impacts..."
}
```

---

## 🧪 Future Enhancements

* Add WebSocket support for live context
* Support file uploads or user notes
* Add local caching or embeddings
* Integrate with a local LLM (e.g., ollama, LM Studio)

---

## 📜 License

MIT License — go build the web-native copilot of your dreams.

```

Would you like this as a downloadable ZIP or GitHub starter template?
```
