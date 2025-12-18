# Claude Code Context - Polar Recorder

## Project Overview

Polar Recorder is an open-source Android application designed for researchers, developers, and enthusiasts to capture raw biometric signals from Polar devices. The app enables recording, storing, and streaming of physiological data such as ECG, PPG, and heart rate from Polar sensors.

## Tech Stack

- **Language**: Kotlin
- **UI Framework**: Jetpack Compose
- **Architecture**: MVVM (Model-View-ViewModel)
- **Navigation**: Jetpack Navigation Compose
- **Build System**: Gradle with Kotlin DSL
- **Target Platform**: Android

## Project Structure

```
app/
├── app/src/main/java/com/wboelens/polarrecorder/
│   ├── MainActivity.kt                    # Main entry point with navigation setup
│   ├── dataSavers/                        # Data persistence layer
│   │   ├── DataSaver.kt                   # Base interface for data savers
│   │   ├── DataSavers.kt                  # Manager for multiple data savers
│   │   ├── FileSystemDataSaver.kt         # Saves data to local filesystem
│   │   └── MQTTDataSaver.kt              # Streams data via MQTT
│   ├── managers/                          # Business logic managers
│   │   ├── PermissionManager.kt           # Handles Android permissions
│   │   ├── PolarManager.kt                # Manages Polar device connections
│   │   ├── PreferencesManager.kt          # Handles app settings/preferences
│   │   └── RecordingManager.kt            # Controls recording sessions
│   ├── services/
│   │   └── RecordingService.kt            # Background recording service
│   ├── ui/                                # UI layer
│   │   ├── components/                    # Reusable UI components
│   │   ├── dialogs/                       # Dialog components
│   │   ├── screens/                       # Screen composables
│   │   └── theme/                         # App theming
│   └── viewModels/                        # ViewModels for UI state management
├── build.gradle.kts
└── privacy_policy.md
```

## Key Features

1. **Device Management**: Scan, connect, and configure Polar devices (H10, H7, OH1+)
2. **Data Recording**: Capture ECG, PPG, heart rate, and other biosignals
3. **Flexible Storage**: Save to local files or stream via MQTT
4. **Auto-reconnect**: Maintains connection and continues recording on failures
5. **Privacy-focused**: All processing happens on-device, no external data transmission

## Navigation Flow

The app uses a linear navigation flow:
1. **deviceSelection** - Scan and select Polar devices
2. **deviceConnection** - Connect to selected devices
3. **deviceSettings** - Configure device-specific settings
4. **recordingSettings** - Configure recording and data saver options
5. **dataSaverInitialization** - Initialize selected data savers
6. **recording** - Active recording screen

## Important Architectural Patterns

- **Manager Classes**: Business logic is separated into manager classes (PolarManager, RecordingManager, etc.)
- **ViewModels**: UI state is managed through ViewModels following MVVM pattern
- **Composables**: UI is built with Jetpack Compose declarative UI
- **Data Savers**: Pluggable architecture for different data persistence strategies

## Development Guidelines

### Code Style
- Formatted with ktfmt
- See [.editorconfig](app/.editorconfig) for formatting rules
- Optimized imports enabled

### Privacy & Security
- App does NOT collect telemetry or analytics
- All data processing is local to the device
- Files saved unencrypted - users responsible for securing sensitive health data
- No third-party SDKs for analytics or crash reporting

### Testing
- Test files located in `app/src/test/` and `app/src/androidTest/`
- Example: [LogViewModelTest.kt](app/app/src/test/java/com/wboelens/polarrecorder/viewModels/LogViewModelTest.kt)

## Key Dependencies

- **Polar BLE SDK 6.13.0** (for Bluetooth communication with Polar devices)
  - Supports: Polar Loop, Polar 360, H10, H9, H7, Verity Sense, OH1, OH1+, Ignite 3, Vantage V3, Vantage M3, Grit X2 Pro, Grit X2, Pacer, Pacer Pro
- Jetpack Compose (UI framework)
- Navigation Compose (navigation)
- ViewModel (state management)
- MQTT client library (for streaming data)
- RxJava3 (reactive programming for SDK integration)

## Building & Running

