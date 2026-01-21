# Certification Badge Gallery Update

## Overview
The website displays certifications from Credly's API with automatic monthly updates via GitHub Actions. The browser attempts to fetch badges in real-time, with local JSON files as a fallback.

## How It Works

### Automated Monthly Updates

A GitHub Actions workflow (`update-badges.yml`) runs on the first day of every month at 00:00 UTC to:

1. **Fetch Latest Badge Data**
   - Uses curl with proper headers to fetch data from Credly's API
   - URLs:
     - Regular badges: `https://www.credly.com/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/badges?page=1&page_size=48`
     - External badges: `https://www.credly.com/api/v1/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/external_badges/open_badges/public?page=1&page_size=48`

2. **Update JSON Files**
   - Saves responses to `src/badge.json` and `src/public_badges.json`

3. **Commit and Push**
   - Commits updated files to the repository
   - Push triggers the main deployment workflow

4. **Deploy to AWS**
   - Main workflow uploads files to S3
   - Invalidates CloudFront cache for immediate updates

### Client-Side Fetching Strategy

The browser uses a two-tier approach:

1. **Direct API Call (Primary)**
   - First, the browser attempts to fetch directly from Credly's API endpoints
   - Headers sent:
     - Accept: application/json
     - Accept-Language: en-US,en;q=0.9
   - Note: Browser automatically sets security headers

2. **Local JSON Fallback (Secondary)**
   - If direct fetch fails due to CORS restrictions or network issues, falls back to local cached JSON files
   - Files: `badge.json` and `public_badges.json`
   - These files are updated monthly by GitHub Actions

### User Experience Flow

```
User visits alherbawi.work
    ↓
Page loads with "Loading certifications..." message
    ↓
JavaScript attempts to fetch from Credly API with proper headers
    ↓
├─ SUCCESS → Displays latest certifications in real-time
│             No manual update needed!
└─ FAILURE → Falls back to cached JSON files
              Shows previously cached certifications
```

### Technical Implementation

The implementation includes:

- **Duplicate Prevention**: Tracks loaded badge IDs to prevent duplicates
- **Error Handling**: Graceful degradation with multiple fallback options
- **Loading States**: Shows loading message while fetching data
- **Console Logging**: Detailed logs for debugging and monitoring
- **Accessibility**: Alt text for images, proper link attributes

## Benefits

1. **Automatic Monthly Updates**: GitHub Actions fetches latest certifications on the first of every month
2. **Zero Manual Work**: No need to manually download and upload JSON files
3. **Real-time Browser Fetching**: Browser attempts to fetch fresh data on every page load
4. **Reliability**: Multiple fallback options ensure the page always works
5. **Performance**: Efficient single-page load with async data fetching
6. **Maintainability**: Set it and forget it - fully automated

## CORS and Security Considerations

### What is CORS?
Cross-Origin Resource Sharing (CORS) is a security feature that restricts web pages from making requests to a different domain than the one serving the page. This prevents malicious websites from accessing sensitive data.

### How Does This Implementation Handle CORS?
The implementation attempts to fetch directly from Credly's API. If Credly's API allows the request, the badges load in real-time. If CORS restrictions prevent the fetch, the implementation gracefully falls back to local JSON files that are automatically updated monthly.

### Is This Secure?
Yes, this approach is secure because:
- We're only fetching public data (publicly visible badges)
- No authentication credentials are exposed
- Local JSON files serve as a reliable backup
- GitHub Actions uses server-side curl (not affected by browser CORS)

## Monitoring and Maintenance

### GitHub Actions Workflow
The workflow runs automatically on the first day of each month. You can also:
- **Manual Trigger**: Run the workflow manually from the Actions tab
- **View Logs**: Check workflow run logs to verify successful updates
- **Monitor Status**: GitHub Actions will notify you of any failures

### Browser Console Logs
The client-side implementation logs all fetch attempts:
- Direct fetch attempts and results
- Fallback to local files
- Any errors encountered

### Manual Updates (Optional)
The monthly cron job handles updates automatically. If you need to update badges immediately:
1. Go to GitHub Actions tab in your repository
2. Select "Update Credly Badges Monthly" workflow
3. Click "Run workflow" button
4. Select the main branch and run

## Troubleshooting

If badges don't load from the API:
- Check browser console for CORS errors
- Verify that local JSON files (badge.json and public_badges.json) are present and up to date
- The fallback mechanism ensures badges always display from local files when API fetch fails
- Check GitHub Actions workflow runs to ensure monthly updates are working

## Future Enhancements

Potential improvements:
- Add pagination support for users with more than 48 badges
- Implement caching with localStorage for faster subsequent loads
- Add retry logic with exponential backoff
- Display badge details on hover/click
- Filter/sort badges by date, issuer, or type
- Increase update frequency if needed (weekly, daily, etc.)
