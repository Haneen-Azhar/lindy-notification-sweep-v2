# Lindy Notification Sweep — Iteration 2

A custom landing page prototype for [Lindy.ai](https://lindy.ai) featuring an animated "notification sweep" interaction. The Lindy logo face appears, inhales all on-screen notifications, then morphs into a lock screen notification icon on an iPhone 16 Pro Max mockup.

## What This Is

This is a single-page HTML prototype built on top of Lindy's production Webflow export. It replaces the hero phone mockup with a custom iPhone 16 Pro Max lock screen and adds a full GSAP-powered sweep animation.

The animation tells a story: your notifications are overwhelming, Lindy sweeps them all up, and then calmly delivers a single summary notification — "47 handled, 2 need you, coffee?"

## Live Demo

Serve locally:

```bash
python3 -m http.server 8080
# Open http://localhost:8080
```

Click **Replay** (bottom-right) to re-run the animation.

## File Structure

```
├── index.html          # The full landing page with embedded animation
├── docs copy/          # Superpowers skill files (inherited from project)
│   └── superpowers/
├── README.md           # This file
├── ANIMATION.md        # Detailed animation breakdown
├── ARCHITECTURE.md     # Technical architecture and customization guide
└── .gitignore
```

## Key Features

- **Notification sweep animation**: 25 realistic notification cards (Slack, Gmail, iMessage, Calendar, etc.) appear across the screen, then get inhaled by the Lindy logo face
- **iPhone 16 Pro Max mockup**: Pure CSS phone with Dynamic Island, lock screen UI, and warm sunset gradient wallpaper
- **Morph-to-icon**: The animated face flies to the notification and becomes its icon, seamlessly reparenting from fixed overlay to in-page element
- **Scroll-aware targeting**: The face computes its landing position at fly time via `getBoundingClientRect()`, so it works no matter where the user has scrolled
- **Responsive**: Works across desktop, tablet, and mobile viewports
- **Fade-out effect**: The phone fades into the page background with a gradient overlay matching Lindy's cream (#FEFDF4)

## Animation Flow

1. **Notification spawn** — 25 cards appear in 5 columns across the viewport
2. **Inhale** — Cards spiral into the Lindy face's mouth with a jelly-slurp compression effect
3. **Cheeks puff** — Face inflates briefly, holding the notifications
4. **Deflate + smile** — Cheeks relax, face breaks into a big smile
5. **Wink** — Smirky wink with a smirk mouth shape
6. **Fly to phone** — Face shrinks and flies to the notification icon position on the lock screen
7. **Become icon** — Face SVG is cloned into the notification placeholder; fixed overlay hides

## Tech Stack

- **HTML/CSS**: Single file, no build step
- **GSAP 3**: Animation engine (loaded from CDN)
- **Webflow**: Base layout and styles from Lindy's production site
- **Pure CSS iPhone mockup**: No images needed for the phone frame

## Credits

Built for Lindy.ai. Animation and phone mockup by Haneen Azhar.
