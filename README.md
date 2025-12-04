# Carbyne.exe

PhiVector development chronicle website.

## Files

- `index.html` - Main newsletter page with sidebar navigation
- `kalic_case_study.html` - Article: Teaching an AI to Remember
- `carbyne_background.avif` - Background image (409 KB)

## Deployment

Currently deployed to TinySite. Background is base64 embedded in index.html for single-file deployment.

To use external AVIF instead (smaller HTML):
1. Update CSS background-image to `url('carbyne_background.avif')`
2. Deploy both files

## Adding Articles

1. Create new HTML file following kalic_case_study.html structure
2. Add nav link in index.html under "Articles" section
3. Commit and deploy
