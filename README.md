# TWGT Device Node
This repository contains the bootstrap scripts and API bridge to run the TWGT Local-First Command Center on an Android/Termux device.

## Quick Start
1. Run `bash start-twgt.sh` to boot the Node API and React frontend.
2. Access the dashboard at `http://localhost:5173`.

## Components
- `api.cjs`: The Node.js API bridge (Port 9999) executing Termux shell commands and `gh` CLI.
- `start-twgt.sh`: One-command startup script.
