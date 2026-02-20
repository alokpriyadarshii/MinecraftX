# MinecraftX

A **Minecraft style voxel sandbox** built with **C++14** + **OpenGL 3.3** on **macOS**, using **GLFW** (window/input) and **Assimp** (model loading).
This is a **learning/demo project** focused on chunked voxel rendering, simple physics, and classic OpenGL techniques (skybox, shadows, transparency).

---

## ✨ What’s inside
- Chunked voxel world (16×16 columns, stacked sub-chunks) with **Perlin-noise terrain generation**
- **Block placement & removal** (basic “Minecraft” loop)
- Transparent materials (water/glass) + non-cube crossing faces (torch/grass/flowers)
- **Day/night sky** with sun & moon and skybox textures
- **Shadow mapping** pipeline (toggleable in `MinecraftX/src/Render.cpp`)
- First-person + third-person camera, “Steve/Alex” models loaded via **Assimp**
- Simple collision + gravity / jump + swimming buoyancy
- Minimal GUI (crosshair) + “held block” rendering

---

## 🧰 Requirements
- macOS (Xcode target is set to **macOS 10.13** deployment target)
- Xcode (open the included `.xcodeproj`)
- Homebrew (recommended) for native libs:
  - `glfw`
  - `glew`
  - `assimp`

> Note: OpenGL is **deprecated** on macOS, but still works for learning projects.

---

## 🚀 Quick Start (open + run)

~~~bash
set -euo pipefail
cd "/path/to/where/you/unzipped/MinecraftX"

# Install deps via Homebrew
brew install glfw glew assimp

# Open the Xcode project
open "MinecraftX.xcodeproj"
~~~

### 1) Fix library paths (important)
This Xcode project was originally configured with Intel Homebrew paths (`/usr/local/...`).

- **Intel Macs:** usually OK as-is.
- **Apple Silicon (M1/M2/M3):** Homebrew lives under `/opt/homebrew`.

In **Xcode → Target (MinecraftX) → Build Settings**, update:
- **Header Search Paths**
  - Intel: `/usr/local/include`
  - Apple Silicon: `/opt/homebrew/include`
- **Library Search Paths**
  - Intel: `/usr/local/lib` (and any versioned Cellar paths if needed)
  - Apple Silicon: `/opt/homebrew/lib`

If you installed `glew` / `assimp` with different versions than the project expects, just re-link them in:
**Build Phases → Link Binary With Libraries**.

### 2) Set the Working Directory (assets loading)
Shaders, textures, and models are loaded using **relative paths** like `shader/Block.vs` and `picture/texture_big.png`.

Set the run working directory to the **inner** `MinecraftX/` folder (the one that contains `shader/`, `picture/`, and `model/`):

- `$(PROJECT_DIR)/MinecraftX`

In **Xcode → Product → Scheme → Edit Scheme → Run → Options**:
- ✅ **Use custom working directory** → select `.../MinecraftX/MinecraftX`

### 3) Build & Run
Press **⌘R** in Xcode.

---

## 🎮 Controls
- **Mouse**: look around
- **W/A/S/D**: move
- **Space**
  - Normal mode: jump (or float up when in water)
  - God mode: fly up
- **F**: fly down (useful in god mode)
- **Esc**: quit

### Block interaction
- **Left mouse (hold)**: break blocks
- **Right mouse (release)**: place block
- **← / → (Arrow keys)**: cycle the currently selected placeable block

### Options selector
Press a number to choose what the arrow **↑ / ↓** controls:

- **1** → FOV
  - ↑ decrease FOV, ↓ increase FOV
- **2** → Cursor mode
  - ↑ lock/hide cursor, ↓ show cursor
- **3** → Game mode
  - ↑ God mode, ↓ Normal mode
- **4** → Camera perspective
  - ↑ First-person, ↓ Third-person
- **5** → Third-person zoom
  - ↑ zoom in, ↓ zoom out (only in third-person)

---

## ♻️ World persistence / reset
Chunks are read/written as **binary files** named like `x_z` (for example `0_0`) in the app’s **working directory**.

- To reset the world: delete the generated `*_*` chunk files in that directory.

---

## 📁 Project structure (high level)

~~~text
MinecraftX/
  MinecraftX.xcodeproj/        # Xcode project (macOS target)
  MinecraftX/                  # Source + assets (working directory)
    src/                       # Game / rendering / voxel logic (C++)
      main.cpp
      Render.*                 # OpenGL init + render loop + input
      ChunkData.*              # Chunk storage + generation + persistence
      Game.*                   # Movement + physics + modes
      Gui.*                    # Crosshair / overlays
    shader/                    # GLSL shaders (Block / Skybox / Steve / etc.)
    picture/                   # Textures (blocks, skybox, sun/moon)
    model/                     # Steve/Alex OBJ + textures
    library/                   # vendored headers (glm, stb, glad, helpers)
  md_pic/                      # README screenshots & design images
  license                      # MIT
~~~

---

## 🛠 Customize
- **Screen size / block IDs / constants**: `MinecraftX/src/game_settings.h`
- **Terrain generation & world features** (height map, herbs, clouds): `MinecraftX/src/ChunkData.cpp`
- **Rendering + input** (FOV, shadow mapping, camera): `MinecraftX/src/Render.cpp`
  - Shadow mapping can be toggled via the `#define SHADOW_MAPPING` flag near the top.
- **Shaders**: `MinecraftX/shader/*`
- **Textures & models**: `MinecraftX/picture/*`, `MinecraftX/model/*`

---

## 🧯 Troubleshooting
- **Build fails: `assimp/Importer.hpp` not found**
  Install assimp (`brew install assimp`) and make sure Header Search Paths include your Homebrew include dir.
- **Linker errors for `glew` / `assimp` / `glfw`**
  Re-check Library Search Paths and re-add the libraries in **Link Binary With Libraries**.
- **Black screen / missing textures / missing shaders**
  Your working directory is wrong. Set it to `$(PROJECT_DIR)/MinecraftX` (see above).
- **Apple Silicon Homebrew path issues**
  Use `/opt/homebrew/include` and `/opt/homebrew/lib` instead of `/usr/local/...`.

---
**Note
