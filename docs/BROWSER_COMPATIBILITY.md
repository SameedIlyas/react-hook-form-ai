# Browser Compatibility

Browser requirements and compatibility information for React Hook Form AI.

## Overview

React Hook Form AI works in all modern browsers. However, the Chrome Built-in AI provider has specific requirements.

## Chrome Built-in AI Requirements

The Chrome Built-in AI provider requires **Chrome version 127 or higher** with experimental AI features enabled.

### Browser Requirements

- **Chrome**: Version 127+ (recommended: 139+)
- **Edge**: Not supported (Chromium-based Edge doesn't include AI features)
- **Safari**: Not supported
- **Firefox**: Not supported
- **Opera**: Not supported

### Enabling Chrome AI

1. **Update Chrome**
   - Ensure you're running Chrome 127 or later
   - Check version: `chrome://settings/help`

2. **Enable AI Features**
   - Visit `chrome://flags/#optimization-guide-on-device-model`
   - Set to "Enabled"
   - Restart Chrome

3. **Verify Availability**
   ```tsx
   const { aiAvailability } = useForm();
   console.log(aiAvailability);
   // { available: true, status: 'readily', needsDownload: false }
   ```

### Model Download

On first use, Chrome AI requires downloading a language model (~1-2GB).

**Requirements:**
- User interaction (button click) to start download
- Stable internet connection
- ~1-2GB of free disk space
- User must keep the tab open during download

**Handling Download:**

```tsx
const { aiAvailability, aiDownloadProgress, aiAutofill } = useForm({
  ai: { providers: [{ type: 'chrome' }] }
});

if (aiAvailability?.needsDownload) {
  return (
    <div>
      <p>Chrome AI requires a one-time model download (~1-2GB)</p>
      <button onClick={() => aiAutofill()}>
        📥 Download AI Model & Start
      </button>
    </div>
  );
}

if (aiAvailability?.status === 'downloading') {
  return (
    <div>
      <p>Downloading AI model... Please keep this tab open.</p>
      <progress value={aiDownloadProgress || 0} max={100} />
      <p>{Math.round(aiDownloadProgress || 0)}% complete</p>
    </div>
  );
}
```

## Other AI Providers

### OpenAI Provider

**Browser Requirements:**
- Any modern browser with JavaScript enabled
- Internet connection required

**Compatibility:**
- ✅ Chrome
- ✅ Firefox
- ✅ Safari
- ✅ Edge
- ✅ Opera
- ✅ Mobile browsers

### Custom Server Provider

**Browser Requirements:**
- Any modern browser with JavaScript enabled
- Internet connection required
- CORS must be configured on your server

**Compatibility:**
- ✅ Chrome
- ✅ Firefox
- ✅ Safari
- ✅ Edge
- ✅ Opera
- ✅ Mobile browsers

### Browser AI Provider

**Browser Requirements:**
- Depends on the specific browser AI service
- Internet connection required

## Fallback Strategy

For maximum compatibility, configure multiple providers with fallback:

```tsx
<AIFormProvider
  providers={[
    // Chrome AI: Best for Chrome users
    { type: 'chrome', priority: 10 },
    
    // OpenAI: Universal fallback
    { 
      type: 'openai', 
      apiKey: process.env.REACT_APP_OPENAI_KEY || '',
      priority: 5 
    },
    
    // Custom server: Final fallback
    {
      type: 'custom',
      apiUrl: 'https://your-api.com',
      priority: 1
    }
  ]}
  fallbackOnError={true}
>
  <App />
</AIFormProvider>
```

**Fallback Behavior:**
- Chrome users: Use Chrome AI (free, fast, private)
- Other browsers: Automatically use OpenAI or Custom server
- No code changes needed

## Checking Browser Support

Programmatically check if Chrome AI is available:

```tsx
const { aiAvailability, refreshAvailability } = useForm();

useEffect(() => {
  refreshAvailability();
}, []);

if (!aiAvailability) {
  return <p>Checking AI availability...</p>;
}

if (aiAvailability.status === 'unavailable') {
  return (
    <div>
      <p>Chrome AI is not available in this browser.</p>
      <p>Using fallback AI provider...</p>
    </div>
  );
}

if (aiAvailability.available) {
  return <button onClick={() => aiAutofill()}>✨ AI Autofill</button>;
}
```

## Mobile Support

### Chrome Built-in AI
- ❌ Not available on mobile Chrome
- Use fallback providers for mobile users

### OpenAI Provider
- ✅ Works on all mobile browsers
- Requires internet connection

### Custom Server Provider
- ✅ Works on all mobile browsers
- Requires internet connection

## Recommended Setup

For best compatibility across all browsers and devices:

```tsx
<AIFormProvider
  providers={[
    // Try Chrome AI first (desktop Chrome only)
    { type: 'chrome', priority: 10 },
    
    // Fallback to OpenAI (works everywhere)
    { 
      type: 'openai', 
      apiKey: process.env.REACT_APP_OPENAI_KEY || '',
      model: 'gpt-3.5-turbo',
      priority: 5 
    }
  ]}
  fallbackOnError={true}
>
  <App />
</AIFormProvider>
```

This configuration:
- Uses free Chrome AI when available (desktop Chrome 127+)
- Automatically falls back to OpenAI for other browsers
- Works on mobile devices
- Provides consistent experience across all platforms

## Troubleshooting

### Chrome AI Not Available

**Problem:** `aiAvailability.status === 'unavailable'`

**Solutions:**
1. Update Chrome to version 127 or later
2. Enable AI features at `chrome://flags/#optimization-guide-on-device-model`
3. Restart Chrome
4. Check if you're using Chrome (not Edge or other Chromium browsers)

### Download Stuck

**Problem:** Download progress not increasing

**Solutions:**
1. Check internet connection
2. Keep the tab open and active
3. Try refreshing the page and starting again
4. Clear Chrome cache and try again

### CORS Errors with Custom Server

**Problem:** CORS errors when using custom server provider

**Solutions:**
1. Configure CORS headers on your server:
   ```
   Access-Control-Allow-Origin: *
   Access-Control-Allow-Methods: POST, GET, OPTIONS
   Access-Control-Allow-Headers: Content-Type, Authorization
   ```
2. Use a proxy if you can't modify server CORS settings
3. Test with a CORS-enabled endpoint first

## See Also

- [Provider Configuration](./PROVIDERS.md)
- [Getting Started](./GETTING_STARTED.md)
- [API Reference](./API_REFERENCE.md)
