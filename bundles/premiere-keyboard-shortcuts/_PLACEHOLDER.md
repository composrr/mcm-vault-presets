# Premiere Pro Keyboard Shortcuts

Place your `.kys` keyboard shortcut files into the matching subfolder:

- `win/` for Windows shortcuts
- `mac/` for macOS shortcuts

The receiver installs each file into the OS-matching profile folder (`Profile-<user>\Win\` on Windows, `Profile-<user>\Mac\` on Mac), and skips the other-platform file.

The same `.kys` file is **not** portable across OSes — Premiere stores them separately. If you publish from a Windows machine, only Windows files get updated; a Mac maintainer publishes the Mac files separately. Cross-platform publishes coexist in the manifest without overwriting each other.

To export keyboard shortcuts from Premiere Pro: **Edit → Keyboard Shortcuts → Save As**. The `.kys` lands in your platform's profile folder. Copy it into `win/` or `mac/` accordingly.

The manifest tracks files with a `win/` or `mac/` prefix (e.g. `["win/mcm-default.kys", "mac/mcm-default.kys"]`); the prefix is stripped on install.
