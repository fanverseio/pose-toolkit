# pose-agent-toolkit

An open-source, frontend-only Three.js application for creating pose and scene references using 3D mannequins, props, and annotations. Designed for artists, animators, and designers who need quick visual references without complex software.

## Project Overview

**pose-agent-toolkit** is a local-first, browser-based tool for:
- Composing 3D scenes with mannequins and props
- Adjusting poses with intuitive interaction handles
- Adding annotations and markers
- Exporting flat 2D images with overlay annotations
- Saving and loading projects locally

**V1 Scope:** Frontend-only, no backend, no database, no account system. Everything runs in the browser with local file storage.

## Tech Stack

- **3D Engine:** Three.js r150 (via CDN + npm)
- **Controls:** Three.js TransformControls, OrbitControls (via CDN)
- **Build Tool:** Vite (for development and production builds)
- **Package Manager:** npm / pnpm / yarn compatible
- ** browsers:** Chrome-first (V1), Firefox/Edge partial support
- **File Format:** Project data saved as `project.json`

**Dependencies:**
```json
{
  "three": "^0.150.0",
  "vite": "^4.x.x"
}
```

## Architecture

### Core Modules

| Module | Description | File |
|--------|-------------|------|
| **Main Entry** | Application bootstrap, scene setup, event handling | `src/main.js` |
| **Mannequin System** | Procedural mannequins via mannequin-js library | `src/main.js` (imports `Male` from `mannequin-js`) |
| **Export System** | Export orchestration (image/video/annotation) | `src/exportSystem.js` |
| **Image Exporter** | 2D flat image export with annotations | `src/export/ImageExporter.js` |
| **Video Exporter** | Animated scene capture | `src/export/VideoExporter.js` |
| **Pose Exporter** | Pose data serialization | `src/export/PoseExporter.js` |
| **File Utilities** | Project save/load helpers | `src/utils/file-utils.js` |

### UI Components

Located in `src/ui/`:
- **Toolbar.js** — Main tool buttons and mode switches
- **PosePanel.js** — Pose parameter controls
- **ExportPanel.js** — Export settings and triggers

### Scene Structure

```
Scene
├── Camera (Perspective)
├── Lighting (Ambient + Directional)
├── Mannequins (multiple, each with IK handles)
├── Props/Assets (GLB/GLTF imports)
├── Annotation Markers
└── Grid/Floor
```

## Current Implementation Status

### ✅ Implemented Features

| Feature | Implementation Notes |
|---------|---------------------|
| **Three.js Scene Setup** | Basic scene, camera, lights, floor grid |
| **Mannequin Rendering** | Procedural mannequins via `mannequin-js` (`Male` class) — no external GLB files required |
| **Transform Controls** | Gizmo attached to selected mannequin for translate/rotate/scale |
| **Interaction Modes** | `translate` / `rotate` / `scale` modes with space toggles (world/local) |
| **Camera Presets** | Front/Back/Left/Right/Top/Bottom relative to active mannequin |
| **Camera HUD** | Fine-grained camera position/rotation control via inputs and buttons |
| **Project Save** | Exports scene state to `project.json` (camera pose, mannequin poses) |
| **Keyboard Shortcuts** | Undo/Redo (Cmd+Z/Cmd+Shift+Z), Toggle modes (W/E/R shortcuts) |
| **Mannequin IK Handles** | Drag handles for hands/feet/limbs with inverse kinematics simulation |
| **Export System Skeleton** | `exportSystem.js` orchestrates exports; `ImageExporter` and `VideoExporter` have basic structure |

### ⚠️ Partially Implemented

| Feature | Status | Notes |
|---------|--------|-------|
| **Project Load** | Function exists but integration incomplete | `file-utils.js` has parsing logic; main app needs UI trigger |
| **Image Export** | Base structure in `ImageExporter.js` | 2D flat rendering with annotation overlays not yet wired |
| **Video Export** | Base structure in `VideoExporter.js` | Video capture not implemented |
| **Pose Export** | Base structure in `PoseExporter.js` | Serialization format not standardized |
| **Props/Assets Import** | Code skeleton exists | GLB/GLTF importing not integrated into UI |

### ❌ Not Implemented

| Feature | Notes |
|---------|-------|
| **Annotation System** | Marker placement, text labels, line drawing — no UI or logic present |
| **Asset Library** | Built-in library of props/objects — UI and storage not implemented |
| **Export Presets UI** | 1920×1080, 1080×1920, 1080×1080 options not exposed in UI |
| **Partial Project Loading** | Graceful handling of missing/corrupted assets |
| **Browser Fallback Support** | Firefox/Edge testing and fixes pending |
| **Automated Testing** | No test framework, unit tests, or E2E tests |

## Installation & Running

### Prerequisites

- Node.js 18+ (or 16 with npm 8+)
- A modern browser (Chrome recommended)

### Setup

```bash
# Clone the repository
cd pose-agent-toolkit

# Install dependencies
npm install
# or
pnpm install
# or
yarn install

# Start development server
npm run dev
# or
pnpm dev
# or
yarn dev
```

### Production Build

```bash
npm run build
```

Build artifacts will be in `dist/`. Serve with any static file server:

```bash
npx serve dist
```

### Manually Open

For quick testing without a server, open `index.html` in a browser. **Note:** Some features (module loading, local file access) may be limited without a server.

## Known Issues

### 🟡 Non-Critical Issues

1. **Mannequin docs mismatch (resolved)**
   - The project uses `mannequin-js` procedural mannequins (no `mannequin.glb` required).
   - If you see older references to `GLTFMannequin` / `mannequin.glb`, treat them as removed V1 scope.

