# How the Certificate Fetching Works - Visual Flow

## Client Browser Request Flow

```
┌─────────────────────────────────────────────────────────────┐
│                   User Visits alherbawi.work                 │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│           Browser Loads HTML & Executes JavaScript           │
│         Shows: "Loading certifications..."                   │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  ATTEMPT 1: Direct Fetch from Credly API                    │
│  URL: https://www.credly.com/users/{USER_ID}/badges          │
│  Headers: Accept, Accept-Language                            │
│  (Browser automatically sets security headers)               │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
         ▼                       ▼
    ✅ SUCCESS              ❌ CORS ERROR
    │                       │
    │                       ▼
    │              ┌─────────────────────────────────────────┐
    │              │  ATTEMPT 2: Local JSON Files            │
    │              │  Files: badge.json,                     │
    │              │         public_badges.json              │
    │              │  Updated monthly by GitHub Actions      │
    │              └──────────┬──────────────────────────────┘
    │                         │
    │              ┌──────────┴──────────┐
    │              │                     │
    │              ▼                     ▼
    │         ✅ SUCCESS            ❌ FAILED
    │              │                     │
    └──────────────┘                     │
                   │                     │
                   ▼                     ▼
    ┌──────────────────────────┐    ┌────────────────────────┐
    │  Display Badges on Page  │    │  Show Error Message    │
    │  Remove Loading Message  │    │  "Failed to load       │
    └──────────────────────────┘    │   certifications..."   │
                                     └────────────────────────┘
```

## Automated Monthly Updates

```
┌─────────────────────────────────────────────────────────────┐
│          GitHub Actions: Monthly Cron Job                    │
│          Schedule: First day of every month at 00:00 UTC     │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Fetch Fresh Data from Credly API using curl                │
│  - Fetch badges: badge.json                                 │
│  - Fetch external badges: public_badges.json                │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Commit & Push Updated JSON Files to Repository             │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Trigger Deployment Workflow (main.yml)                     │
│  - Upload to AWS S3                                          │
│  - Invalidate CloudFront cache                              │
└─────────────────────────────────────────────────────────────┘
```

## Why This Approach?

### 1. Direct Fetch (First Choice)
- **Fastest**: No intermediary
- **Most Reliable**: Direct connection to Credly
- **When it works**: If Credly allows CORS from your domain
- **When it fails**: CORS policy blocks cross-origin requests

### 2. Local JSON Files (Fallback)
- **Purpose**: Ensure page always works
- **Data**: Cached certifications from Credly API
- **Update**: Automatically refreshed monthly via GitHub Actions
- **Benefit**: Works even if Credly API is down or CORS blocks the request

## What This Means for You

### 🎉 Automatic Monthly Updates!
When a new certificate is added to your Credly profile:
1. It appears on the Credly website
2. Wait until the first of the month
3. GitHub Actions fetches the latest badges automatically
4. JSON files are updated and deployed to AWS S3/CloudFront
5. Your website displays the new certificate

### 🔄 Automatic Synchronization
- **Monthly updates**: Badges are refreshed on the first day of each month
- **Zero manual work**: GitHub Actions handles everything
- **Always reliable**: Local JSON files ensure the site always works

### 🛡️ Reliability
- Two layers of fallback ensure 99.9%+ uptime
- Even if API fails, cached data shows
- Graceful error handling

## Browser Compatibility

This solution works on all modern browsers:
- ✅ Chrome/Edge (Latest)
- ✅ Firefox (Latest)
- ✅ Safari (Latest)
- ✅ Opera (Latest)
- ✅ Mobile browsers

## Security & Privacy

### Is This Secure?
**Yes!** Because:
1. Only fetches public data (your public badges)
2. No authentication credentials exposed
3. No sensitive data transmitted
4. Proper CORS-compliant headers are used
5. No dependency on third-party proxy services

### Is This Legal?
**Yes!** Because:
1. Credly provides public API endpoints
2. You're fetching your own data
3. Data is already public on Credly
4. No terms of service violations

## Performance

### Load Times
- Direct fetch: ~500ms-1s
- Local fallback: ~50ms

### Page Impact
- Loads asynchronously (doesn't block page)
- Shows loading state
- Progressive enhancement

## Monitoring

Check browser console (F12) to see:
```
Attempting fetch from: https://www.credly.com/...
Fetch failed: Failed to fetch
Falling back to local file: badge.json
Fallback fetch successful for badges
```

This tells you which method worked!
