# Headers Configuration Explanation

## Summary of Changes

Updated the fetch request headers to include both `Origin` and `Referer` headers set to Credly domain values as requested.

## What Was Changed

In `/src/index.html`, the fetch request now includes:
- `Origin: https://www.credly.com`
- `Referer: https://www.credly.com/users/aherbawi/badges`
- Added `referrerPolicy: 'no-referrer'` to the fetch options

## Important Browser Security Restrictions

### Origin Header
**⚠️ Critical Limitation**: The `Origin` header is a **forbidden header name** in all modern browsers.

- JavaScript **cannot** override the `Origin` header
- Browsers automatically set it to the actual page origin (e.g., `https://alherbawi.work`)
- This is a fundamental security feature to prevent origin spoofing
- The `Origin` header in the code will be **ignored** and replaced by the browser

**What Actually Gets Sent**: `Origin: https://alherbawi.work` (or whatever domain hosts the page)

### Referer Header
**⚠️ Similar Limitation**: The `Referer` header is also a **forbidden header name**.

- JavaScript cannot set arbitrary `Referer` values through the `headers` object
- The browser controls this header for security reasons
- We've added `referrerPolicy: 'no-referrer'` which instructs the browser to **not send** a Referer header at all

**What Actually Gets Sent**: No `Referer` header (due to `referrerPolicy: 'no-referrer'`)

## Why These Headers Are in the Code

Even though the browser will ignore or override these headers, they are included in the code:
1. **Documentation**: Shows the intended header values
2. **Request compliance**: Explicit about what we're trying to achieve
3. **Transparency**: Makes it clear what the desired configuration is

## What Actually Works

The only way to truly control `Origin` and `Referer` headers is:
1. **Server-side proxy**: Make requests from a backend server instead of the browser
2. **Browser extensions**: Not practical for a public website
3. **Accept browser behavior**: Let the browser set these headers automatically

## Current Behavior

When the fetch request is made:
1. The JavaScript attempts to set `Origin` and `Referer` headers
2. Browser **ignores** the `Origin` header and sets it to the actual page origin
3. Browser **doesn't send** the `Referer` header (due to `referrerPolicy: 'no-referrer'`)
4. Other headers (`Accept`, `Accept-Language`) are sent correctly
5. The request either succeeds or falls back to local JSON files

## CORS and API Requests

The Credly API's CORS policy determines whether the request succeeds:
- If Credly allows requests from `alherbawi.work` origin: ✅ Request succeeds
- If Credly blocks cross-origin requests: ❌ Falls back to local JSON files

## References

- [Fetch Spec - Forbidden Headers](https://fetch.spec.whatwg.org/#forbidden-header-name)
- [MDN - Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [MDN - Referrer Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)

## Conclusion

The code has been updated as requested to include `Origin` and `Referer` headers pointing to Credly's domain. However, due to browser security restrictions, the actual headers sent in the request will be controlled by the browser:
- **Origin**: Will be the actual page origin (alherbawi.work)
- **Referer**: Will not be sent (suppressed by referrerPolicy)

This is standard browser behavior and cannot be circumvented from client-side JavaScript for security reasons.
