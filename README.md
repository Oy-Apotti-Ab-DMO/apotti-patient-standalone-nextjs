# Apotti Patient Standalone App (Next.js)

This project is a standalone patient-facing application that allows patients to enter and view their observations. It interacts with a FHIR server to retrieve and create patient observations using the SMART on FHIR protocol. This application is built using Next.js with TypeScript.

## Table of Contents
- [Features](#features)
- [Technologies](#technologies)
- [Setup](#setup)
- [Environment Variables](#environment-variables)
- [Running the Application](#running-the-application)
- [PWA & Mobile (Android / iOS) Support](#pwa--mobile-android--ios-support)
  - [1. PWA Structure & Web App Manifest](#1-pwa-structure--web-app-manifest)
  - [2. Building Android TWA App (.apk / .aab)](#2-building-android-twa-app-apk--aab)
  - [3. Running & Testing on Local Android Emulator](#3-running--testing-on-local-android-emulator)
  - [4. Testing on iOS (iPhone / iPad)](#4-testing-on-ios-iphone--ipad)
- [File Structure](#file-structure)
- [Patient Data Handling](#patient-data-handling)
- [License](#license)

## Features
- **Patient Data Display**: Displays patient information (Name, ID, Date of Birth, Address) retrieved from the FHIR server.
- **Observations Display**: Shows the patient's observations in a table, sorted by the `effectiveDateTime` in descending order.
- **Observation Creation**: Allows patients to create new observations by filling out a form.
- **SMART on FHIR Authentication**: Utilizes the SMART on FHIR protocol for OAuth2 authentication.

## Technologies
- **Next.js** (v13+)
- **React** (v18+)
- **TypeScript** (for type safety)
- **CSS Modules** (for styling)
- **Axios** (for data fetching)
- **FHIR** (Fast Healthcare Interoperability Resources)

## Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/Oy-Apotti-Ab/apotti-patient-standalone.git

2.  Navigate to the project directory:
cd apotti-patient-standalone

3. Install the dependencies:
npm install

## Environment Variables
Before running the application, ensure you have the following environment variables set in the .env.local file:
NEXT_PUBLIC_FHIR_SERVER: FHIR server base URL
NEXT_PUBLIC_FHIR_SERVER_A: FHIR server OAuth2 base URL (e.g. https://gw.apottiekosysteemi.fi/Interconnect-EKO01/oauth2)
NEXT_PUBLIC_CLIENT_ID: Your client ID for OAuth2 authentication

## Running the Application
To start the development server, run:
```bash
npm run dev
# or
bun dev
```

This will start the application on http://localhost:3000.

## PWA & Mobile (Android / iOS) Support

This application functions as a **Progressive Web App (PWA)** and can be installed as a native-feeling standalone application on both Android and iOS devices.

### 1. PWA Structure & Web App Manifest
* **Manifest (`app/manifest.ts`)**: Defines application metadata, colors (`theme_color`, `background_color`), and icon paths.
* **Icons (`public/icon-192x192.png`, `public/icon-512x512.png`)**: High-resolution icons for home screens and adaptive launcher icons.
* **Digital Asset Links (`public/.well-known/assetlinks.json`)**: Domain verification file required by Android Trusted Web Activities (TWA) to run full-screen without Chrome URL bars.

### 2. Building Android TWA App (.apk / .aab)

Google's [Bubblewrap CLI](https://github.com/GoogleChromeLabs/bubblewrap) packages the PWA into a native Android `.apk` (for local testing) and `.aab` (for Google Play Store):

1. **Initialize Bubblewrap**:
   ```bash
   bunx @bubblewrap/cli init --manifest=https://your-domain.vercel.app/manifest.webmanifest
   ```
2. **Extract SHA-256 Fingerprint**:
   ```bash
   keytool -list -v -keystore android.keystore -alias android
   ```
   Paste the generated `SHA256:` fingerprint into `public/.well-known/assetlinks.json`.
3. **Build the Android APK**:
   ```bash
   bunx @bubblewrap/cli build
   ```
   * Output files: `app-release-signed.apk` and `app-release-bundle.aab`.

### 3. Running & Testing on Local Android Emulator

To test OAuth2 authentication and PWA functionality locally without cloud limits:

1. **Increase Linux `inotify` limits** (prevents `EMFILE` watch errors with Android Studio):
   ```bash
   sudo sysctl fs.inotify.max_user_watches=524288
   sudo sysctl fs.inotify.max_user_instances=512
   ```
2. **Start the Android Virtual Device (AVD)** in Android Studio.
3. **Start local dev server**:
   ```bash
   bun dev
   ```
4. **Forward port 3000 into the emulator**:
   ```bash
   ~/Android/Sdk/platform-tools/adb reverse tcp:3000 tcp:3000
   ```
5. **Open in Emulator Chrome**: Navigate to `http://localhost:3000` and tap **Sign In**.
6. **Install Standalone Mode**: Tap Chrome's menu (`⋮`) -> **Add to Home screen** / **Install app**.

### 4. Testing on iOS (iPhone / iPad)

1. Open Safari on your iOS device.
2. Navigate to your deployed HTTPS URL (e.g. `https://your-domain.vercel.app`).
3. Tap the **Share** button -> tap **Add to Home Screen**.
4. The app launches directly from the home screen in full-screen standalone mode.

## File Structure

```plaintext
├── app
│   ├── api
│   │   ├── auth
│   │   │   └── callback
│   │   │       └── route.ts       # Dynamic callback route for OAuth2
│   │   └── observation
│   │       └── [patientId]
│   │           └── route.ts       # API route to fetch patient observations
│   ├── components
│   │   └── PatientObservationForm.tsx  # Observation form component
│   ├── manifest.ts                # Next.js App Router Web App Manifest
│   ├── patient
│   │   └── [patientId]
│   │       └── page.tsx           # Patient details and observation listing
│   ├── page.tsx                   # Homepage with OAuth2 sign-in
│   └── styles
│       ├── HomePage.css           # CSS for the homepage
│       └── PatientPage.css        # CSS for the patient info and observations page
├── public
│   ├── .well-known
│   │   └── assetlinks.json        # Digital Asset Links for Android TWA
│   ├── icon-192x192.png           # 192x192 PWA app icon
│   ├── icon-512x512.png           # 512x512 PWA app icon
│   └── apottilabs.png             # Logo used in the app
├── .env                           # Environment variables
├── package.json                   # Dependencies and scripts
└── README.md                      # Project documentation
```

## Patient Data Handling
Patient Info
The patient’s information is displayed after successful authentication and fetching of patient data from the FHIR server. It includes:

- Name
- ID
- Date of Birth
- Address
- 
Observations:. The patient’s observations are fetched from the FHIR server and displayed in a table. Observations are sorted by effectiveDateTime in descending order. Each observation includes:

    - Observation Code
    - Value
    - Unit
    - DateTime
## License
This project is licensed under the MIT License.
