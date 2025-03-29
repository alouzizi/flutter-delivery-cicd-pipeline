# Flutter CI/CD Workflow

A comprehensive CI/CD workflow configuration for Flutter applications using Codemagic. This repository provides ready-to-use YAML configuration files to automate building, testing, and deploying Flutter apps to TestFlight and other distribution channels.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
- [Configuration Options](#configuration-options)
- [Workflow Examples](#workflow-examples)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## 🔍 Overview

This CI/CD workflow automates the process of building and deploying Flutter applications using Codemagic. The repository contains separate workflows for iOS (TestFlight) and Android (Google Play) platforms, handling Flutter dependencies, code signing, building packages (IPA and APK/AAB), and publishing to their respective distribution channels.

## ✨ Features

- **Automated Flutter Builds**: Automatically builds your Flutter application for iOS
- **Code Signing**: Handles iOS code signing with provisioning profiles and certificates
- **App Store Connect Integration**: Publishes builds directly to TestFlight
- **Email Notifications**: Sends build status notifications via email
- **Customizable**: Easily adaptable to different Flutter projects
- **Multiple Environment Support**: Configure different workflows for dev, staging, and production

## 📝 Prerequisites

Before using this workflow, ensure you have:

1. A Flutter application repository on GitHub
2. An Apple Developer account with App Store Connect access
3. A Codemagic account linked to your GitHub repository
4. Necessary iOS provisioning profiles and certificates

## 🚀 Setup Instructions

### 1. Add the YAML file to your repository

Create a file named `codemagic.yaml` in the root of your Flutter project and copy the configuration from this repository.

### 2. Configure App Store Connect API Key

1. In App Store Connect, generate an API key with App Manager permissions
2. In Codemagic, go to Team Settings > Integrations > App Store Connect
3. Add a new integration named "AppStore_Distribution_API" with your API key details

### 3. Configure iOS Signing

1. In Codemagic, go to Team Settings > Code Signing > iOS
2. Upload your provisioning profile as "iOS_Distribution_Profile"
3. Upload your development certificate as "iOS_Distribution_Certificate"

### 4. Commit and Push

Commit the `codemagic.yaml` file and push it to your repository. Codemagic will detect the configuration file and set up the workflow.

## ⚙️ Configuration Options

### Workflow Structure

```yaml
workflows:
  testFlight:             # Workflow identifier
    name: "Flutter CI/CD"  # Display name
    integrations:         # External service connections
    environment:          # Build environment settings
    scripts:              # Build commands
    artifacts:            # Files to save from the build
    publishing:           # Distribution settings
```

### Environment Options

| Option | Description | Example |
|--------|-------------|---------|
| `flutter` | Flutter version to use | `3.27.1`, `stable`  |
| `xcode` | Xcode version for iOS builds | `15.0`, `Latest (16.2)` |
| `cocoapods` | CocoaPods version | `1.16.2`, `default` |

You can choose specific version numbers or version keywords (like `stable`, `latest`, `default`) based on your project requirements. Specifying versions explicitly helps ensure reproducible builds.

### Script Options

Common script steps include:

- Flutter package installation
- Running tests
- Building for different platforms
- Code signing
- Custom script execution

### Publishing Options

Available publishing channels:

- App Store Connect / TestFlight
- Google Play
- Email notifications
- Slack notifications
- GitHub releases
- Firebase App Distribution

## 📋 Simple Workflow Examples

### iOS TestFlight Workflow

```yaml
workflows:
  testFlight:
    name: "Ios Release"
    integrations:
      app_store_connect: Your-App-Store-API-Key-Name
    environment:
      flutter: stable
      ios_signing:
        provisioning_profiles:
          - Your_Provisioning_Profile_Ref
        certificates:
          - Your_Certificate_Ref
    scripts:
      - name: Set up code signing settings on Xcode project
        script: xcode-project use-profiles
      - name: Get Flutter packages
        script: flutter packages pub get
      - name: Set up code signing
        script: xcode-project use-profiles
      - name: Build IPA
            script: flutter build ipa --release \
            --export-options-plist=/Users/builder/export_options.plist
    artifacts:
      - build/ios/ipa/*.ipa
    publishing:
      email:
        recipients:
            - your-email@exemple.com
        notify:
          success: true
          failure: true
      app_store_connect:
        auth: integration

```

### Android Play Store Workflow

```yaml
workflows:
  android_release:
    name: "Android Release"
    instance_type: linux_x2
    environment:
      flutter: 3.27.1
      android_signing:
        - keystore_reference
    scripts:
      - name: Get Flutter packages
        script: flutter packages pub get
      - name: Run Flutter tests
        script: flutter test
      - name: Build APK and AAB
        script: |
          flutter build appbundle --release
          flutter build apk --release --split-per-abi
    artifacts:
      - build/**/outputs/**/*.aab
      - build/**/outputs/**/*.apk
    publishing:
      google_play:
        credentials: $GCLOUD_SERVICE_ACCOUNT_CREDENTIALS
        track: internal
```

### Complete Production Workflow

Check the [full-example.yaml](examples/full-example.yaml) file for a comprehensive production workflow setup.

## 🔧 Troubleshooting

### Common Issues

1. **Build Failures**
   - Check Flutter version compatibility
   - Verify code signing settings
   - Ensure all dependencies are properly specified

2. **TestFlight Upload Issues**
   - Verify App Store Connect API key permissions
   - Check app bundle ID matches the provisioning profile
   - Ensure the app version and build number are incremented

3. **Code Signing Problems**
   - Verify certificate and provisioning profile match
   - Check expiration dates
   - Ensure the correct team ID is specified

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
