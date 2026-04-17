# Technical Architecture

## Overview

Single HTML file with embedded CSS and JavaScript. No build step, no dependencies beyond CDN-loaded GSAP.

## Dependencies

| Library | Version | Source | Purpose |
|---------|---------|--------|---------|
| GSAP 3 | 3.x | CDN (cdnjs) | Core animation engine |
| MotionPathPlugin | 3.x | CDN | Path-based animations |
| ScrollTrigger | 3.x | CDN | Scroll-linked hero animation |
| Webflow CSS | Production | CDN | Base layout and typography |

## Page Structure

```
<html>
├── <head>
│   ├── Webflow base CSS (CDN)
│   ├── Meta tags (noindex, nofollow for prototype)
│   └── Facebook pixel (consent: revoke)
│
├── <body>
│   ├── Navigation bar (Webflow)
│   │
│   ├── Hero section
│   │   ├── "Trusted by 400,000+" badge
│   │   ├── Heading with rotating text
│   │   ├── CTA button + G2 badge
│   │   │
│   │   └── Phone mockup (.hero-phone-container)
│   │       └── .hero-phone (CSS iPhone 16 Pro Max)
│   │           ├── .hero-phone-island (Dynamic Island)
│   │           ├── .hero-lockscreen
│   │           │   ├── Lock icon
│   │           │   ├── Time (9:41)
│   │           │   ├── Date (Thursday, April 17)
│   │           │   └── Notification (#lindy-phone-notif)
│   │           │       ├── Icon placeholder (#lindy-notif-icon) — empty until face lands
│   │           │       ├── "Lindy" + "now"
│   │           │       └── "47 handled, 2 need you, coffee?"
│   │           └── Home bar indicator
│   │
│   ├── Company logos marquee
│   ├── Features sections (from Webflow)
│   ├── Testimonials, pricing, footer (from Webflow)
│   │
│   ├── Animation overlay elements (fixed position, z-index 10000+)
│   │   ├── #chaos-vignette — edge darkening during animation
│   │   ├── #chaos-blur — frosted background blur
│   │   ├── #chaos-overlay — notification card container
│   │   ├── #face-glow — golden glow behind face
│   │   ├── #blow-rings — air puff container (legacy, unused)
│   │   ├── #air-burst — burst effect container (legacy, unused)
│   │   ├── #lindy-face-center — the animated Lindy face SVG
│   │   ├── #page-revealer — flash effect
│   │   └── #chaos-replay — replay button
│   │
│   └── <script> — main animation logic
```

## iPhone 16 Pro Max Mockup

Pure CSS implementation, no images.

### Specifications
| Property | Value |
|----------|-------|
| Aspect ratio | 430:932 (matches physical device) |
| Width | 380px (desktop), 320px (mobile) |
| Border radius | 50px outer, 46px screen |
| Border | 4px solid #1a1a1a |
| Dynamic Island | 95x28px, 20px border-radius |

### Lock Screen Wallpaper
Warm sunset gradient matching Lindy's brand gold (#d4b574):
```css
background: linear-gradient(180deg,
  #1c1e2e 0%,    /* dark charcoal */
  #2a2640 18%,   /* deep purple-grey */
  #3d3450 34%,   /* dusty mauve */
  #5e4d5a 50%,   /* warm grey */
  #8a7260 64%,   /* taupe */
  #b8976a 78%,   /* muted gold */
  #c9a96e 90%,   /* warm gold */
  #d4b574 100%   /* Lindy brand gold */
);
```

### Fade Effect
The phone container clips at `max-height: 480px` with a gradient overlay pseudo-element that blends into the page background:
```css
.hero-phone-container::after {
  background: linear-gradient(to bottom,
    rgba(254,253,244,0) 0%,        /* transparent */
    ...                             /* S-curve easing */
    #FEFDF4 100%);                 /* page background */
}
```

## Notification Design

Matches iOS 17+ notification style:
- Background: `rgba(255,255,255,0.16)` with `blur(40px) saturate(1.4)`
- Border radius: 16px
- Icon: 40x40px circular placeholder
- Font: SF Pro Text system font stack
- Layout: icon vertically centered to text block via flexbox

## Animation Engine

### Scroll-Aware Morph Target

The face-to-icon flight computes position at execution time, not at page load:

```javascript
tl.call(function() {
  var targetRect = targetIcon.getBoundingClientRect(); // live position
  var flyDX = targetX - CX;  // viewport-relative offset
  var flyDY = targetY - CY;
  gsap.to(face, { x: flyDX, y: flyDY, scale: targetScale, ... });
}, null, flyT);
```

This guarantees accuracy regardless of scroll position, viewport resize, or layout shifts.

### SVG Reparenting

When the face lands, its SVG is cloned into the DOM placeholder:

```javascript
var clone = faceSvg.cloneNode(true);
placeholder.appendChild(clone);
face.style.display = 'none';  // hide fixed overlay
```

The clone inherits the placeholder's CSS (40x40, circular clip), so it becomes a native page element that scrolls with the content.

### FOUC Prevention

Webflow elements use `opacity: 0` until GSAP initializes:
```css
.home_hero_animation > *:not(...):not(.hero-phone-container):not(style) {
  opacity: 0;
}
```

The phone container is excluded so it's visible immediately.

## Responsive Behavior

| Breakpoint | Phone width | Container height | Notes |
|------------|-------------|-----------------|-------|
| Desktop (>479px) | 380px | 480px | Full experience |
| Mobile (≤479px) | 320px | 380px | Slightly smaller phone, adjusted padding |

The animation uses viewport-relative calculations (`window.innerWidth/Height`) so notification positions and face targeting work at any size.

## Customization Guide

### Change the notification text
Find `#lindy-phone-notif` in the HTML and edit the `.notif-body` paragraph.

### Change the wallpaper
Edit `.hero-phone-screen` background gradient in the `<style>` block.

### Change the phone size
Edit `.hero-phone` width and `.hero-phone-container` max-height.

### Add the animation to a different page
Copy everything from `<!-- Animation overlay -->` styles through the closing `</script>`, plus the phone mockup HTML. Ensure GSAP 3 is loaded.

### Disable the animation
Remove the `<script>` block at the bottom and the overlay `<div>` elements. The phone mockup will still render as a static lock screen.
