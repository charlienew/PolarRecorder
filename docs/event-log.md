# Event Log

Mark timestamped events during a recording session, then edit their labels afterward. Events are saved for all connected devices.

## Usage

1. Start a recording
2. Press **Mark Event** to capture a timestamp — the label defaults to "Event N"
3. Tap any event (or the edit icon) to rename it
4. Events persist when recording stops

## Data Format

Events are stored in `EVENT_LOG.jsonl` alongside other data files (ECG, PPG, HR, etc.), one per device.

Each line is a JSON object following the standard data saver format:

```json
{"phoneTimestamp":1773939851447,"deviceId":"08502D33","recordingName":"...","dataType":"EVENT_LOG","data":[{"index":1,"timestamp":1773939851447,"label":"start hand movements"}]}
```

| Field | Description |
|-------|-------------|
| `index` | Sequential event number (1-based) |
| `timestamp` | Unix epoch ms when the event was marked |
| `label` | User-editable description |

The file is **append-only**. Editing a label appends a new line with the same `index` and `timestamp` but the updated `label`. When recording stops, a final snapshot of all events is appended. To reconstruct the current state, take the **last entry per index**.

## Implementation

**Core logic** — `RecordingOrchestrator`:
- `_eventLogEntries: MutableStateFlow<List<EventLogEntry>>` holds in-memory state
- `addEvent()` creates an entry with the current timestamp and saves it immediately
- `updateEventLabel()` updates the in-memory list and appends the updated entry to all data savers
- `stopRecording()` calls `saveAllEventLogEntries()` to write a final snapshot before disposing streams
- Events are cleared on `startRecording()`

**Service layer** — `RecordingService` / `RecordingServiceConnection` expose `eventLogEntries`, `addEvent()`, and `updateEventLabel()` through the binder pattern, same as other recording state.

**UI** — `EventLogSection` (component) + `EditEventLabelDialog` (dialog):
- "Mark Event" button with flag icon shown during recording
- Event list shows label + elapsed time (MM:SS or H:MM:SS relative to recording start)
- Tapping an event opens an edit dialog
- Uses `Column` + `forEach` (not `LazyColumn`) to avoid nested scroll conflicts with the parent scrollable column

**Data saver integration** — `EVENT_LOG` is registered as a data type in `DataSaverInitializationScreen`, so each device gets a dedicated `EVENT_LOG.jsonl` file created during initialization. Events are saved to all enabled data savers (FileSystem, MQTT) for all connected devices.
