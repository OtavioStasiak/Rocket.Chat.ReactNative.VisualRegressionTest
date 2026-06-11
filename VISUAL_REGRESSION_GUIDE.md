# Visual Regression — Baseline Reference Guide

This repository holds the **baseline reference screenshots** for Rocket.Chat React
Native, used for visual regression testing. It is not the app source — it mirrors the
app's **view structure** and stores one approved screenshot per view, per device, per
orientation.

The baselines here are the "source of truth". A regression run captures fresh
screenshots from the app and diffs them against the matching file in this repo.

---

## Folder organization

The layout is **view → device → orientation**:

```
app/views/
└── <ViewName>/
    ├── iphone-16-pro/
    │   ├── portrait.png
    │   └── landscape.png
    ├── iphone-se-3/
    │   ├── portrait.png
    │   └── landscape.png
    └── pixel-7-pro/
        ├── portrait.png
        └── landscape.png
```

Concrete example:

```
app/views/RoomView/
├── iphone-16-pro/
│   ├── portrait.png
│   └── landscape.png
├── iphone-se-3/
│   ├── portrait.png
│   └── landscape.png
└── pixel-7-pro/
    ├── portrait.png
    └── landscape.png
```

### Why this layout

- **View-first** mirrors the app's `app/views/` structure exactly, so a view's
  baselines live where you'd expect to find that screen. There is one folder per view
  (75 today).
- **Device second** keeps everything for one device together — useful when reviewing or
  re-capturing a single device, and easy to glob (`*/iphone-se-3/*`).
- **Orientation as the file** keeps the leaf simple: a folder bottoms out in
  `portrait.png` / `landscape.png`.

---

## Devices

The device tokens match the simulators/emulators used in CI
(`.github/workflows/maestro-ios.yml`, `.github/workflows/maestro-android.yml`):

| Token            | Platform | Device                     | Reference resolution (px) | Why it's tracked            |
| ---------------- | -------- | -------------------------- | ------------------------- | --------------------------- |
| `iphone-16-pro`  | iOS      | iPhone 16 Pro              | 1206 × 2622               | Current-gen tall iPhone     |
| `iphone-se-3`    | iOS      | iPhone SE (3rd generation) | 750 × 1334                | Low-height / small device   |
| `pixel-7-pro`    | Android  | Pixel 7 Pro (API 34)       | 1440 × 3120               | Android baseline            |

Each device folder carries **both `portrait.png` and `landscape.png`**.

> Add a new device by introducing a new token folder (e.g. `pixel-8/`) under each view.
> Use the same kebab-case convention and pin a concrete model + resolution here.

---

## Naming rules

- **View folder** — exact PascalCase name as it appears in the app's `app/views/`
  (e.g. `RoomView`, `RoomsListView`, `AccessibilityAndAppearanceView`). Views that are a
  single file in the app (e.g. `AttachmentView.tsx`) still get a folder here
  (`AttachmentView/`).
- **Device folder** — lowercase kebab-case, from the table above.
- **Orientation file** — exactly `portrait.png` or `landscape.png`. PNG only.
- Not every device needs every orientation present from day one, but the **filenames
  never change**: if a view has no landscape baseline yet, the file is simply absent —
  it is never renamed.

---

## What counts as a "view"

A view = one navigable screen in the app, named after its folder/file under
`app/views/`. Two app entries are intentionally **excluded** because they are not views:

- `Styles.ts` — shared style helper.
- `__snapshots__/` — Jest snapshot artifacts.

`CannedResponseDetail` is **included** even though its name doesn't end in `View`, since
it is a real navigable screen.

---

## Adding or updating a baseline

1. Capture the screen on the target device/orientation.
2. Drop it at `app/views/<ViewName>/<device-token>/<orientation>.png`, creating the
   device folder if needed.
3. Review the image visually before committing — once merged it becomes the truth that
   every future run is diffed against.
4. Commit with a message describing what changed and why (e.g. design update vs. fixing
   a stale baseline).

> Empty view folders currently hold a `.gitkeep` so git tracks them. Remove the
> `.gitkeep` once a folder has real baselines in it.

---

## Conventions summary

| Level       | Rule                                  | Example                  |
| ----------- | ------------------------------------- | ------------------------ |
| View        | PascalCase, matches `app/views/`      | `RoomView/`              |
| Device      | kebab-case, pinned model              | `iphone-16-pro/`         |
| Orientation | `portrait.png` or `landscape.png`     | `landscape.png`          |
| Format      | PNG                                   | —                        |

Full path: `app/views/RoomView/iphone-16-pro/portrait.png`
