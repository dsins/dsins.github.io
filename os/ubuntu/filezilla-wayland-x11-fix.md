# FileZilla: Invisible Filenames & Text Glitch Fix on Ubuntu (Wayland → X11)

**Symptom:** FileZilla opens and connects, but the file list is broken — filenames invisible, text overlapping, items only appear on click and vanish on mouseover.

**Cause:** FileZilla's wxWidgets rendering library is incompatible with the Wayland display server. Affects Ubuntu 22.04+ with Wayland as default session.

---

## Fix: Force X11 Backend

### Step 1 — Test first (no permanent changes yet)

```bash
GDK_BACKEND=x11 filezilla
```

If the glitch is gone, proceed to make it permanent.

---

### Step 2 — Patch the application shortcut

```bash
sudo nano /usr/share/applications/filezilla.desktop
```

Find the line:
```
Exec=filezilla
```

Replace it with:
```
Exec=env GDK_BACKEND=x11 filezilla
```

Save: `Ctrl+O` → `Enter` → `Ctrl+X`

FileZilla will now always launch in X11 mode regardless of how you open it.

---

## Why This Works

`GDK_BACKEND=x11` tells GTK/GDK to use the XWayland compatibility layer instead of the native Wayland backend. FileZilla's wxWidgets version does not handle Wayland's rendering pipeline correctly — forcing X11 bypasses the broken code path entirely.

---

## Notes

- This fix survives FileZilla updates **only if** the `.desktop` file isn't replaced by the package manager. If glitches return after an update, reapply Step 2.
- To check if a FileZilla update reset the file: `grep Exec /usr/share/applications/filezilla.desktop`
- Alternative for Flatpak installs: set the environment variable via `flatpak override --user --env=GDK_BACKEND=x11 org.filezilla_project.FileZilla`