```bash
# Build the project
./gradlew build

# Install on connected device
./gradlew installDebug
```

## Important Notes

- **Not affiliated with Polar**: Independent open-source project
- **Research purposes**: Intended for research and development, not medical use
- **GDPR compliance**: Biosignals are sensitive health data - app is designed for full user control
- **Watch support**: Polar watches require specific setup (see Polar SDK documentation)

## Recent Updates (December 2025)

### Polar Loop Support Enhancements

The following changes were made to add full support for the Polar Loop device:

#### 1. Polar BLE SDK Update (6.2.0 → 6.13.0)

**File:** `gradle/libs.versions.toml`
**Line:** 21

```toml
# Changed from: polarBleSdk = "6.2.0"
polarBleSdk = "6.13.0"
```

**Reason:** Polar Loop and Polar 360 devices require SDK version 6.4.0+ for proper support. Version 6.13.0 includes:

- Official Polar Loop and Polar 360 device support
- Protocol improvements for newer devices
- Bug fixes for device capability detection
- Split offline skin temperature measurement support
- Training session deletion support
- Faster offline listing
- No breaking changes affecting this app's functionality

#### 2. Minimum SDK Version Update (24 → 26)

**File:** `app/build.gradle.kts`
**Line:** 26

```kotlin
// Changed from: minSdk = 24
minSdk = 26
```

**Reason:** Polar BLE SDK 6.12.0 requires Android 8.0 (API 26) or higher. This is a reasonable requirement:

- Android 8.0 was released in 2017
- Over 95% of active Android devices run Android 8.0+
- Needed for improved Bluetooth LE functionality

#### 3. PPG Display Fix

**File:** `app/src/main/java/com/wboelens/polarrecorder/managers/RecordingManager.kt`
**Line:** 37

```kotlin
// Changed from:
PolarBleApi.PolarDeviceDataType.PPG ->
    (data as PolarPpgData).samples.lastOrNull()?.channelSamples?.lastOrNull()?.toFloat()

// Changed to:
PolarBleApi.PolarDeviceDataType.PPG ->
    (data as PolarPpgData).samples.lastOrNull()?.channelSamples?.firstOrNull()?.toFloat()
```

**Reason:** Fixed UI display issue where the ambient light sensor value (channel 2, always 3) was shown instead of the main PPG signal (channel 0). This was only a display bug - all 3 PPG channels were always recorded correctly in the data files.

**PPG Channel Mapping:**

- Channel 0: Main PPG signal (~89,000-90,000 range)
- Channel 1: Secondary PPG signal (~17,000-18,000 range)
- Channel 2: Ambient light sensor (constant value: 3)

### Polar Loop Device Capabilities

Based on testing and SDK documentation, the Polar Loop supports:

**✅ Supported in SDK Mode (Raw Data Recording):**

- PPG (Photoplethysmography) - 22Hz, 24-bit, 3 channels
- ACC (Accelerometer) - 50Hz, 16-bit, 8G range
- Skin Temperature - 1Hz, 32-bit, 1 channel

**❌ Not Supported in SDK Mode:**

- HR (Heart Rate) streaming - SDK Mode disables HR streaming
- PPI (RR Intervals) streaming - SDK Mode disables PPI streaming

**Note:** HR and PPI can be extracted from raw PPG data through post-processing using peak detection algorithms.

### Testing Results

Tested with Polar Loop Gen 2 (device ID: 11455734):

- ✅ Device detection and connection working
- ✅ PPG data streaming at 22Hz successfully
- ✅ ACC data streaming at 50Hz successfully
- ✅ Skin temperature streaming at 1Hz successfully
- ✅ All 3 PPG channels recorded correctly in JSONL format
- ✅ UI now displays correct PPG values (~89K range)
- ✅ Auto-reconnect functionality working
- ℹ️ HR/PPI not available in SDK Mode (expected behavior)

## December 2025 Updates - SDK 6.13.0 and Debugging Enhancements

### Polar BLE SDK Update (6.12.0 → 6.13.0)

**File:** `gradle/libs.versions.toml`
**Line:** 21

