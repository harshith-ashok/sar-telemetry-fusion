# Search & Rescue

The **Search & Rescue** page simulates a drone looking down at a scene and asks a vision-language model (VLM) to find the people in it. It works on one image at a time, from several sources, and shows what the model found alongside an optional ground-truth comparison.

## Image sources

- **Dataset scenes.** A random image from one of the available datasets: drone-view RGB, thermal, or the paired KAIST set (shown as either its RGB or its thermal channel). A toggle chooses the dataset, and a button picks a new random scene.
- **Your own image.** Upload a file or drag one onto the image area. Uploaded images have no ground truth.

Which datasets are offered depends on the active [deployment](deployments.md).

## Detection

Pressing **Detect** asks the VLM to find every person in the image and classify each one by posture or activity: running, walking, laying down, seated, standing, or not defined when the posture cannot be told. The same classes are used for RGB, thermal and uploaded images.

The model works **blind**. It is told which classes exist, but never what is actually in the image: no dataset labels, positions or counts are provided. Each detected person is drawn as a labelled box on the image and listed in the side panel with a short description.

## Ground truth

The **Show ground truth** option overlays the dataset's own annotations (green boxes) and lists the annotated classes, so the model's output can be compared with the truth after the fact. It is off by default and is never shown to the model. The thermal dataset has a single class, so it can check where people are but not their posture.

## Conclusion agent

After a detection finishes, an analyst agent writes a short plain-language conclusion for the image: an assessment of what was found, where in the view it is, and a recommended action. It can be respawned or cancelled. Because this page works on a single image, the agent is told that no flight telemetry or second sensor is available, so its advice rests on that image alone.

## Limitations

- Posture classification is unreliable, especially for very small people.
- Small or distant people can be missed and boxes can be loose.
- The model can give different answers on repeated runs.
- Conclusions are advisory and are not verified automatically.

See [journey.md](journey.md) for the paired RGB and thermal workflow and [deployments.md](deployments.md) for how inputs are chosen.
