# 📱 App Update Notification System

## Overview
This app includes an automatic update notification system using GitHub Releases API for seamless update delivery.

## 🎯 Current Implementation

### GitHub Releases Integration
- ✅ Uses GitHub Releases API for version checking
- ✅ Automatic APK detection from release assets
- ✅ Release notes from GitHub markdown
- ✅ No external server required
- ✅ Secure HTTPS API calls

---

## 🚀 Setup Guide

### Step 1: Configure Repository Information
Edit `lib/core/services/update_notification_service.dart`:

```dart
// Lines 14-15: Replace with your GitHub repository details
static const String _githubOwner = 'your-github-username';
static const String _githubRepo = 'reval';
```

### Step 2: Create GitHub Releases
When you want to publish an update:

1. **Build your APK**:
   ```bash
   flutter build apk --release
   ```

2. **Create a GitHub Release**:
   - Go to your repository on GitHub
   - Click "Releases" → "Create a new release"
   - Tag version: `v1.2.0` (use semantic versioning)
   - Release title: `Version 1.2.0 - Feature Update`
   - Description: Write release notes in markdown
   - **Attach the APK file** to the release
   - Publish the release

3. **Users get notified**: The app automatically checks for updates and shows the dialog

---

## 📋 Release Process

### Creating Proper Releases

1. **Version Tagging**: Use format `v1.2.3`
2. **Release Title**: Descriptive name like "Version 1.2.3 - Bug Fixes"
3. **Release Notes**: Use markdown format:
   ```markdown
   ## What's New
   - Added employee consumption tracking
   - Fixed profit calculation issues
   - Enhanced monthly reports
   
   ## Bug Fixes
   - Resolved inventory discrepancy warnings
   - Fixed database migration issues
   ```

4. **APK Upload**: Always attach the `app-release.apk` file

### Example Release Workflow
```bash
# Build the release APK
flutter build apk --release

# The APK will be at: build/app/outputs/flutter-apk/app-release.apk
# Upload this file when creating the GitHub release
```

---

## 🔧 Configuration Options

### Update Behavior
- **Automatic checks**: On app startup (2-second delay)
- **Version comparison**: Semantic versioning (1.2.3 format)
- **Force updates**: Major version changes (1.x.x → 2.x.x)
- **Skip prereleases**: Draft and prerelease versions are ignored

### Update Dialog Features
- ✅ Release name as title
- ✅ Formatted release notes (markdown cleaned up)
- ✅ Version comparison display
- ✅ Force update warnings for major versions
- ✅ Direct download from GitHub
- ✅ Localized in Indonesian

---

## 🌐 Deployment Automation

### GitHub Actions Workflow
Create `.github/workflows/release.yml`:

```yaml
name: Build and Release APK

on:
  push:
    tags:
      - 'v*'

jobs:
  build-and-release:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Java
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '11'
        
    - name: Setup Flutter
      uses: subosito/flutter-action@v2
      with:
        flutter-version: '3.19.0'
        channel: 'stable'
        
    - name: Get dependencies
      run: flutter pub get
      
    - name: Build APK
      run: flutter build apk --release
      
    - name: Create Release
      uses: softprops/action-gh-release@v1
      with:
        files: build/app/outputs/flutter-apk/app-release.apk
        body_path: CHANGELOG.md  # Optional: use changelog file
        draft: false
        prerelease: false
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Automatic Deployment Flow
1. **Tag your code**: `git tag v1.2.0 && git push origin v1.2.0`
2. **GitHub Actions triggers**: Builds APK automatically
3. **Release created**: APK uploaded to GitHub releases
4. **Users notified**: Next app launch shows update dialog

---

## 🛡️ Security & Best Practices

### Security Features
- **HTTPS API calls**: All GitHub API requests use HTTPS
- **APK integrity**: Downloads directly from GitHub (trusted source)
- **Rate limiting**: Built-in 10-second timeout for API calls
- **Error handling**: Graceful failures, app continues normally

### Best Practices
1. **Semantic Versioning**: Always use Major.Minor.Patch format
2. **Clear Release Notes**: Users see these in the update dialog
3. **Test Before Release**: Test APK before publishing
4. **Gradual Rollout**: For major updates, consider user feedback
5. **Backup Strategy**: Keep previous working versions available

---

## 📱 User Experience

### Update Dialog Flow
1. **App launches** → 2-second delay → **Check GitHub API**
2. **If update available** → **Show update dialog**
3. **User options**:
   - **"Nanti"** (Later): Skip for now, check again next launch
   - **"Unduh Update"**: Download APK from GitHub

### Update Types
- **Minor Updates** (1.1.0 → 1.2.0): Optional, user can postpone
- **Major Updates** (1.x.x → 2.0.0): Strongly recommended, warning shown
- **Patch Updates** (1.1.0 → 1.1.1): Optional, for bug fixes

---

## 🔧 Customization

### Modifying Update Behavior
In `update_notification_service.dart`:

```dart
// Change force update logic (line 112)
bool _isMajorVersionDifference(String newVersion, String currentVersion) {
  // Current: force update for major version changes
  // Modify this logic as needed
}

// Change release notes formatting (line 92)
String _formatReleaseNotes(String releaseBody) {
  // Customize markdown cleanup and formatting
}
```

### Styling the Update Dialog
The dialog uses Material Design 3 theming and can be customized:
- Colors follow app theme
- Responsive layout for different screen sizes
- Professional appearance with GitHub branding

---

## 🛠️ Testing

### Manual Testing
1. **Lower app version**: Temporarily change version in `pubspec.yaml`
2. **Create test release**: Publish with higher version number
3. **Launch app**: Update dialog should appear
4. **Test download**: Verify APK downloads correctly

### Debug Mode
The system includes console logging for debugging:
- API request/response status
- Version comparison results
- Error messages for troubleshooting

---

## 🆘 Troubleshooting

### Common Issues

1. **No update dialog showing**:
   - Check GitHub repository configuration
   - Verify release has APK attached
   - Ensure app version is lower than release version

2. **API rate limiting**:
   - GitHub API allows 60 requests/hour for unauthenticated requests
   - App checks once per launch, should not hit limits

3. **APK download issues**:
   - Verify APK file is properly attached to release
   - Check user's internet connection
   - Ensure APK is not corrupted

4. **Version comparison problems**:
   - Use semantic versioning (1.2.3)
   - Ensure release tags start with 'v' (v1.2.3)

### Debug Information
Check console logs for:
```
GitHub API request failed with status: XXX
No APK file found in latest release
Error checking for update: [error details]
```

---

## 📞 Migration from Firebase

If you were previously using Firebase Remote Config:

### What Changed
- ✅ **Removed**: Firebase dependencies and configuration
- ✅ **Added**: GitHub Releases API integration
- ✅ **Improved**: No server maintenance required
- ✅ **Enhanced**: Automatic release notes from GitHub

### Benefits of GitHub Integration
- **No Backend Required**: GitHub handles everything
- **Version Control Integration**: Releases tied to code versions
- **Professional Workflow**: Standard software release process
- **Cost Effective**: No Firebase costs or limits
- **Reliable**: GitHub's 99.9% uptime SLA

---

## 🎯 Production Ready

The GitHub-based update system is production-ready with:
- ✅ Error handling and graceful failures
- ✅ Professional user interface
- ✅ Automatic version detection
- ✅ Secure download process
- ✅ No external dependencies beyond GitHub

Simply configure your repository details and start publishing releases!