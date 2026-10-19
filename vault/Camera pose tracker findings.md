---
tags:
  - note/guide
created: 2026-10-18
source: agent
---

# Camera pose tracker findings

Building a webcam body tracker (skeleton only, camera feed never displayed) for a friend on Windows. Project lives at `/OpenCode-Sandbox/camera-pose`.

## YOLO pose vs MediaPipe

- YOLO pose gives **17 COCO keypoints only**: nose, eyes, ears, shoulders, elbows, wrists, hips, knees, ankles. No hands/fingers, no face detail, and partial arms are unreliable.
- For hands, face, and partial arms use **MediaPipe tasks API** (installed 1.1.1):
  - `vision.PoseLandmarker` → 33 points (includes refined arms)
  - `vision.HandLandmarker` → 21 points per hand, `num_hands=2`
  - `vision.FaceLandmarker` → 468-point face mesh + irises
- MediaPipe 1.x has **no `mp.solutions`** — that API is gone. Use `mediapipe.tasks` + `.task` model assets.
- `.task` models auto-download from `storage.googleapis.com/mediapipe-models/...`.
- Drawing: `vision.drawing_utils.draw_landmarks` + `PoseLandmarksConnections.POSE_LANDMARKS`, `HandLandmarksConnections.HAND_CONNECTIONS`, `FaceLandmarksConnections.FACE_LANDMARKS_*` (all 0-based, unlike ultralytics' 1-based skeleton list).

## Windows gotchas (friend's machine)

- MediaPipe wheels are `py3-none-win_amd64` — they **work on Python 3.14**. The 3.12 advice was wrong; no need to downgrade.
- **opencv-python vs opencv-contrib-python conflict**: MediaPipe depends on `opencv-contrib-python`, which also provides `cv2`. Installing both breaks the import chain and shows as "No module named mediapipe". Fix: `pip uninstall -y opencv-python opencv-contrib-python` then `pip install mediapipe` (it pulls its own opencv).
- **Virtual camera**: `pyvirtualcam` on Windows needs the **OBS Virtual Camera** driver. Run OBS Studio once to register it; then select "OBS Virtual Camera" in Discord/Zoom.
- **Em-dash trap**: Windows errors "No such file or directory" when a path contains an em-dash (`camera—pose`) instead of a hyphen (`camera-pose`). Use hyphen-free folder names.
- One-liner pattern that works: download zip → `Expand-Archive` to `%USERPROFILE%\Downloads` → `pip install mediapipe pyvirtualcam` → run `py <full path>\script.py --virtual`.

## Linux virtual camera

- `v4l2loopback`: `pkexec modprobe v4l2loopback devices=1 video_nr=2 card_label="BodyTrack" exclusive_caps=1` creates `/dev/video2`.
- Feed it by piping raw BGR frames to `ffmpeg -f rawvideo -pix_fmt bgr24 -s WxH -r 30 -i - -f v4l2 /dev/video2`.
- `pkexec rmmod v4l2loopback` removes it. A consumer (Discord) left pointing at a dead virtual camera can spin and lag the machine — switch its camera to None first.

## Performance

- MediaPipe runs on CPU, not the GPU. Heavy pose + face mesh + hands is slow.
- Fast preset: `pose_landmarker_lite` only, ~256px input, single model.
- Complex preset: `pose_landmarker_heavy` + both hands + full face mesh, 640px.

## Privacy invariant

The overlay/virtual camera must only ever contain landmarks on black. Never pass the captured frame to the drawing surface; capture is for inference only. Mirror the canvas at the end (`cv2.flip(canvas, 1)`).

## Discord on Linux (verified 2026-10-19)

- `pkexec modprobe v4l2loopback devices=1 video_nr=2 card_label="BodyTrack" exclusive_caps=1` → `/dev/video2`.
- `cd /OpenCode-Sandbox/camera-pose && ./run.sh --virtual` pipes the landmark-only canvas to it.
- Discord → Voice & Video → Camera → **BodyTrack**; Discord keeps that selection between runs, but the camera must be toggled off/on after the virtual cam starts or it stays on a dead device.
- Verified Discord's own camera track by sampling its `<video>` element: mean pixel 1.1/255, 0.76% non-black — skeleton on black, no camera feed.
- Driving the Discord client is possible because it runs with `--remote-debugging-port=9222`; `Runtime.evaluate` in context 1 can click its DOM (its `localStorage` is undefined there, DOM works).

[[Inbox]]
