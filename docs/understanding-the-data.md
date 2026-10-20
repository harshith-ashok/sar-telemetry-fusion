# Understanding the Flight Data

A guide to what each feature means, what it tells you, what you can do with it, where machine learning fits, and how an analysis workflow can be automated.

The data comes from an ArduPilot DataFlash flight log, decoded into one table per message type. This guide covers the four message types most useful for analysis: attitude, battery, GPS and IMU. All tables share a timestamp: time in microseconds since the flight controller booted. Subtract the first value and divide by 1e6 to get seconds.

---

## 1. What each feature means

### Attitude: how the vehicle is oriented

| Feature | Meaning | What it tells you |
|---------|---------|-------------------|
| `Roll`, `Pitch`, `Yaw` | Actual orientation (degrees) | How the vehicle is tilted and which way it faces |
| `DesRoll`, `DesPitch`, `DesYaw` | What the controller was *asking for* | The pilot's/autopilot's intent |
| `AEKF` | Which state estimator is active | Whether the navigation filter switched mid-flight |

**Key insight:** the gap between `Des*` and the actual value is the *tracking error*. A small error means the vehicle is flying as commanded; a large or oscillating error points to bad tuning, wind, or a mechanical problem.

### Battery: power system health

| Feature | Meaning | What it tells you |
|---------|---------|-------------------|
| `Volt` | Measured voltage (V) | Pack state; sags under load |
| `VoltR` | Resting voltage estimate (V) | Voltage with load sag removed, a better indicator of charge |
| `Curr` | Current draw (A) | How hard the motors are working |
| `CurrTot` | Consumed charge (mAh) | Total capacity used so far |
| `EnrgTot` | Consumed energy (Wh) | Total energy used so far |
| `Temp` | Temperature (°C) | Overheating risk |
| `Res` | Estimated internal resistance (Ω) | Pack ageing; rises as the battery degrades |
| `RemPct` | Remaining capacity (%) | Fuel gauge |
| `H`, `SH` | Health flags | Whether the monitor considers the battery healthy |
| `Inst` | Battery instance number | Which battery, if the vehicle has several |

**Key insight:** `Volt - VoltR` is proportional to `Curr × Res`. Tracking `Res` over many flights shows how fast a battery is ageing.

### GPS: where the vehicle is and how well it knows

| Feature | Meaning | What it tells you |
|---------|---------|-------------------|
| `Lat`, `Lng` | Position (degrees) | The flight path |
| `Alt` | Altitude (m) | Height above sea level, not above ground |
| `Spd` | Ground speed (m/s) | Horizontal velocity |
| `GCrs` | Ground course (°) | Direction of travel. Only meaningful while moving; noisy when stationary |
| `VZ` | Vertical speed (m/s) | Climb/descent rate |
| `NSats` | Satellites in use | Fix strength |
| `HDop` | Horizontal dilution of precision | Fix quality. Lower is better; below ~1 is good |
| `Status` | Fix type | 3 = 3D fix, 4 = DGPS, 5/6 = RTK |
| `GMS`, `GWk` | GPS time of week (ms) / week number | Absolute wall-clock time |
| `I` | GPS instance | Which receiver, if there are several |

**Key insight:** position is only as trustworthy as `NSats`, `HDop` and `Status`. Always check them before relying on a track.

### IMU: raw motion sensing

| Feature | Meaning | What it tells you |
|---------|---------|-------------------|
| `GyrX/Y/Z` | Angular rate (rad/s) | How fast the vehicle is rotating |
| `AccX/Y/Z` | Acceleration (m/s²) | Forces on the vehicle, including gravity |
| `T` | Sensor temperature (°C) | Thermal drift of the sensor |
| `I` | IMU instance (0, 1, 2) | Each row is one of several redundant sensors |
| `GH`, `AH` | Gyro / accel health flags | Sensor failures |
| `GHz`, `AHz` | Sample rate (Hz) | Logging/sensor rate |

**Key insight:** because there are several IMUs, a disagreement between them is a strong signal of a sensor fault or vibration.

---

## 2. What you can do with this data now (no ML needed)

| Goal | How |
|------|-----|
| Flight summary | Duration, max altitude, max speed, distance flown (sum of distances between GPS points), battery used |
| Battery health | Plot `Res` and `VoltR` per flight; flag high temperature or fast `RemPct` drop |
| Control quality | Compute RMS of `Roll - DesRoll` etc. Lower is better; compare after tuning |
| GPS quality report | Percentage of time with `NSats` < 8 or `HDop` > 2 |
| Vibration check | Standard deviation of `AccX/Y/Z`, or use the vibration message |
| Sensor agreement | Compare IMU instances; large differences mean a faulty sensor |
| Efficiency | Energy per metre flown: `EnrgTot` change / distance |
| Event timeline | Overlay the flight-mode, error and event messages on any plot to explain what happened and when |

Other useful message types: `RCOU` (motor outputs), `VIBE` (vibration), `MAG` (compass), `BARO` (barometer), `XKF*` (EKF internals), `ERR`/`MSG` (errors and messages).

---

## 3. Where ML could be applied

Start with simple, well-defined problems. Machine learning needs many flights to be useful.

| Problem | Type | Inputs | Notes |
|---------|------|--------|-------|
| Anomaly / fault detection | Unsupervised (Isolation Forest, autoencoder) | IMU, vibration, battery, motor outputs | Train on healthy flights; flag windows that look different. Best first project |
| Battery remaining-flight-time prediction | Regression | `Curr`, `VoltR`, `Temp`, speed, altitude | Predict time until `RemPct` hits a threshold |
| Battery degradation | Regression / trend | `Res`, `Temp` per flight | Predict when a pack needs replacing |
| Failure / crash prediction | Classification | Windowed features from all logs, labeled with `ERR` events | Needs many labeled failures |
| Flight-mode / manoeuvre recognition | Classification | `ATT`, `GPS` speed, `IMU` | Hover vs cruise vs climb, using flight mode as labels |
| Vibration / tuning analysis | Signal processing, then ML | FFT of `IMU` | Detect propeller imbalance or resonances |
| Dynamics / system identification | Regression | `Des*` and motor outputs → `Roll/Pitch/Yaw` | Learn a model of the vehicle for simulation or tuning |

**Practical tips**
- Resample everything to a common time grid; the logs run at different rates (IMU is much faster than GPS).
- Work with sliding windows (for example 1-5 s) and summarise each one: mean, std, min, max, FFT peak.
- Split train/test **by flight**, not by random rows, or you will leak information.
- Normalise per feature and keep the instance (`I`) columns separate or aggregated.

---

## 4. How to automate the process

The pipeline is: **flight log → decode → tables → clean → features → plots / report / model**.

Suggested stages:

1. **Script the plots.** Wrap each graph in a function that takes a folder of decoded tables and writes the figures.
2. **Summarise each flight.** Compute the summary metrics from section 2 for every flight and store them in a machine-readable summary.
3. **Process in batches.** Run the same steps over a whole folder of logs, giving each flight its own output and adding one row per flight to a combined table. That table becomes the training set for any model.
4. **Run it automatically.** Trigger processing when a new log arrives, either by watching a folder on a schedule or from an upload form.
5. **Generate a report.** Render the figures and summary into a single document per flight.
6. **Add checks.** Fail loudly if a log is empty, has no GPS fix or is very short, so bad data does not silently reach a model.

A sensible order: (1) script the plots, (2) add summary metrics per flight, (3) batch a folder, (4) collect more flights, (5) try anomaly detection.
