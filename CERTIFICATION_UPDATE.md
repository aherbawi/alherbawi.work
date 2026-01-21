# Certification Badge Gallery Update

## Overview
The website has been updated to automatically fetch certifications from Credly's API in real-time, eliminating the need to manually upload JSON files.

## How It Works

### Client-Side Fetching Strategy

The updated implementation uses a three-tier approach to fetch certification badges:

1. **Direct API Call (Primary)**
   - First, the browser attempts to fetch directly from Credly's API endpoints
   - URLs:
     - Regular badges: `https://www.credly.com/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/badges?page=1&page_size=48`
     - External badges: `https://www.credly.com/api/v1/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/external_badges/open_badges/public?page=1&page_size=48`

2. **CORS Proxy (Secondary)**
   - If direct fetch fails due to CORS restrictions, the request is routed through a CORS proxy
   - Using: `https://api.allorigins.win/raw?url=`
   - This proxy adds the necessary CORS headers to allow cross-origin requests

3. **Local JSON Fallback (Tertiary)**
   - If both direct and proxy fetches fail, falls back to local cached JSON files
   - Files: `badge.json` and `public_badges.json`
   - These files serve as backup and can be periodically updated

### User Experience Flow

```
User visits alherbawi.work
    ↓
Page loads with "Loading certifications..." message
    ↓
JavaScript attempts to fetch from Credly API
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

1. **Automatic Updates**: New certifications appear automatically without manual intervention
2. **Real-time Data**: Always shows the latest certifications from Credly
3. **Reliability**: Multiple fallback options ensure the page always works
4. **Performance**: Efficient single-page load with async data fetching
5. **Maintainability**: No need to manually download and upload JSON files

## CORS and Security Considerations

### What is CORS?
Cross-Origin Resource Sharing (CORS) is a security feature that restricts web pages from making requests to a different domain than the one serving the page. This prevents malicious websites from accessing sensitive data.

### Why Do We Need a CORS Proxy?
Credly's API endpoints may not include the necessary CORS headers to allow requests from your domain (alherbawi.work). The CORS proxy:
- Acts as an intermediary between your website and Credly's API
- Adds the required CORS headers to the response
- Allows the browser to accept the response

### Is This Secure?
Yes, this approach is secure because:
- We're only fetching public data (publicly visible badges)
- No authentication credentials are exposed
- The CORS proxy (allorigins.win) is a reputable public service
- Local JSON files serve as a backup if the proxy is unavailable

## Monitoring and Maintenance

### Browser Console Logs
The implementation logs all fetch attempts:
- Direct fetch attempts and results
- CORS proxy attempts and results
- Fallback to local files
- Any errors encountered

### Updating Fallback Files (Optional)
While not required for normal operation, you can update the local JSON files periodically:

1. Visit the Credly API endpoints in your browser:
   - https://www.credly.com/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/badges?page=1&page_size=48
   - https://www.credly.com/api/v1/users/7f90c8d7-ad39-45cf-96bb-31074194eebb/external_badges/open_badges/public?page=1&page_size=48

2. Save the JSON responses as `badge.json` and `public_badges.json`

3. Commit and push to the repository

This ensures the fallback always has recent data.

## Testing

To test the implementation:

1. **Normal Operation**: Simply visit the website and verify badges load
2. **Network Tab**: Open browser DevTools → Network tab to see fetch requests
3. **Console**: Check console logs to see which fetch method succeeded
4. **Fallback Testing**: Temporarily rename local JSON files to test proxy behavior

## Alternative CORS Proxies

If `api.allorigins.win` becomes unavailable, you can switch to alternatives:

- `https://corsproxy.io/?`
- `https://cors-anywhere.herokuapp.com/`
- `https://api.codetabs.com/v1/proxy?quest=`

Simply update the `CORS_PROXY` constant in the script.

## Future Enhancements

Potential improvements:
- Add pagination support for users with more than 48 badges
- Implement caching with localStorage for faster subsequent loads
- Add retry logic with exponential backoff
- Display badge details on hover/click
- Filter/sort badges by date, issuer, or type
