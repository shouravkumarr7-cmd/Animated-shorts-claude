# Claude 2D & 3D Stickman Video Studio (`claude-stickman-studio`)

An open-source toolkit and [Claude Code](https://code.claude.com/docs/en/overview) skill repository that lets anyone generate **1080×1920 (9:16, 30 fps) 2D and 3D animated stickman vertical Shorts** from a single plain-English prompt or JSON story file—complete with narrator voiceover, word-by-word pop captions, procedural background music, and synchronized sound effects.

Works on **Mac, Windows, Linux, and mobile browsers** via [GitHub Codespaces](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) and [GitHub Actions](https://docs.github.com/actions/managing-workflow-runs/manually-running-a-workflow).

## Key Features

| Feature | 2D Classic Stickman Mode (`--mode 2d`) | 3D Toon Diorama Mode (`--mode 3d`) |
| :--- | :--- | :--- |
| **Visual Style** | Flat round-head stickman cast, clean `#1E1E24` ink outlines, 16 indoor/outdoor vector scenes, 25 props, animated chalkboards | 3D perspective floor grid, volumetric rim lighting (`#FFB36B` / `#7FC4F0`), specular sphere shading, `#241726` dark-plum outlines, tilt-shift depth-of-field |
| **Rendering Engine** | Native [Skia-Python](https://github.com/kyamagu/skia-python) with automatic Pillow/NumPy fallback (`engine/skia_compat.py`) | Python 3D diorama pass (`engine/three_d.py`) + interactive browser [Three.js `MeshToonMaterial`](https://threejs.org/docs/pages/MeshToonMaterial.html) & [`OutlineEffect`](https://threejs.org/docs/pages/OutlineEffect.html) previewer (`web-3d-studio/index.html`) |
| **Voice & Audio** | Local neural TTS via [Piper TTS](https://github.com/OHF-voice/piper1-gpl) (`joe`, `john`, `bryce`, `norman`, `kristin`) + offline `ffmpeg` `libflite` fallback | Auto-ducked procedural score, 15 synthesized physical SFX cues, and EBU R128 loudness normalization (`-18` to `-14 LUFS`) |
| **Claude Integration** | Pre-configured [`CLAUDE.md`](https://code.claude.com/docs/en/memory) and [`.claude/skills/`](https://code.claude.com/docs/en/skills) (`/find-story`, `/make-video`) | Dedicated `/make-3d-video` skill following `docs/STYLE_BIBLE_3D.md` with twist-line `3.5°` camera roll stings |

## 3 Ways to Use This Repository

### Option 1: 1-Click Cloud Setup in GitHub Codespaces (PC or Mobile Phone)
If you are on a smartphone or want zero local installation, open this repository in [GitHub Codespaces](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) (included free with the [GitHub Student Developer Pack](https://education.github.com/pack)):
1. Click **Code → Codespaces → Create codespace on main**.
2. The `.devcontainer/devcontainer.json` container automatically installs Python 3.11, FFmpeg, OpenGL/EGL libraries, and runs `python3 run.py setup`.
3. Open [Claude Code](https://code.claude.com/docs/en/overview) or GitHub Copilot Chat in the terminal and run:
   ```text
   /find-story medicine
   /make-video 1
   /make-3d-video 1
   ```

### Option 2: Zero-Install Cloud Rendering via GitHub Actions
Anyone who forks this repository can render 2D and 3D videos directly inside GitHub's cloud servers using the included [GitHub Actions `workflow_dispatch`](https://docs.github.com/actions/managing-workflow-runs/manually-running-a-workflow) pipeline:
1. Go to the **Actions** tab of your GitHub repository and select **Render 2D or 3D Stickman Video**.
2. Click **Run workflow**, enter a story file path (e.g., `stories/marathon_1904_3d.json` or `stories/handwashing_doctor.json`), and pick `2d`, `3d`, or `auto`.
3. Download the finished 1080×1920 `.mp4` and contact sheet from the workflow run's [Artifacts section](https://docs.github.com/en/actions/concepts/workflows-and-actions/workflow-artifacts).

### Option 3: Local Desktop Setup (Mac, Windows, Linux)
```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/claude-stickman-studio.git
cd claude-stickman-studio

# 2. Run one-time setup (creates .venv, installs packages, downloads default voice)
python3 run.py setup

# 3. Validate a story file
python3 run.py check stories/marathon_1904_3d.json

# 4. Render fast preview stills + contact sheet (2D or 3D)
python3 run.py stills stories/handwashing_doctor.json --mode 2d
python3 run.py stills stories/marathon_1904_3d.json --mode 3d

# 5. Render the full 1080x1920 30fps MP4 video
python3 run.py render stories/handwashing_doctor.json --mode 2d
python3 run.py render stories/marathon_1904_3d.json --mode 3d
```

## Using with Free Claude.ai Web App (Without Claude Code CLI)
If you use the free web version of Claude instead of [Claude Code](https://code.claude.com/docs/en/overview):
1. Upload `.claude/skills/make-video/SKILL.md`, `docs/STORY_TO_FILM.md`, and `docs/STYLE_BIBLE_3D.md` to a Claude Project or chat (or upload the skill folder via [Claude Skills](https://support.claude.com/en/articles/12512180-use-skills-in-claude)).
2. Ask Claude: *"Create a 3D stickman story JSON about the 1904 Olympic Marathon following `STORY_TO_FILM.md`."*
3. Save Claude's JSON output to `stories/my_story.json` in your repository and either run `python3 run.py render stories/my_story.json --mode 3d` or trigger the **GitHub Actions** workflow from your browser.

## Repository Structure

```text
claude-stickman-studio/
├── CLAUDE.md                        # Project memory auto-loaded by Claude Code
├── README.md                        # Full documentation & quickstart guide
├── LICENSE                          # MIT License
├── run.py                           # Unified CLI (setup, check, stills, render, voices)
├── publish_to_github.sh             # 1-command helper to push this repo to GitHub
├── requirements.txt                 # Python dependencies (skia-python, piper-tts, pillow, numpy)
├── .devcontainer/
│   └── devcontainer.json            # 1-click GitHub Codespaces environment
├── .github/workflows/
│   └── render-video.yml             # Cloud MP4 rendering via GitHub Actions workflow_dispatch
├── .claude/skills/                  # Auto-discovered Claude Code slash skills
│   ├── find-story/SKILL.md          # /find-story
│   ├── make-video/SKILL.md          # /make-video (2D mode)
│   └── make-3d-video/SKILL.md       # /make-3d-video (3D diorama mode)
├── claude-skills/                   # Visible backup of .claude/skills/
├── docs/
│   ├── STORY_TO_FILM.md             # Complete 7-beat story formula, props, places, poses & schema
│   └── STYLE_BIBLE_3D.md            # 3D miniature-diorama & toon stickman specification
├── engine/
│   ├── __init__.py                  # Auto-detects skia-python or Pillow compatibility layer
│   ├── characters.py                # Stickman rig: 6 bodies, 9 hats, 17 poses, 14 faces, walks
│   ├── places.py                    # 16 indoor/outdoor presets + weather FX (rain, snow, lightning)
│   ├── props.py                     # 25 vector/diorama props + animated chalkboard
│   ├── scenes.py                    # Shot types: wide, closeup, board, headline, card
│   ├── three_d.py                   # 3D perspective grid, volumetric rim lighting & DOF pass
│   ├── draw.py                      # Shaders, easing curves, grain, vignette & pop captions
│   ├── audio.py                     # Piper neural TTS + ffmpeg libflite fallback + music & 15 SFX
│   ├── check.py                     # Pre-render linter for coordinates, pacing, and shot variety
│   ├── render.py                    # Frame-accurate 30fps MP4 encoder with --mode 2d|3d
│   └── skia_compat.py               # Pure Pillow/NumPy fallback when native Skia is absent
├── stories/
│   ├── handwashing_doctor.json      # Tested 2D example (Ignaz Semmelweis, 1847)
│   ├── lightning_rod.json           # Tested 2D/3D storm example (Benjamin Franklin, 1752)
│   └── marathon_1904_3d.json        # Tested 3D diorama example (1904 St. Louis Marathon)
├── topics/
│   ├── latest_options.md            # Fact-checked 7-beat story options with sources
│   └── used.txt                     # Tracker of completed video topics
└── web-3d-studio/
    └── index.html                   # Browser-based Three.js 3D Toon Stickman interactive studio
```

## Credits & Upstream Documentation
- **Agent & Skill Standard**: Built on [Anthropic Agent Skills](https://github.com/anthropics/skills), [Claude Code Skills](https://code.claude.com/docs/en/skills), and [`CLAUDE.md` project memory](https://code.claude.com/docs/en/memory).
- **Graphics & 3D Rendering**: Powered by [Skia-Python](https://github.com/kyamagu/skia-python), Pillow, and [Three.js `MeshToonMaterial`](https://threejs.org/docs/pages/MeshToonMaterial.html) / [`OutlineEffect`](https://threejs.org/docs/pages/OutlineEffect.html).
- **Neural Voice Synthesis**: Local commercial-free voices (`joe`, `john`, `bryce`, `norman`, `kristin`) via [Piper TTS (`OHF-Voice/piper1-gpl`)](https://github.com/OHF-voice/piper1-gpl) and [rhasspy/piper-voices](https://huggingface.co/rhasspy/piper-voices).
