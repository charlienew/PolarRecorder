# Polar Recorder

Polar Recorder is an open-source application designed for researchers, developers, and enthusiasts who need to capture raw biometric signals from Polar devices. It provides a simple way to record, store, and stream physiological data.

## Features

- **Records Raw Data** – Capture signals such as ECG, PPG, and heart rate from Polar sensors
- **Supports All Polar Devices** – Compatible with all Polar hardware capable of streaming raw data (tested with H10, H7, and OH1+)
- **Flexible Data Storage** – Save recordings to a file for offline analysis
- **Live Data Streaming** – Transmit data in real-time via MQTT
- **Resilient** – Auto-reconnects on connection failures and continues the recording
- **Fully Open Source** – Developed with transparency and collaboration in mind

## Installing

Polar Recorder is available on the [Play Store](https://play.google.com/store/apps/details?id=com.wboelens.polarrecorder). You can also download the APK directly from the [Releases](https://github.com/boelensman1/polarrecorder/releases) section and install it manually on your device.

## Using the app with Polar Watches
The app supports data streaming from Polar watches, however, specific setup steps are required. See [the official Polar documentation](https://github.com/polarofficial/polar-ble-sdk/blob/master/documentation/UsingSDKWithWatches.md#step-by-step-how-to) for step-by-step setup instructions.

## Code Examples

The `code_examples/` directory contains sample code for processing recorded data in Python and R. More examples will be added over time, contributions are welcome!

## Troubleshooting

### Device Not Showing Up During Scan

If your Polar device is not being discovered during Bluetooth scanning, the most common cause is a **Bluetooth connection conflict with the Polar Flow app** (or other Polar apps).

**Symptoms:**

- Device powers on and appears to be working
- Other Polar apps can connect to the device
- Polar Recorder cannot find the device during scanning
- Other Polar devices (like H10) may work fine

**Root Cause:**

When the Polar Flow app is installed and has previously connected to your device, it may maintain an active or reserved Bluetooth connection. This prevents the device from advertising itself to other apps during BLE scanning, making it invisible to Polar Recorder.

**Solutions (choose one):**

1. **Temporarily Uninstall Polar Flow** (quickest solution)
   - Uninstall the Polar Flow app
   - Open Polar Recorder and scan for devices
   - Your device should now be discoverable
   - You can reinstall Flow later if needed

2. **Disable Bluetooth Permission for Flow**
   - Go to Android Settings → Apps → Polar Flow
   - Disable the Bluetooth permission
   - Open Polar Recorder and scan
   - Re-enable the permission when you want to use Flow again

3. **Disconnect Device from Flow**
   - Open the Polar Flow app
   - Disconnect or unpair your Polar device
   - Force close the Flow app (from Android's recent apps)
   - Wait a few seconds
   - Open Polar Recorder and scan

4. **Ensure Only One App is Running**
   - Close Polar Flow completely (swipe it away from recent apps)
   - Wait 5-10 seconds for Bluetooth to release
   - Open Polar Recorder and scan

**Note:** This is a limitation of Bluetooth LE on Android, not a bug in either app. Only one app can maintain an active connection to a Bluetooth device at a time.

### Connection Issues After Firmware Updates

If you experience connection issues after updating your device firmware:

1. Ensure you're using the latest version of Polar Recorder
2. Try the solutions listed above for connection conflicts
3. Restart your device by turning it off and on
4. Restart Bluetooth on your phone (toggle it off and on)
5. If issues persist, please report them on the [GitHub Issues](https://github.com/boelensman1/polarrecorder/issues) page with:
   - Device model
   - New firmware version
   - Logs from the connection attempt (use a logcat app)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Citing This Project

If you use Polar Recorder in your research, please cite it using the information provided in the repository's `citation.cff` file. You can cite this project directly from GitHub by:

1. Navigating to the repository's main page
2. Clicking on the "Cite this repository" button in the sidebar
3. Using either the APA or BibTeX format provided

Alternatively, you can generate citations in various formats using tools that support the Citation File Format (CFF) standard.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Disclaimer

Polar Recorder is an independent open-source project and is not affiliated with, endorsed by, or officially connected to Polar Electro or any of its products. "Polar" and associated device names are trademarks of their respective owners. This app is intended for research and development purposes only and comes with no guarantees regarding data accuracy or medical reliability.
