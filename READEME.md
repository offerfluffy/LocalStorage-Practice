### ⚙️ `localStorage`

**What It Is:**
`localStorage` is a synchronous key-value store available in all modern browsers. It persists data across sessions and page reloads, with a per-origin storage limit (typically \~5MB).

---

### 🔍 Key Properties

* **Synchronous**: Blocks the main thread. Bad for large/frequent reads/writes.
* **Persistent**: Survives tab/browser close.
* **String-Only**: Stores everything as strings. Must `JSON.stringify` objects manually.
* **Origin-Scoped**: Isolated by protocol + host + port.
* **No TTL**: Requires custom logic for expiration.

---

### ✅ When to Use

* Storing non-sensitive UI state (e.g., theme, onboarding status).
* Lightweight, client-only flags or cache.

### ❌ Avoid For

* Authentication tokens (vulnerable to XSS).
* Large datasets (use `IndexedDB`).
* Critical state requiring consistency or reactivity.

---

### 🛡 Best Practices

1. **Wrap it**: Create utility functions to handle parsing and error fallback.
2. **Add TTL manually**:

   ```js
   function setWithExpiry(key, val, ttl) {
     localStorage.setItem(key, JSON.stringify({ val, exp: Date.now() + ttl }));
   }
   function getWithExpiry(key) {
     const item = JSON.parse(localStorage.getItem(key));
     return item && Date.now() < item.exp ? item.val : null;
   }
   ```
3. **Don't use for tokens**: Prefer httpOnly cookies.
4. **Throttle writes**: Use debounce for things like autosaves.

---

### ⚖️ Compared to Others

| Feature      | `localStorage` | `sessionStorage` | `IndexedDB` |
| ------------ | -------------- | ---------------- | ----------- |
| Async?       | ❌              | ❌                | ✅           |
| Expiry?      | ❌              | ✅ (on tab close) | ❌ (manual)  |
| Size Limit   | \~5MB          | \~5MB            | 100MB+      |
| Complex Data | ❌              | ❌                | ✅           |

---

### 🔄 Other Tab Sync

The `storage` event only fires in **other tabs**, useful for sync:

```js
window.addEventListener("storage", (e) => console.log(e.key, e.newValue));
```

---

### ⚠️ Final Tip

`localStorage` is a tool — not a database, not a cache layer, and not secure storage. Use it minimally, and abstract access for flexibility and future-proofing.