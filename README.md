# MCM Vault Presets

This repository is the source of truth for all preset bundles distributed via MCM Vault to Milestone Creative Media's team.

## How it works

The MCM Vault desktop app fetches `manifest.json` from this repo on launch and on a schedule. When a bundle's `version` field changes, the app re-downloads its files and installs them into the team member's local Premiere Pro or DaVinci Resolve folders.

There is no build step. You commit, you push, the team gets it.

## Structure

```
mcm-vault-presets/
├── manifest.json                ← single source of truth, edited by hand
├── README.md                    ← this file
└── bundles/
    ├── youtube-export-presets/
    │   ├── MCM_YT_1080p.epr
    │   ├── MCM_YT_4K.epr
    │   └── MCM_YT_Shorts.epr
    ├── client-delivery-luts/
    │   └── ...
    └── ...
```

Each bundle is a folder under `bundles/`. The folder name should match the bundle's `id` in the manifest.

## How to update a bundle

1. Make your change inside `bundles/[bundle-id]/`. Add files, replace files, or remove files as needed.
2. Open `manifest.json` and bump the `version` for that bundle. Any change to the version string triggers an update on every team member's app — semver isn't required, but bumping the patch number (e.g. 1.0 → 1.1) for additions and the minor or major (e.g. 1.x → 2.0) for breaking changes is a sensible convention.
3. If you removed any files from the folder, also remove their names from the `files` array in the manifest.
4. If you added new files, add their names to the `files` array.
5. Update `updatedAt` at the top of the manifest to the current ISO timestamp.
6. Commit and push to `main`.

That's it. Within the configured check interval (default 4 hours), every team member's MCM Vault app will pull the new files. They can also click "Update all" to get the change immediately.

## How to add a new bundle

1. Create a new folder under `bundles/` named with a kebab-case slug (e.g. `wedding-color-pack`). This becomes the bundle's permanent `id` — don't change it after the bundle ships.
2. Add the preset files to the folder.
3. Add a new entry to the `bundles` array in `manifest.json`. Use an existing entry as a template. Required fields:
   - `id` — same as folder name
   - `name` — display name shown in the app
   - `description` — one or two sentences shown in the bundle detail view
   - `version` — start at `1.0`
   - `category` — `premiere` or `resolve`
   - `installType` — `auto` (file drop) or `manual` (sync to local folder + show import steps)
   - `presetType` — see manifest schema reference below
   - `path` — `bundles/[your-id]`
   - `files` — array of file names exactly matching the folder contents
   - `importInstructions` — required only if `installType` is `manual`
4. Update `updatedAt` at the top of the manifest.
5. Commit and push.

## Preset type reference

The `presetType` field tells MCM Vault where to install each bundle.

### Premiere Pro (`category: premiere`)

| presetType | File extensions | Installs to |
|------------|-----------------|-------------|
| `export` | `.epr` | Premiere Pro EPR folder |
| `effect` | `.prfpset` | Premiere Pro Effects Presets folder |
| `lumetri` | `.look` | Adobe Common LUTs/Creative folder |
| `lut` | `.cube`, `.3dl` | Adobe Common LUTs/Technical folder |
| `audio` | `.aup` | Audition Presets folder |
| `sequence` | `.sqpreset` | Premiere Pro Custom Settings folder |
| `mogrt` | `.mogrt` | Adobe Common Motion Graphics Templates folder |

### DaVinci Resolve (`category: resolve`)

| presetType | installType | File extensions | Behavior |
|------------|-------------|-----------------|----------|
| `lut` | `auto` | `.cube`, `.3dl` | Installs to Resolve's LUT/MCM Vault folder |
| `fusion` | `auto` | `.setting` | Installs to Fusion Templates folder |
| `fairlight` | `auto` | `.preset` | Installs to Fairlight Presets folder |
| `powergrade` | `manual` | `.drx` | Synced to ~/Documents/MCM Vault Presets/Resolve/PowerGrades/ for manual import |
| `timeline` | `manual` | `.drt` | Synced for manual import via Media Pool |
| `project` | `manual` | `.drp` | Synced for manual import via Project Manager |
| `render` | `manual` | `.xml` | Synced for manual import on Deliver page |

## Maintainer checklist

Before pushing changes, verify:

- [ ] Bundle folder name matches the `id` in the manifest exactly
- [ ] `files` array lists every file in the folder (and nothing extra)
- [ ] `version` was bumped if anything changed
- [ ] `updatedAt` was refreshed
- [ ] `manifest.json` is valid JSON (no trailing commas, correct quotes)
- [ ] If adding a new bundle with `installType: manual`, you wrote `importInstructions`

## Releasing the desktop app

The desktop app itself (the Tauri-built MCM Vault binary) is distributed separately via GitHub Releases on the `mcm-vault` repo, not this one. This repo only contains preset content.
