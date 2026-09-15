# Carbyne-Web Dependency Audit Report

**Date:** December 13, 2025
**Auditor:** Claude (Automated Analysis)

## Executive Summary

This is a **static HTML website** with no package manager dependencies (no npm, pip, etc.). The site consists of 8 HTML files with inline CSS and no JavaScript. The main concerns are **bloat from embedded assets** and **CSS duplication**.

---

## 1. External Dependencies

### Google Fonts (External CDN)
| Font | Weight | Source |
|------|--------|--------|
| Share Tech Mono | 400 | fonts.googleapis.com |
| Orbitron | 400, 700 | fonts.googleapis.com |

**Current Implementation:**
```css
@import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700&display=swap');
```

**Status:** ✅ Current (no versioning issues with Google Fonts)

**Security Considerations:**
- ⚠️ **Privacy:** Google Fonts tracks users via cookies and referer headers
- ⚠️ **GDPR:** May require consent banner in EU jurisdictions
- ⚠️ **Availability:** Dependent on Google's CDN uptime

**Recommendations:**
1. **Self-host fonts** for privacy compliance and performance
   - Download WOFF2 files and serve locally
   - Eliminates third-party requests and tracking
   - Provides better caching control
2. Or use `font-display: swap` (already included) ✅

---

## 2. Bloat Analysis

### Critical Issue: Embedded Base64 Image

| File | Size | Issue |
|------|------|-------|
| `index.html` | **569 KB** | Contains ~550KB base64-encoded AVIF image |
| `carbyne_background.avif` | **419 KB** | Separate file, **unused** |

**Problem:** The background image is embedded as base64 directly in the HTML:
- Base64 encoding adds **~33% overhead** (419KB → ~560KB)
- Cannot be cached separately by browsers
- Increases initial page load time
- The separate AVIF file exists but is not referenced

**Recommendation:**
```html
<!-- Replace base64 data URL with file reference -->
background-image: url('carbyne_background.avif');
```

**Potential Savings:** ~150KB reduction in index.html

---

### CSS Duplication Across Pages

Each of the 8 HTML files contains **duplicate CSS** (~3-4KB each):

| File | CSS Size | Content Size |
|------|----------|--------------|
| index.html | 566 KB (includes image) | 569 KB |
| about.html | 3.7 KB | 9 KB |
| architecture.html | ~4 KB | 24 KB |
| failures.html | ~4 KB | 15 KB |
| philosophy.html | ~4 KB | 11 KB |
| performance.html | ~4 KB | 16 KB |
| roadmap.html | ~4 KB | 13 KB |
| kalic_case_study.html | ~4 KB | 18 KB |

**Total duplicated CSS:** ~25-30 KB across 8 files

**Recommendation:**
1. Extract common CSS to a shared `styles.css` file
2. Use `<link rel="stylesheet" href="styles.css">`
3. Browser can cache the CSS file across page navigations

---

## 3. Security Vulnerabilities

### Low Risk Items
| Issue | Severity | Description |
|-------|----------|-------------|
| No CSP headers | Low | No Content Security Policy (server config) |
| External font loading | Low | Third-party CDN dependency |

**No High/Critical Vulnerabilities Found**
- No JavaScript dependencies to audit
- No npm/yarn packages
- No server-side code
- No form submissions or user input handling

---

## 4. Recommended Changes

### Priority 1: Remove Base64 Bloat (High Impact)
**Savings: ~150KB on index.html**

Replace the inline base64 image with a file reference:
```css
/* Before - embedded base64 */
background-image: url('data:image/avif;base64,...');

/* After - external file */
background-image: url('carbyne_background.avif');
```

### Priority 2: Extract Shared CSS (Medium Impact)
**Savings: ~25KB total, improved caching**

1. Create `/styles.css` with common styles
2. Replace inline `<style>` blocks with:
   ```html
   <link rel="stylesheet" href="styles.css">
   ```

### Priority 3: Self-Host Fonts (Privacy/Performance)
**Impact: Privacy compliance, faster loading**

1. Download fonts from Google Fonts
2. Add to project:
   ```
   /fonts/ShareTechMono-Regular.woff2
   /fonts/Orbitron-Regular.woff2
   /fonts/Orbitron-Bold.woff2
   ```
3. Update CSS:
   ```css
   @font-face {
     font-family: 'Share Tech Mono';
     src: url('fonts/ShareTechMono-Regular.woff2') format('woff2');
     font-display: swap;
   }
   ```

### Priority 4: Delete Unused File (Cleanup)
If you implement Priority 1, keep `carbyne_background.avif`.
If not, consider if the separate file is needed.

---

## 5. Summary Table

| Category | Status | Action Required |
|----------|--------|-----------------|
| Outdated Packages | ✅ N/A | No package dependencies |
| Security Vulnerabilities | ✅ None | No issues found |
| Bloat - Base64 Image | ⚠️ High | Remove inline encoding |
| Bloat - CSS Duplication | ⚠️ Medium | Extract to shared file |
| Privacy - Google Fonts | ⚠️ Low | Consider self-hosting |

---

## Estimated Impact

| Change | File Size Reduction | Load Time Improvement |
|--------|--------------------|-----------------------|
| Remove base64 image | ~150 KB | ~0.5-1s on 3G |
| Extract CSS | ~25 KB | Improved caching |
| Self-host fonts | 0 (tradeoff) | Eliminate 1 DNS lookup |

**Total potential savings:** ~175 KB (~30% of current total)
