# Control Centre

One screen for a search-and-rescue drone operator: is everything working, where has the drone been, what has it found, what needs attention, and what should happen next. It brings together the flight data, the detections from the other pages and the analyst agent, and adds a search sweep and a mission brief.

## Layout

| Column | Content |
|--------|---------|
| Left | **Flight map** (the track, start and end, and a pin for each finding) and **Flight** vitals such as duration, distance, altitude, speed, battery and GPS quality |
| Middle | Totals (findings, confirmed people, casualty alerts), **Alerts**, and the **Mission log** of every finding |
| Right | **Search sweep**, **Operator brief**, and links to the other tools |

A row of status indicators across the top shows whether each part of the system and each input is available.

## Map pins

Pins are coloured by what was found: red for a possible casualty, green for people confirmed by both sensors, orange for single-sensor detections, and grey where nothing was found. The number on a pin is the people count. The map is a local projection of the flight track.

## Alerts

Alerts summarise what needs attention, in three levels:

- **Critical:** a possible casualty (a person laying down), or the detection service being unavailable.
- **Warning:** detections seen by only one sensor, a missing model or flight log, low battery, or weak GPS.
- **Info:** people confirmed by both sensors, or no findings yet.

A possible casualty is a prompt for a human to look, not a diagnosis.

## Mission log

A table of every finding with its time, position, how many people were confirmed out of how many were detected, the postures seen and the battery level. Selecting a row shows the agent's conclusion for that finding.

## Search sweep

A sweep simulates a search pass. Choose a source and a number of frames, and the system detects people on frames spread evenly across the source, adding each result to the log as it goes. A sweep can be stopped part-way. Findings made on the Journey page appear here too.

## Operator brief

**Generate brief** asks the analyst agent to summarise the flight and all findings so far: the overall situation, priority targets and where they were seen, gaps and uncertainty, and recommended next actions. The text streams into the panel as it is written.

## Limitations

- Flight data and imagery come from different sources, so positions are simulated.
- Detection results can vary between runs.
- The log is not kept permanently.

Which datasets, flight log and videos are used depends on the active [deployment](deployments.md).
