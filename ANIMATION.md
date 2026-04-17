# Animation Breakdown

Detailed phase-by-phase breakdown of the notification sweep animation.

## Timeline Overview

| Phase | Time (s) | Duration | Description |
|-------|----------|----------|-------------|
| 1. Spawn | 0.0 | ~0.6s | Notifications slide into 5 columns |
| 2. Lindy face appears | 0.3 | 0.3s | Face fades in at viewport center |
| 3. Inhale | 0.65 | ~1.8s | Cards spiral into the face's mouth |
| 4. Cheeks puff | 2.5 | 0.3s | Face inflates, eyes narrow |
| 5. Deflate | 2.8 | 0.2s | Cheeks relax |
| 6. Smile + wink | ~3.1 | ~0.8s | Big smile, smirky wink, hold |
| 7. Fly to icon | ~3.9 | 0.75s | Face shrinks and flies to phone |
| 8. Land + become icon | ~4.7 | 0.5s | Bounce, clone SVG, cleanup |

Total duration: ~5.2 seconds.

## Phase Details

### Phase 1: Notification Spawn

25 notification cards are created dynamically from a data array. Each card includes:
- App icon (Gmail, Slack, iMessage, Calendar, Phone, Figma, Notion, LinkedIn)
- App name, title, subtitle, timestamp
- Optional badge count
- Green checkmark (hidden initially, used in original blow phase)

Cards are distributed across 5 columns positioned like a phone lock screen. They slide in from below with staggered delays, rotating slightly for organic feel.

### Phase 2: Lindy Face Appears

The Lindy face SVG (`#lindy-face-center`) is a 220x220px fixed-position element at viewport center. It contains:
- Outer stroke circle (subtle border)
- Inner filled circle with radial gradient (#FFF8E7 to #EDE3CC)
- Nose path (the signature squiggle)
- Mouth path (animatable between smile, O-shape, smirk, etc.)
- Two eye paths (animatable between normal, wide, narrow, wink)
- Mouth hole mask (ellipse for inhale opening)

The face scales up from 0 with a back-ease and glow effect.

### Phase 3: Inhale

The most complex phase. Each card computes its distance to the mouth position and follows a 12-point keyframed path:

- **Spiral trajectory**: Cards curve toward the mouth using perpendicular wave offsets
- **Jelly slurp**: Once cards enter the "slurp zone" (55% of their journey), they compress horizontally and stretch vertically, then squish flat
- **Staggering**: Closer cards arrive first (sorted by distance), with 8ms stagger between each
- **Face stretches**: The face goes scaleX(0.93) scaleY(1.07) during inhale — tall and narrow, like sucking through a straw

Mouth opens to an O-shape, eyes widen with effort.

### Phase 4: Cheeks Puff

Quick beat where the face looks "loaded":
- Eyes narrow to squint
- Face scales to scaleX(1.12) scaleY(1.06) — wide and round
- Mouth hole closes (rx/ry to 0)
- Mouth path transitions to a closed slight smile

### Phase 5: Deflate

- Face returns to scale(1.0)
- Background blur overlay fades out

### Phase 6: Smile + Wink

- Eyes return to normal
- Mouth transitions to big smile
- After 0.35s: right eye winks (path becomes flat line), mouth shifts to asymmetric smirk
- Hold wink for 0.4s
- Eye opens, mouth returns to big smile

### Phase 7: Fly to Icon

**Scroll-aware targeting**: At the moment of flight (not at page load), the animation calls `getBoundingClientRect()` on `#lindy-notif-icon` to get the current viewport position. This means the face always finds its target regardless of scroll position.

- Glow fades out
- Face translates from viewport center to icon center
- Scale reduces from 1.0 to ~0.182 (40px icon / 220px face)
- Box shadow fades to zero
- Mouth relaxes to calm smile

### Phase 8: Land + Become Icon

- Subtle bounce: scale overshoots by 5%, then settles with elastic ease
- The face's SVG is cloned and appended into the `#lindy-notif-icon` placeholder div
- The fixed-position overlay face is hidden
- All overlay elements (blur, vignette, glow, etc.) are cleaned up
- Replay button fades in

The cloned SVG inherits the placeholder's CSS: 40x40px, border-radius 50%, overflow hidden — so it looks like a native notification icon that was always there.

## SVG Path Reference

### Mouth Shapes
| Name | Path | Used In |
|------|------|---------|
| MOUTH_SMILE | `M 169.29 186.11 C 156.97 202.72 133.53 206.19 116.92 193.87` | Default, final |
| MOUTH_O | `M 158 186 C 158 215 104 215 104 186 C 104 162 158 162 158 186` | Inhale |
| MOUTH_BIG_SMILE | `M 175 186 C 160 218 100 218 95 186` | Post-wink |
| MOUTH_SMIRK | `M 175 186 C 158 214 102 210 100 192` | During wink |
| MOUTH_CLOSED | `M 147 197 C 147 200 115 200 115 197 C 115 194 147 194 147 197` | Puffed cheeks |

### Eye Shapes
| Name | Path | Used In |
|------|------|---------|
| EYE_L_NORMAL | `M 54.55 102.30 L 75.33 86.78 L 93.67 107.97` | Default |
| EYE_R_NORMAL | `M 148.71 115.96 L 169.50 100.43 L 187.84 121.63` | Default |
| EYE_R_WINK | `M 150 114 L 168 109 L 186 114` | Wink (flat line) |
| EYE_L_WIDE | Scaled up version | Inhale effort |
| EYE_L_NARROW | Compressed version | Puffed cheeks |

## Customization

### Changing animation speed
All timing is relative. Adjust `inhaleT`, `puffT`, `smileT`, `flyT` variables in the script to shift phases.

### Adding/removing notifications
Edit the `DATA` array in the script. Each entry needs: `t` (icon type), `app`, `title`, `sub`, `time`, and optional `badge`.

### Changing the morph target
The face targets whatever element has `id="lindy-notif-icon"`. Move this element anywhere on the page and the animation will find it.
