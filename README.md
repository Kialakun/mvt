# MU Logger — Raw Motion + GPS Data Logger

**MU Logger** is a web-based data acquisition tool that records raw sensor data from mobile devices. It captures accelerometer, gyroscope, and GPS readings in real time, stores them locally in IndexedDB, and exports the complete dataset as CSV for offline analysis.

Built for researchers, engineers, and hobbyists who need **unfiltered** sensor data without any onboard filtering or derived computations.

---

## Features

- **Real‑time sensor monitoring** — displays accelerometer (with gravity), linear acceleration, rotation rate, and GPS fields live.
- **Raw‑only logging** — stores exactly what the hardware provides; no Kalman filters, no fusion, no smoothing.
- **Inertial‑frame acceleration** — transforms linear acceleration into the global (inertial) frame using the device's orientation matrix.
- **GPS metadata** — logs latitude, longitude, horizontal accuracy (68% confidence radius), altitude, speed, and heading.
- **Local storage** — all data is saved to an IndexedDB database; no network transmission.
- **CSV export** — download all logged records as a single CSV file for use in spreadsheets or analysis tools.
- **Clear database** — wipe all stored records with a single tap.
- **Mobile‑optimised** — responsive UI with large touch targets and a dark theme for outdoor use.

---

## How It Works

The app uses three core browser APIs:

| API | Purpose |
|-----|---------|
| `DeviceMotionEvent` | Provides accelerometer (with gravity), linear acceleration, and rotation rate (gyroscope). |
| `DeviceOrientationEvent` | Provides absolute orientation angles (alpha, beta, gamma) used to transform acceleration into the inertial frame. |
| `Geolocation API` | Provides GPS position, accuracy, altitude, speed, and heading. |

### Data Pipeline

1. **Orientation** — the device's orientation angles are captured from `deviceorientation`.
2. **Rotation Matrix** — a 3×3 rotation matrix is built from the Euler angles (α, β, γ).
3. **Inertial Acceleration** — the linear acceleration vector is multiplied by the rotation matrix to obtain acceleration in the global (inertial) frame.
4. **Logging** — all values (raw + inertial) are timestamped and stored in IndexedDB.
5. **GPS** — position data is merged with each motion sample if available.

---

## Data Schema (CSV Export)

| Column | Description |
|--------|-------------|
| `timestamp_ms` | Unix timestamp in milliseconds (when the sample was captured) |
| `acc_x, acc_y, acc_z` | Acceleration including gravity (m/s²) — device frame |
| `lin_x, lin_y, lin_z` | Linear acceleration (m/s²) — device frame |
| `iner_x, iner_y, iner_z` | Linear acceleration (m/s²) — **inertial (global) frame** |
| `rot_alpha, rot_beta, rot_gamma` | Rotation rate (rad/s) — gyroscope |
| `lat, lon` | GPS latitude / longitude (decimal degrees) |
| `gps_acc` | GPS horizontal accuracy (metres, 68% confidence radius) |
| `gps_alt_acc` | GPS altitude accuracy (metres) |
| `compass` | Compass heading (degrees, 0–360) |
| `comp_acc` | Compass accuracy (if available) |
| `mot_int` | Motion intensity (placeholder for future use) |

---

## Getting Started

### 1. Open the App
- Serve the HTML file from any web server (local or remote).
- Open it in a **mobile browser** (Chrome, Safari, Firefox).
- For best results, use a device with **hardware sensors** (smartphone / tablet).

### 2. Grant Permissions
On first launch, the app will request:
- **Motion & orientation** permissions (iOS requires explicit user consent).
- **Location** permissions (GPS).

### 3. Start Recording
Tap **START RECORDING** to begin logging. The status dot will turn red and pulse.

### 4. Stop & Export
- Tap **STOP** to end the session.
- Tap **EXPORT CSV** to download all records as a `log_<timestamp>.csv` file.

### 5. Clear Data
Use **CLEAR DATABASE** to remove all stored records (prompts for confirmation).

---

## Technical Notes

### Permissions (iOS)
On iOS 13+, `DeviceMotionEvent` and `DeviceOrientationEvent` require explicit permission via `requestPermission()`. The app handles this automatically.

### GPS Accuracy
The accuracy value displayed is the **horizontal error radius** at 68% confidence (1‑sigma). A lower value indicates a more precise fix.

### Orientation API Fallback
The app uses `deviceorientationabsolute` if available (absolute heading relative to true north). Falls back to `deviceorientation` otherwise.

### No Network Calls
All data stays on the device. No external servers are contacted.

---

## Limitations

- **Requires HTTPS** — sensor APIs and Geolocation are only available over secure contexts.
- **GPS only on mobile** — desktop browsers typically lack GPS hardware.
- **Orientation accuracy** — depends on device calibration; magnetometer interference may affect heading.

---

## Use Cases

- **Vehicle dynamics logging** — capture acceleration and GPS for route profiling.
- **Human motion analysis** — record gait, step, or gesture data.
- **Navigation development** — combine raw IMU + GPS for fusion algorithm testing.
- **Educational demos** — visualise sensor behaviour in real time.

---

## Development

The entire application is a **single HTML file** with embedded CSS and JavaScript. No build tools or dependencies are required.

To modify or extend:
1. Open the file in any text editor.
2. Adjust the UI layout in the `<style>` section.
3. Extend the data schema in the `handleMotionEvent` function.
4. Update the CSV export to include new columns.

---

## License

This project is provided as‑is under the **MIT License**. Feel free to use, modify, and distribute it for personal or commercial purposes.

---

## About

MU Logger was designed for situations where **raw, unprocessed sensor data** is more valuable than filtered or fused outputs. By logging exactly what the hardware reports, it enables custom sensor‑fusion pipelines, offline calibration, and transparent data auditing.

---

## Acknowledgements

- Built with the Web Sensors API, IndexedDB, and the Geolocation API.
- Inspired by the need for simple, transparent motion logging in field research.

---

**Happy logging!** 🚀
