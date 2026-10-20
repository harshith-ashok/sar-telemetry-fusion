# Journey (RGB + thermal fusion)

The **Journey** page replays a drone flight over paired RGB and thermal frames. For each frame it detects people in both modalities, combines the results, attaches the drone's state at that moment, and has an agent write a conclusion.

## Layout

| Quadrant | Content |
|----------|---------|
| Top left | **Drone view**: the RGB frame with the detected people drawn on it (optional ground truth) |
| Top right | **Thermal / RGB**: the two modalities superimposed with an adjustable blend, or each shown alone |
| Bottom left | **Conclusion**: the analyst agent's assessment of the current frame, with spawn, respawn and cancel controls |
| Bottom right | **JSON**: the structured capture record for the frame, with a copy button |

Box colours show agreement: green for people seen by both sensors, blue for RGB only, orange for thermal only.

## Frame sources

- **Paired dataset.** Aligned RGB and thermal frames from the KAIST multispectral dataset, stepped through with the controls in the header or sampled at random.
- **Video.** A video can be loaded as another source, either as a single side-by-side video (RGB on the left, thermal on the right) or as separate aligned RGB and thermal videos. Frames are sampled at a chosen rate, after which a video behaves like the dataset. A **Play** button advances through the frames automatically.

## Telemetry

Each frame carries a drone state: position, altitude, speed, heading, attitude, battery, GPS quality and flight mode. The imagery and the flight log were not recorded together, so the flight is replayed across the frame sequence. The telemetry illustrates the data path and is not a real geolocation of what the camera saw.

## Detection and fusion

**Detect & analyse** examines the RGB and thermal frames independently with the same blind detector used on the Search & Rescue page. The two sets of detections are then compared:

- a person found in both modalities becomes a single **confirmed** detection with higher confidence;
- a person found by only one sensor is kept and marked as single-sensor with lower confidence.

Confidence indicates agreement; it is not a calibrated probability.

## Capture record

The JSON view shows the record produced for each finding: the frame, the drone's telemetry, how many people each sensor found, and the fused detections with their sources and confidence. It is the unit shared with the analyst agent and the operator console.

## Analyst agent

After detection, an agent reads the capture record and writes a short conclusion: what was found and how sure, roughly where it is relative to the drone's view, and what the operator should do next. It weighs two-sensor evidence more heavily than single-sensor evidence and is told not to state anything the record does not support. It can be respawned or cancelled, and it only advises.

## Limitations

- The imagery comes from a different source than the flight data, so telemetry is simulated.
- Small or distant people are often found by only one sensor, and fusion is a heuristic.
- Detection results can vary between runs.

Which datasets, flight log and videos are available depends on the active [deployment](deployments.md).
