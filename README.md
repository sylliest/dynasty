# DYNASTY.EXE — 3D AR STUDIO

A browser-based augmented reality try-on tool built for the **dynasty.exe** Y3K brand concept. Place 3D accessories on your face in real time using your webcam — no app, no install, just a URL.

---

## HOW TO USE

**Open the site** in Chrome or Edge (camera requires HTTPS or localhost — GitHub Pages works out of the box).

**Allow camera access** when the browser prompts you. Everything runs locally; your camera feed never leaves the page.

**Pick an accessory** by pressing **SWITCH ACCESSORY** at the bottom. A full-screen modal opens with spinning 3D previews of every item. Click any card or press SELECT to apply it.

**Adjust position** by dragging directly on the camera viewport — the accessory moves with your drag in real time.

**Capture your look** with the round shutter button. It saves a PNG with the AR overlay composited onto your camera frame.

---

## ADMIN MODE — `Ctrl + 0`

Opens a hidden settings panel on the right. From here you can:

- **Import a 3D model** — supports `.glb`, `.gltf`, `.obj`, and `.stl`. GLB files with embedded textures and PBR materials are fully supported. After importing, the accessory picker opens automatically so you can preview the model before selecting it.
- **Fine-tune anchor offsets** — X / Y / Z position, rotation on all three axes, and scale. These save per-item so each accessory remembers its fit.
- **Adjust material** — metalness and roughness sliders for models that use the default chrome material.
- **Environment / HDRI** — by default the camera feed itself is used as the environment map (so metallic surfaces reflect your actual room). Switch to the built-in studio lighting or upload your own HDR / image file.
- **Clear catalogue** — removes all imported items and resets to the built-in defaults.

---

## MASK DEBUG — `Ctrl + 9`

Toggles visibility of the 3D face occlusion mesh. When enabled it renders as a semi-transparent pink overlay so you can see how the depth mask maps to your face. This is how the glasses arms disappear behind your ears — the mask writes depth to the GPU before the accessories render, so anything behind the face surface is automatically hidden.

---

## IMPORTING MODELS

| Format | Notes |
|--------|-------|
| `.glb` / `.gltf` | Recommended. Embedded textures, PBR materials, Draco compression all supported. |
| `.stl` | Geometry only. Gets the default chrome PBR material. |
| `.obj` | Geometry only (no `.mtl` sidecar — browser security blocks relative file paths). |

After importing, open **SWITCH ACCESSORY** to preview. Use the **ANCHOR** selector in the preview to choose whether the item tracks your **face** (glasses position) or **neck** (shoulder/necklace position).

Fine-tune the fit in Admin mode after selecting the item — the scale and offset sliders let you dial in exactly where the model sits.

---

## TECHNICAL NOTES

- Runs **100% in the browser** — no server, no backend, no data upload.
- Face tracking uses **MediaPipe FaceLandmarker** (468 3D landmarks + full 4×4 head pose matrix). Models rotate correctly in all three axes as you turn your head.
- Neck tracking uses **MediaPipe PoseLandmarker** (33 body keypoints).
- The face occlusion mesh triangulates live from the 36-point face oval + interior landmark zones, giving depth-correct hiding of glasses arms behind ears and cheeks.
- Scale is computed from the **true 3D euclidean eye span** (including MediaPipe's z-depth), so accessories don't shrink when you turn your head to the side.
- Rendering via **Three.js r128** with ACESFilmic tone mapping and physically-based materials.
