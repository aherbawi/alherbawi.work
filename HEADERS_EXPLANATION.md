# Headers Configuration Explanation

## Summary of Changes

The previous approach of setting `Origin` and `Referer` headers in browser JavaScript did not work due to browser security restrictions. The new implementation uses a GitHub Actions cron job with server-side curl to fetch badge data monthly.

## What Was Changed

### Old Approach (Deprecated)
- Attempted to set `Origin` and `Referer` headers in client-side JavaScript
- These headers are **forbidden headers** and were ignored by browsers
- This approach could not bypass CORS restrictions

### New Approach (Current)
- **GitHub Actions Workflow**: Monthly cron job runs on the 1st of each month
- **Server-side curl**: Fetches data with proper headers (not affected by browser CORS)
- **Automated updates**: JSON files updated and deployed automatically
- **Client-side fallback**: Browser attempts direct fetch, falls back to updated JSON files

## Why the Old Approach Didn't Work

### Browser Security Restrictions

Both `Origin` and `Referer` are **forbidden header names** in all modern browsers:

- JavaScript **cannot** override these headers
- Browsers automatically set them to the actual page origin
- This is a fundamental security feature to prevent origin spoofing
- Any attempt to set them in fetch() is **silently ignored**

## Current Implementation

### Server-Side (GitHub Actions)
```bash
curl -X GET $URL \
  -H "accept: application/json" \
  -H "accept-language: en-US,en;q=0.9" \
  -H "user-agent: Mozilla/5.0 ..." \
  # ... all headers work as expected in server-side curl
```

**Benefits**:
- ✅ Not affected by browser CORS restrictions
- ✅ All headers are sent as specified
- ✅ Reliable monthly updates
- ✅ Automated deployment

### Client-Side (Browser)
```javascript
fetch('https://www.credly.com/users/{USER_ID}/badges', {
  headers: {
    'Accept': 'application/json',
    'Accept-Language': 'en-US,en;q=0.9'
  }
})
```

**Fallback**:
```javascript
fetch('badge.json') // Updated monthly by GitHub Actions
```

## Workflow Overview

1. **Monthly Cron Job** (1st of month at 00:00 UTC)
   - GitHub Actions runs curl commands
   - Fetches fresh data from Credly API
   - Updates badge.json and public_badges.json

2. **Automatic Deployment**
   - Commits updated files to repository
   - Triggers main.yml workflow
   - Deploys to AWS S3
   - Invalidates CloudFront cache

3. **Client-Side Loading**
   - Browser attempts direct API fetch
   - If fails, falls back to monthly-updated JSON files
   - Always reliable with fresh data

## References

- [Fetch Spec - Forbidden Headers](https://fetch.spec.whatwg.org/#forbidden-header-name)
- [MDN - Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [GitHub Actions - Schedule](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule)

## Conclusion

The browser-based header approach was replaced with a robust server-side solution using GitHub Actions. This eliminates CORS issues and provides reliable automated updates. The monthly cron job ensures badge data is always fresh while maintaining a client-side fallback for real-time attempts.