```toml
# Changed from: polarBleSdk = "6.12.0"
polarBleSdk = "6.13.0"
```

**Reason:** Updated to latest SDK version (released December 16, 2024). Version 6.13.0 includes:

- Training session deletion support
- Faster offline listing
- Exercise status notifications
- Split offline skin temperature measurement support
- General bug fixes and improvements

### Enhanced Connection Debugging

Added comprehensive logging throughout the connection flow to help diagnose device discovery and connection issues.

#### 1. Feature Polling with Extended Timeout

**File:** `app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt`
**Lines:** 131-166

```kotlin
// Poll for FEATURE_DEVICE_INFO readiness with longer timeout
var waitTime = 0L
val maxWaitTime = 5000L // 5 seconds max wait
val pollInterval = 500L // Check every 500ms

while (waitTime < maxWaitTime) {
  kotlinx.coroutines.delay(pollInterval)
  waitTime += pollInterval

  val readyFeatures = deviceFeatureReadiness[polarDeviceInfo.deviceId]
  if (readyFeatures?.contains(PolarBleApi.PolarBleSdkFeature.FEATURE_DEVICE_INFO) == true ||
      readyFeatures?.contains(PolarBleApi.PolarBleSdkFeature.FEATURE_POLAR_ONLINE_STREAMING) == true) {
    logViewModel.addLogMessage("Key features detected after ${waitTime}ms")
    break
  }
}
```

**Reason:** Changed from fixed 1-second delay to intelligent polling up to 5 seconds. This accommodates devices with newer firmware that may take longer to signal feature readiness.

#### 2. Device Discovery Logging

**File:** `app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt`
**Lines:** 411-442

Added detailed logging for device scanning:

- Shows when scans start/stop
- Logs each discovered device with name and ID
- Counts total devices found per scan
- Helps identify BLE scanning issues

#### 3. Enhanced Capability Fetching Logs

Added detailed logging throughout the capability detection process:

- Feature readiness checks
- Retry attempts with count
- Fallback method usage
- Success/failure for each step

### Troubleshooting: Bluetooth Connection Conflicts

**Common Issue:** Device not discoverable during scanning despite being powered on.

**Root Cause:** Bluetooth connection conflict with Polar Flow app or other Polar apps. When Flow maintains an active/reserved connection, it prevents the device from advertising to other apps during BLE scanning.

**Solution:**

- Uninstall or disable Bluetooth permission for Polar Flow
- Disconnect device from Flow before using Polar Recorder
- Only run one Polar app at a time

**Documentation:** Added comprehensive troubleshooting section to [README.md](README.md) explaining this limitation and providing multiple solutions.

### Skin Temperature Recording Support

**Date:** December 18, 2025

Added full support for skin temperature recording from Polar Loop and other devices that report `SKIN_TEMPERATURE` capability.

#### The Issue

The Polar Loop (firmware 4.0.7) reports `SKIN_TEMPERATURE` as an available data type, but the app only supported the generic `TEMPERATURE` type. This caused crashes when attempting to record skin temperature because:

1. `SKIN_TEMPERATURE` wasn't in the list of supported data types for settings requests
2. Empty settings `{}` were returned instead of proper configuration
3. The streaming API failed when starting with invalid settings

#### The Solution

**Files Modified:**

- [PolarManager.kt](app/app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt)
- [RecordingManager.kt](app/app/src/main/java/com/wboelens/polarrecorder/managers/RecordingManager.kt)
- [DeviceSettingsDialog.kt](app/app/src/main/java/com/wboelens/polarrecorder/ui/dialogs/DeviceSettingsDialog.kt)

**Key Changes:**

