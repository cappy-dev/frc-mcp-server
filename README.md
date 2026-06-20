# frc-mcp-server

A customizable MCP (Model Context Protocol) server for FRC (FIRST Robotics Competition). It provides AI agents with searchable access to your team's robot code, WPILib documentation, and vendor library context — making it easier to get accurate, team-specific answers about your robot.

Built on the [Model Context Protocol](https://modelcontextprotocol.io/) and deployed via [Vercel](https://vercel.com/) for zero-infrastructure setup.

## Features

- **Three MCP tools** for browsing, searching, and reading FRC context:
  - `list_frc_sources` — Discover all indexed files grouped by directory
  - `search_frc_context` — Full-text search across documents and code (case-insensitive, scored by relevance)
  - `get_frc_file` — Read a specific file by path, with optional line-range selection
- **Customizable data** — Drop your robot code, WPILib docs, and vendor notes into `src/data/` and they become immediately available
- **Vercel deployment** — One-click deploy; no server management needed
- **CI included** — Automated type-checking and build on push via GitHub Actions

## Quick Start

### Prerequisites

- Node.js >= 20
- npm

### Install and Build

```bash
npm ci
npm run build
```

### Run Locally

```bash
npm start
```

The server exposes a Streamable HTTP MCP endpoint. By default, Vercel routes the MCP endpoint to `/mcp`.

### Deploy to Vercel

1. Fork or clone this repo
2. Push to your own GitHub account
3. Import the project in [Vercel](https://vercel.com/new) — it will auto-detect the configuration from `vercel.json`
4. Your MCP server will be live at `https://<your-deployment>.vercel.app/mcp`

## Adding Your FRC Context

Place files in `src/data/` following this structure:

```
src/data/
  robot/          ← Your team's robot source code
    src/main/java/frc/robot/...
    build.gradle
  wpilib/         ← WPILib documentation & API notes
    docs/
    api-notes/
  vendors/        ← Vendor library context (REV, CTRE, PathPlanner, etc.)
    rev/
    ctre/
    pathplanner/
```

Supported file types: `.md`, `.txt`, `.json`, `.java`, `.kt`, `.cpp`, `.cc`, `.c`, `.h`, `.hpp`, `.gradle`, `.xml`, `.yml`, `.yaml`

### Tips

- Keep files focused and high-signal — smaller, curated docs produce better agent answers
- Add a `README.md` in each subfolder describing what's inside
- Refresh data each season and remove stale docs
- See [docs/FRC_CONTEXT.md](docs/FRC_CONTEXT.md) for the full guide on organizing your context data

## MCP Tools Reference

### `list_frc_sources`

List all indexed files grouped by directory. Use this to discover available FRC documentation and team robot code context.

**Parameters:** None

**Example output:**
```
# Available FRC Context Sources
## frc-docs/ (42 files) - Official FRC/WPILib Documentation
  frc-docs/docs/software/command-based.md
  ...
## robot/ (18 files) - Team Robot Code
  robot/src/main/java/frc/robot/Constants.java
  ...
```

### `search_frc_context`

Search across FRC documentation and team robot code for classes, methods, constants, and concepts. Returns relevant code snippets with context.

**Parameters:**

| Parameter   | Type   | Required | Description |
|-------------|--------|----------|-------------|
| `query`     | string | Yes      | Text to search for (e.g. `PIDController`, `swerve`, `vision`, `AutoBuilder`). Case-insensitive, minimum 2 characters. |
| `sourcePath`| string | No       | Limit search to a specific file or directory prefix (e.g. `robot/src/main/java/frc/robot/`) |
| `limit`     | number | No       | Max snippets to return (default: 8, max: 25) |

### `get_frc_file`

Read complete or partial content of a specific file.

**Parameters:**

| Parameter    | Type   | Required | Description |
|--------------|--------|----------|-------------|
| `sourcePath` | string | Yes      | Relative path from `list_frc_sources` (e.g. `robot/src/main/java/frc/robot/Constants.java`) |
| `startLine`  | number | No       | 1-based start line |
| `endLine`    | number | No       | 1-based end line (inclusive) |

## Project Structure

```
├── .github/
│   ├── dependabot.yml          # Automated dependency updates
│   └── workflows/
│       └── ci.yml              # Build & type-check on push/PR
├── docs/
│   └── FRC_CONTEXT.md          # Guide for organizing context data
├── public/
│   └── index.html              # Landing page
├── src/
│   ├── data/                   # ← Your FRC context goes here
│   │   └── README.md
│   └── index.ts                # MCP server implementation
├── .gitignore
├── LICENSE                     # GPL-3.0-or-later
├── package.json
├── tsconfig.json
└── vercel.json                 # Vercel deployment config
```

## Development

```bash
# Install dependencies
npm ci

# Type-check without emitting
npm run typecheck

# Build for production
npm run build

# Start the server
npm start
```

CI runs automatically on push and Pull Requests against `master`, testing across Node.js 20.x and 22.x.

## Contributing

Contributions are welcome! Here's how you can help:

1. Fork the repository
2. Create a feature branch (`git checkout -b my-feature`)
3. Make your changes
4. Ensure type-checking and build pass (`npm run typecheck && npm run build`)
5. Commit with a clear message (`git commit -m "Add new feature"`)
6. Push to your branch (`git push origin my-feature`)
7. Open a Pull Request against the `master` branch

### Ideas for Contributions

- Additional MCP tools (e.g., syntax-aware code search, dependency analysis)
- Improved search scoring or snippet extraction
- Example data sets or starter templates for common FRC setups
- Better landing page / UI for the public endpoint

## License

This project is licensed under the [GNU General Public License v3.0 or later](LICENSE).
