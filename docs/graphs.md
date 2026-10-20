# Flight Log Graphs

Plots of the main signals in a decoded flight log, each with a short explanation of what it shows. Time on every x-axis is seconds since the start of the log.

## Attitude

### Roll, pitch and yaw

![Attitude](../figs/attitude_plot.png)

Orientation of the vehicle in degrees. Roll is tilt side to side, pitch is tilt nose up/down, and yaw is the heading.

## Battery

### Voltage

![Battery voltage](../figs/battery_voltage.png)

Battery voltage and resting voltage (voltage corrected for load). A slow, steady drop is normal; sudden dips happen under heavy load.

### Current

![Battery current](../figs/battery_current.png)

Current drawn from the battery in amps. Spikes mean high motor load.

### Consumption

![Battery consumption](../figs/battery_consumption.png)

Total charge used (mAh, left axis) and energy used (Wh, right axis). Both only ever increase.

### Temperature

![Battery temperature](../figs/battery_temperature.png)

Battery temperature in °C.

### Remaining capacity

![Battery remaining](../figs/battery_remaining.png)

Estimated battery percentage left.

## GPS

### Ground track

![GPS track](../figs/gps_track.png)

Path of the vehicle (longitude vs latitude). Color shows time, with the green dot as the start and the red square as the end.

### Altitude

![GPS altitude](../figs/gps_altitude.png)

Altitude in metres as reported by GPS.

### Speed

![GPS speed](../figs/gps_speed.png)

Top: ground speed in m/s. Bottom: vertical speed in m/s.

### Quality

![GPS quality](../figs/gps_quality.png)

Number of satellites and horizontal dilution of precision. More satellites and a lower HDop mean a better position fix.

## IMU

The log contains several IMU sensors. Each graph draws one line per sensor so they can be compared; they should closely agree.

### Gyroscope

![IMU gyro](../figs/imu_gyro.png)

Angular rate about the X, Y and Z axes in rad/s.

### Accelerometer

![IMU accelerometer](../figs/imu_accel.png)

Acceleration along X, Y and Z in m/s². Z reads about -9.8 at rest because of gravity.

### Temperature

![IMU temperature](../figs/imu_temperature.png)

Temperature of each IMU sensor in °C.