2. **TransformControls Module Resolution Error**
   - Error: `EISDIR: illegal operation on a directory, read` when accessing TransformControls module
   - The `three/examples/jsm/controls/` path appears to be a directory, not a module file
   - **Impact:** TransformControls gizmo may not load in some build configurations; fallback to CDN required

   **Workaround:** Use CDN version in `index.html`:
   ```html
   <script type="importmap">
   {
     "imports": {
       "three": "https://unpkg.com/three@0.150.0/build/three.module.js",
       "three/addons/": "https://unpkg.com/three@0.150.0/examples/jsm/"
     }
   }
   </script>
   ```

3. **Export System Incomplete**
   - `ImageExporter.js`, `VideoExporter.js`, and `PoseExporter.js` have scaffold code but are not wired to the UI
   - No actual rendering pipeline configured for flat 2D export
   - **Impact:** Cannot export images or videos until pipelines are implemented

4. **Project Load Missing UI Trigger**
   - `saveProject()` function exists and works, but `loadProject()` equivalent is not connected to UI
   - File input dialog or drag-and-drop for `project.json` not implemented
   - **Impact:** Can save but not load saved projects easily

5. **Annotation System Absent**
   - Marker placement, text labels, and drawing tools are not implemented
   - Export annotations mode (2D flat overlays) has no underlying data structure
   - **Impact:** Cannot add or export annotations, a core feature from PRD

6. **No Error Handling**
   - File operations (save/load) have minimal error handling
   - GLTF loading failures may crash without user feedback
   - **Impact:** Poor user experience when operations fail

7. **Browser Compatibility**
   - Tested only on Chrome; Firefox/Edge likely have rendering differences
   - TransformControls behavior may vary across browsers
   - **Impact:** Limited user reach; may need browser-specific fixes

8. **No Testing**
   - No unit tests, integration tests, or E2E tests
   - Manual testing only
   - **Impact:** Regressions not caught; refactoring risky

## Project Structure

```
pose-agent-toolkit/
├── index.html              # Entry HTML
├── package.json            # Dependencies and scripts
├── vite.config.js          # Vite build config (if present)
├── README.md               # This file
├── PRD.md                  # Product Requirements Document
├── MEMORY.md               # Development notes and decisions
├── dist/                   # Production build output
├── src/
│   ├── main.js             # Application entry point (Three.js scene + mannequin-js integration)
│   ├── exportSystem.js     # Export orchestration
│   └── style.css           # Global styles
├── node_modules/           # Dependencies (generated)
└── .dev/                   # Specs, QA, TODOs
```

## Next Steps

### Immediate Priorities

1. **Fix TransformControls Module Loading**
   - Configure import map to use CDN for `three/addons/`
   - Or bundle TransformControls as a local module
   - Test in both development and production builds

3. **Wire Export System**
   - Implement rendering pipeline in `ImageExporter.js` (capture canvas, overlay annotations, export)
   - Complete `VideoExporter.js` with MediaRecorder API
   - Define pose serialization format in `PoseExporter.js`
   - Add UI buttons in `ExportPanel.js` to trigger exports

### Short-Term (1-2 weeks)

4. **Implement Project Load**
   - Add file input dialog for selecting `project.json`
   - Integrate `file-utils.js` parsing logic
   - Trigger scene reconstruction from saved data
   - Handle missing assets gracefully (show placeholders, skip errors)

5. **Build Annotation System**
   - Design data structure for markers (position, text, color)
   - Implement marker placement tool (click to add, drag to move)
   - Add text label input and editing
   - Render annotations in 2D export mode only

6. **Add Asset Library**
   - Create `public/assets/` directory for props/objects
   - Build UI for listing and selecting assets
   - Implement GLB/GLTF import dialog for custom assets
   - Load and place assets in scene

### Medium-Term (2-4 weeks)

7. **Error Handling & UX**
   - Add try/catch blocks for file operations
   - Show user-friendly error messages (toasts, modals)
   - Validate file types and sizes on import
   - Add loading spinners for async operations

8. **Browser Compatibility**
   - Test on Firefox and Edge
   - Fix rendering differences
   - Add vendor prefixes if needed
   - Provide feature detection and fallbacks

9. **Testing Setup**
   - Set up Vitest or Jest for unit tests
   - Write tests for core utilities (`file-utils.js`, pose math)
   - Add E2E tests with Playwright or Cypress for critical user flows
   - Configure CI/CD for automated testing

### Long-Term (post-V1)

10. **Backend Integration** (V2+)
    - Add cloud save/load options
    - Implement user accounts and projects
    - Shareable links with view-only mode
    - API for programmatic control

## Development Notes

### Key Design Decisions

- **Local-First:** No backend required for V1; all data stored locally
- **Single File Export:** Project saved as `project.json` with all scene data
- **Three.js CDN:** Using unpkg CDN for Three.js modules to ensure compatibility
- **Mannequin-Relative Camera:** Presets move camera relative to active mannequin orientation
- **IK Handles:** Simplified inverse kinematics by dragging limb endpoints

### Code Conventions

- Modules use ES6 imports/exports
- Three.js objects wrapped in semantic classes
- UI components handle their own DOM manipulation
- Event listeners centralized in `main.js` for initialization

### Performance Considerations

- Scene culling not yet implemented (mannequins always rendered)
- Annotation markers should use instanced meshes if many
- Large GLTF assets may impact load time (consider compression)

## Contributing

This is an open-source project. Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

[To be determined — add license information]

## Contact

For questions, issues, or feature requests, please open an issue on GitHub or contact the maintainers.

---

**Last Updated:** 2025-03-22
**Build Version:** V0.1-alpha (development)
