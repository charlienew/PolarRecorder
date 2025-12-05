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

- Polar BLE SDK (for Bluetooth communication with Polar devices)
- Jetpack Compose (UI framework)
- Navigation Compose (navigation)
- ViewModel (state management)
- MQTT client library (for streaming data)

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