1. **Added SKIN_TEMPERATURE to settings request** ([PolarManager.kt:577](app/app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt#L577))

   ```kotlin
   PolarDeviceDataType.TEMPERATURE,
   PolarDeviceDataType.SKIN_TEMPERATURE -> {
     // Request settings from device
   }
   ```

2. **Added streaming support** ([PolarManager.kt:683-686](app/app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt#L683-L686))

   ```kotlin
   PolarDeviceDataType.SKIN_TEMPERATURE -> {
     Log.d(TAG, "Starting SKIN_TEMPERATURE streaming for $deviceId")
     api.startSkinTemperatureStreaming(deviceId, sensorSettings)
   }
   ```

3. **Added data processing** ([RecordingManager.kt:49-50, 309-310](app/app/src/main/java/com/wboelens/polarrecorder/managers/RecordingManager.kt#L49-L50))

   ```kotlin
   // Both TEMPERATURE and SKIN_TEMPERATURE use PolarTemperatureData
   PolarBleApi.PolarDeviceDataType.SKIN_TEMPERATURE ->
       (data as PolarTemperatureData).samples.lastOrNull()?.temperature
   ```

4. **Added UI display name** ([DeviceSettingsDialog.kt:565](app/app/src/main/java/com/wboelens/polarrecorder/ui/dialogs/DeviceSettingsDialog.kt#L565))

   ```kotlin
   PolarDeviceDataType.SKIN_TEMPERATURE -> "Skin Temperature"
   ```

#### Important Technical Details

**Data Type Difference:**

- **`TEMPERATURE`**: Generic temperature streaming (older devices, some firmware versions)
- **`SKIN_TEMPERATURE`**: Specific skin temperature streaming (Polar Loop firmware 2.0.0+, Polar 360)

**Shared Data Structure:**

Both `startTemperatureStreaming()` and `startSkinTemperatureStreaming()` return the same data type: `PolarTemperatureData`. This is documented in [Polar SDK GitHub issue #656](https://github.com/polarofficial/polar-ble-sdk/issues/656).

**Polar Loop Configuration:**

- Sample rate: 1 Hz (1 sample per second)
- Resolution: 32-bit
- Channels: 1

**Known Limitation:**

The Polar Loop does not support `requestFullStreamSettings()` for SKIN_TEMPERATURE, which generates a `PolarOperationNotSupported` error. This is expected and handled gracefully by the code using `onErrorReturn { PolarSensorSetting(emptyMap()) }`. The available settings are sufficient for streaming.

#### Verification

Verified with Polar Loop Gen 2 (firmware 4.0.7):

- ✅ Settings request: `{SAMPLE_RATE=[1], RESOLUTION=[32], CHANNELS=[1]}`
- ✅ Streaming starts successfully
- ✅ Data received at 1 Hz
- ✅ Data saved to JSONL files correctly
- ✅ No crashes or errors during recording

## Related Files

- [README.md](README.md) - User-facing documentation
- [privacy_policy.md](app/privacy_policy.md) - Privacy policy details
- [CITATION.cff](CITATION.cff) - Citation information for research
- [code_examples/](code_examples/) - Sample code for processing recorded data

## Common Tasks

### Adding a new data saver

1. Create new class implementing `DataSaver` interface in [dataSavers/](app/app/src/main/java/com/wboelens/polarrecorder/dataSavers/)
2. Register in [DataSavers.kt](app/app/src/main/java/com/wboelens/polarrecorder/dataSavers/DataSavers.kt)
3. Add settings dialog in [ui/dialogs/](app/app/src/main/java/com/wboelens/polarrecorder/ui/dialogs/)
4. Update [RecordingSettingsScreen.kt](app/app/src/main/java/com/wboelens/polarrecorder/ui/screens/RecordingSettingsScreen.kt)

### Adding a new screen

1. Create composable in [ui/screens/](app/app/src/main/java/com/wboelens/polarrecorder/ui/screens/)
2. Add route in [MainActivity.kt](app/app/src/main/java/com/wboelens/polarrecorder/MainActivity.kt) NavHost
3. Create ViewModel if needed in [viewModels/](app/app/src/main/java/com/wboelens/polarrecorder/viewModels/)

### Modifying device settings

- Edit [PolarManager.kt](app/app/src/main/java/com/wboelens/polarrecorder/managers/PolarManager.kt) for Polar SDK interactions
- Update [DeviceViewModel.kt](app/app/src/main/java/com/wboelens/polarrecorder/viewModels/DeviceViewModel.kt) for state management
- Modify [DeviceSettingsScreen.kt](app/app/src/main/java/com/wboelens/polarrecorder/ui/screens/DeviceSettingsScreen.kt) for UI
