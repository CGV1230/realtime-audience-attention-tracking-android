# Audience Attention Estimation (Android Demo)

Real-time, on-device audience attention estimation using **CameraX** and **Google ML Kit Face Detection**.

This Android demo analyzes head pose, eye openness, and sustained behavior patterns to estimate audience engagement during presentations. The system runs fully on-device and does not store or transmit facial data.

## Overview

This project demonstrates a privacy-preserving attention estimation pipeline built with:

- **CameraX** for live video capture
- **ML Kit Face Detection** for facial analysis
- Custom tracking and scoring logic
- Calibration-based baseline modeling
- 1-minute rolling crowd aggregation

The goal is to provide a presenter with an objective engagement score without requiring cloud processing or biometric identification.

## Project Structure

```
app/
└── src/main/java/com/example/audienceattention/
    └── MainActivity.kt
```

All core logic is currently implemented inside **MainActivity.kt**, including:

### 1. UI Layer (*Jetpack Compose*)

- `CameraPreviewScreen()`
- Real-time camera preview
- Face overlay rendering
- Attention score display
- Calibration controls

### 2. `AudienceAnalyzer`

Handles:

- Frame analysis
- Face detection
- Tracking
- Attention estimation
- Crowd aggregation

### 3. `FaceTracker`

Maintains consistent IDs across frames:

- Uses ML Kit `trackingId`
- Falls back to IoU matching
- Removes stale tracks via TTL

### 4. `AttentionEstimatorWithCalibration`

Core scoring engine:

- 10-second calibration phase
- Baseline yaw/pitch/eye estimation
- Deviation scoring
- Sustained behavior penalties
- EMA smoothing per person

### 5. `CrowdAggregator`

- Aggregates per-person scores
- Maintains 60-second rolling window
- Outputs 1-minute attention score (0–100)
- Computes confidence metric

## Core Pipeline

1. **CameraX** captures frames.
2. **ML Kit** detects faces.
3. Faces are tracked across frames.
4. Each face receives:
   - Pose deviation score
   - Eye openness score
   - Sustained behavior penalties
   - EMA smoothing
5. Crowd attention is computed as mean score.
6. A 60-second rolling average produces final attention output.

## Required Libraries

Add the following dependencies to `app/build.gradle`:

### CameraX

```
implementation "androidx.camera:camera-camera2:1.3.1"
implementation "androidx.camera:camera-lifecycle:1.3.1"
implementation "androidx.camera:camera-view:1.3.1"
```

### ML Kit Face Detection

```
implementation "com.google.mlkit:face-detection:16.1.5"
```

### Jetpack Compose

```
implementation "androidx.compose.ui:ui:1.5.0"
implementation "androidx.compose.material3:material3:1.1.1"
implementation "androidx.lifecycle:lifecycle-runtime-compose:2.6.2"
```

### Lifecycle

```
implementation "androidx.lifecycle:lifecycle-runtime-ktx:2.6.2"
```

Make sure **Compose** is enabled in your project:

```
buildFeatures {
compose true
}
```

## Permissions

Add camera permission in `AndroidManifest.xml`:

```
<uses-permission android:name="android.permission.CAMERA" />
```

Camera permission is requested at runtime.

