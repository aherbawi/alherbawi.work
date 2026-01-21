# Quick Start Guide - Automated Certification System

## What Changed?

Your website now has **automated monthly badge updates** via GitHub Actions! 

🎉 **No more manual JSON file updates needed!**

## How to Use

### For Normal Operation
**Nothing to do!** The system automatically updates your badges monthly.

### When You Add a New Certificate
1. Earn a new certification on Credly
2. Make sure it's set to "Public" on your Credly profile
3. Wait until the first day of the next month (automated update)
4. Your website will automatically display the new certificate!

Or if you need immediate updates:
1. Go to GitHub Actions tab
2. Select "Update Credly Badges Monthly" workflow
3. Click "Run workflow" to trigger manually

## How to Verify It's Working

### Check GitHub Actions
1. Go to your GitHub repository
2. Click on "Actions" tab
3. Look for "Update Credly Badges Monthly" workflow
4. Check the last run status and logs

### Check Your Website
1. Visit your website: https://alherbawi.work
2. Open browser console (Press F12)
3. Reload the page (Ctrl+R or Cmd+R)
4. Look for messages like:
   ```
   Attempting fetch from: https://www.credly.com/...
   Fetch successful for badges
   ```
   Or if the direct fetch fails:
   ```
   Attempting fetch from: https://www.credly.com/...
   Fetch failed: Failed to fetch
   Falling back to local file: badge.json
   Fallback fetch successful for badges
   ```
5. You'll see which method successfully loaded your badges

## Two Ways Your Badges Load

### 1. Direct from Credly (First Attempt - Real-time)
✅ Fastest
✅ Most up-to-date
✅ No intermediary

### 2. From Local Files (Fallback - Updated Monthly)
✅ Always works
✅ Shows last cached version (updated monthly by GitHub Actions)
✅ Instant loading
✅ Used when direct fetch fails

## File Structure

```
alherbawi.work/
├── .github/
│   └── workflows/
│       ├── main.yml             ← Deploys to AWS S3/CloudFront
│       └── update-badges.yml    ← NEW: Monthly cron job
├── src/
│   ├── index.html               ← Updated with fetching logic
│   ├── badge.json               ← Auto-updated monthly by GitHub Actions
│   └── public_badges.json       ← Auto-updated monthly by GitHub Actions
├── CERTIFICATION_UPDATE.md      ← Full technical documentation
├── HOW_IT_WORKS.md              ← Visual flow and explanation
└── QUICKSTART.md                ← This file
```

## Do I Still Need badge.json and public_badges.json?

**Yes, they're essential!** These files:
- Are automatically updated monthly by GitHub Actions
- Serve as fallback when direct Credly API fetch fails
- Ensure badges always display even during Credly outages
- Are kept fresh by the automated workflow

## Troubleshooting

### Badges not loading?
1. Check browser console for errors
2. Verify your internet connection
3. Check if Credly.com is accessible
4. Clear browser cache and reload
5. Check that badge.json files exist in src/ directory

### New certificate not showing immediately?
1. Verify it's marked as "Public" on Credly
2. Badges update on the first of every month
3. For immediate update: Trigger workflow manually on GitHub Actions
4. Or wait for direct browser fetch to succeed (attempts on every page load)

### GitHub Actions workflow not running?
1. Check Actions tab for any errors
2. Verify workflow file exists: `.github/workflows/update-badges.yml`
3. Check workflow run history and logs
4. Ensure repository has write permissions for GitHub Actions

## Updating Fallback Files (Automated)

**Good news**: GitHub Actions handles this automatically!

The workflow runs monthly and:
1. Fetches latest badge data from Credly API using curl
2. Updates `badge.json` and `public_badges.json`
3. Commits and pushes changes
4. Triggers deployment to AWS S3/CloudFront

### Manual Update (If Needed)
To trigger an immediate update:
1. Go to GitHub repository → Actions tab
2. Select "Update Credly Badges Monthly"
3. Click "Run workflow" button
4. Wait for completion (~1-2 minutes)

## Benefits Summary

| Before | After |
|--------|-------|
| Manual JSON downloads | ✅ Automated monthly updates |
| Update every time | ✅ Zero maintenance |
| Outdated badges | ✅ Always fresh (monthly) |
| Multiple manual steps | ✅ Set and forget |
| Risk of forgetting | ✅ Automatic cron job |

## Support

- **Technical Documentation**: See `CERTIFICATION_UPDATE.md`
- **Visual Guide**: See `HOW_IT_WORKS.md`
- **Workflow File**: See `.github/workflows/update-badges.yml`

## What's Next?

Nothing! The system runs automatically. Just focus on earning new certifications! 🚀

---

**Last Updated**: January 2026
**Implementation**: Automated Monthly Updates via GitHub Actions Cron Job
