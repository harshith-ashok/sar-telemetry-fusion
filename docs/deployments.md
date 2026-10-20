# Deployments

A **deployment** is a named bundle of the inputs the system works on: which datasets are available, which flight log supplies telemetry, which videos are preloaded, and which model does the detecting. Choose one from the **Deployment** dropdown at the top of the left sidebar and every page switches to it.

## What a deployment contains

- **Datasets.** Any combination of the drone-view RGB set, the thermal set and the paired RGB/thermal set. A dataset that a deployment does not include is simply not offered.
- **Flight log.** The recorded flight that supplies telemetry and the flight map.
- **Videos.** Videos that are loaded as frame sources when the deployment is selected.
- **Model.** The vision-language model used for detection and for the analyst agent.

Each deployment also has a name and a short description shown in the sidebar.

## What switching does

- **Search & Rescue** offers only the datasets the deployment includes.
- **Journey** uses the deployment's paired dataset and its videos as frame sources.
- **Control Centre** status, flight map, vitals and mission log follow the deployment. Each deployment keeps its own log of findings, so switching away and back restores it.
- The sidebar lists what the deployment contains, marking anything that cannot be found.

Visualize, Ingest and Dashboard work on log files you provide, not on deployments.

## Limitations

- The active deployment applies to the whole system rather than to each browser tab or user.
- Deployment definitions and the files they refer to are read when the system starts, so changes need a restart.
