# Integration Guide

How to add smart personalization to your existing documentation site.

## Quick Start

### Step 1: Add OS-Specific Classes

Wrap platform-specific content in these classes:

```html
<!-- macOS content -->
<div class="os-mac">
  <h3>Install on macOS</h3>
  <pre>brew install your-tool</pre>
</div>

<!-- Windows content -->
<div class="os-windows">
  <h3>Install on Windows</h3>
  <pre>winget install your-tool</pre>
</div>

<!-- Linux content -->
<div class="os-linux">
  <h3>Install on Linux</h3>
  <pre>apt install your-tool</pre>
</div>
```

### Step 2: Deploy the Worker

```bash
wrangler deploy
```

### Step 3: Point Your Domain

In `wrangler.toml`:

```toml
routes = [
  { pattern = "docs.yoursite.com/*", zone_name = "yoursite.com" }
]
```

## Advanced Patterns

### Inline OS-Specific Content

```html
<p>
  Press 
  <span class="os-mac"><kbd>⌘</kbd> + <kbd>S</kbd></span>
  <span class="os-windows"><kbd>Ctrl</kbd> + <kbd>S</kbd></span>
  <span class="os-linux"><kbd>Ctrl</kbd> + <kbd>S</kbd></span>
  to save.
</p>
```

### Dynamic Badges

```html
<div class="env-badges">
  <span class="os-badge" data-os="mac">🍎 macOS</span>
  <span class="os-badge" data-os="windows">🪟 Windows</span>
  <span class="os-badge" data-os="linux">🐧 Linux</span>
</div>
```

The `.active` class is automatically added to the matching badge.

### Code Blocks

```html
<div class="os-mac">
  <pre><code class="language-bash">
# macOS shell configuration
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
  </code></pre>
</div>

<div class="os-windows">
  <pre><code class="language-powershell">
# Windows PowerShell configuration
$profile_path = $PROFILE.CurrentUserAllHosts
Add-Content $profile_path '$env:PATH += ";C:\Tools"'
  </code></pre>
</div>
```

## Styling Tips

### Smooth Transitions

```css
.os-mac, .os-windows, .os-linux {
  transition: opacity 0.3s ease;
}
```

### Highlighted Sections

```css
.os-mac {
  border-left: 4px solid #000;
  padding-left: 20px;
}

.os-windows {
  border-left: 4px solid #0078D4;
  padding-left: 20px;
}
```

## Testing

### Test Different OS Detection

```bash
# Test as macOS user
curl -H "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)" \
  http://localhost:8787/

# Test as Windows user
curl -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)" \
  http://localhost:8787/

# Test as Linux user
curl -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64)" \
  http://localhost:8787/
```

### API Endpoint

Check what environment is detected:

```bash
curl http://localhost:8787/api/environment
```

## Migrating Existing Docs

### 1. Identify OS-Specific Sections

Look for:
- Installation commands
- File paths
- Keyboard shortcuts
- Terminal commands
- Package managers

### 2. Wrap in Classes

Before:
```html
<h3>Installation</h3>
<p>macOS: brew install tool</p>
<p>Windows: winget install tool</p>
```

After:
```html
<div class="os-mac">
  <h3>Installation</h3>
  <pre>brew install tool</pre>
</div>

<div class="os-windows">
  <h3>Installation</h3>
  <pre>winget install tool</pre>
</div>
```

### 3. Test Thoroughly

- Open on Mac, Windows, Linux
- Check mobile devices (iOS, Android)
- Verify content displays correctly
- Ensure fallbacks work for unknown OS

## Best Practices

### 1. Always Provide Fallbacks

```html
<div class="os-mac">Mac instructions</div>
<div class="os-windows">Windows instructions</div>
<div class="os-linux">Linux instructions</div>
<div class="os-unknown">
  <p>Choose your operating system:</p>
  <!-- Show all options -->
</div>
```

### 2. Don't Hide Critical Info

Only hide OS-specific implementation details, not important warnings or concepts.

### 3. Progressive Enhancement

The documentation should work even if JavaScript or the Worker fails.

### 4. Add Visual Indicators

```html
<div class="os-mac">
  <span class="badge">Personalized for macOS</span>
  <!-- content -->
</div>
```

## Performance Optimization

### Enable Caching

```javascript
return new Response(html, {
  headers: {
    'content-type': 'text/html',
    'cache-control': 'public, max-age=3600'
  }
});
```

### Conditional Content

For rarely-needed content, use display:none instead of removing from DOM:

```javascript
.on('.os-mac', {
  element(element) {
    if (os !== 'mac') {
      element.setAttribute('style', 'display: none;');
    }
  }
})
```

## Troubleshooting

### Content Still Shows for All OS

- Check class names match exactly (`.os-mac`, not `.mac`)
- Verify HTMLRewriter is transforming the response
- Test with curl to see raw output

### Detection Not Working

- Check User-Agent header exists
- Verify detectOS() function logic
- Test with `/api/environment` endpoint

### Performance Issues

- Enable caching headers
- Minimize HTML size
- Use efficient CSS selectors

## Examples in the Wild

### Documentation Sites That Could Benefit

- Package manager docs (npm, pip, cargo)
- IDE documentation (VS Code, IntelliJ)
- CLI tool guides (git, docker, kubectl)
- Framework setup guides (React, Vue, Angular)
- Cloud platform docs (AWS, Azure, GCP)

### Real-World Impact

**Before Smart Docs:**
- User reads entire page: ~5 minutes
- User filters mentally: cognitive load HIGH
- User makes mistakes: copy-paste wrong commands

**After Smart Docs:**
- User reads relevant content: ~2 minutes
- User sees only what matters: cognitive load LOW
- User follows instructions: success rate HIGH

---

Need help integrating? Open an issue or submit a PR!
