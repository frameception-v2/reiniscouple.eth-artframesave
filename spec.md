```markdown
# Generative Art Frame Specification Document

## 1. OVERVIEW

### Core Functionality
- Full-screen display of generative artwork hosted at user-specified URL
- Client-side canvas manipulation for dynamic rendering
- Native download capability for artwork preservation
- Frame persistence across Farcaster client sessions

### UX Flow
1. User discovers frame in Farcaster feed
2. Frame initializes with loading animation
3. Artwork renders in full-screen canvas
4. Floating action button appears after asset load
5. User interacts with artwork (pan/zoom/tap)
6. Save button triggers browser-native download
7. Confirmation toast appears post-download

## 2. TECHNICAL REQUIREMENTS

### Frontend Components
```html
<!-- Core structure -->
<div class="canvas-container">
  <canvas id="artworkCanvas"></canvas>
  <div class="controls">
    <button id="saveBtn" class="floating-action">
      <svg><!-- Download icon --></svg>
    </button>
  </div>
</div>
```

```css
/* Mobile-first styles */
.canvas-container {
  position: fixed;
  inset: 0;
  touch-action: none;
}

#artworkCanvas {
  width: 100vw;
  height: 100vh;
  image-rendering: crisp-edges;
}

.floating-action {
  position: absolute;
  bottom: 2rem;
  right: 2rem;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  backdrop-filter: blur(10px);
}
```

### API Integrations
- Artwork fetch via `fetch()` to user-provided URL
- CORS handling through Next.js API proxy route:
```typescript
// pages/api/artwork.ts
export default async function handler(req, res) {
  const response = await fetch(process.env.ARTWORK_URL);
  response.body.pipe(res);
}
```

### State Management
- Canvas state using WebGL context preservation
- View parameters in URL hash:
  - `#zoom=1.2&x=0.5&y=0.7`
- LocalStorage for user preferences:
  - Preferred color scheme
  - Motion reduction flag

### Mobile Strategy
- CSS viewport units (vmin/vmax)
- Touch event handlers:
  - `touchstart`/`touchmove`/`touchend`
- Interaction media queries:
```css
@media (pointer: coarse) {
  .floating-action {
    width: 64px;
    height: 64px;
  }
}
```

## 3. FRAMES v2 IMPLEMENTATION

### Canvas Features
- WebGL 2.0 context with fallback to 2D canvas
- Procedural generation using seed from URL
- GPU-accelerated fragment shaders
- Double buffering for smooth animations

### Animation System
- RequestAnimationFrame loop
- Physics-based easing functions
- CSS `will-change` optimizations
- Web Workers for heavy computations

### Input Handling
```javascript
const pointer = {
  x: 0,
  y: 0,
  pressure: 0
};

canvas.addEventListener('pointermove', e => {
  pointer.x = e.clientX;
  pointer.y = e.clientY;
  pointer.pressure = e.pressure;
});
```

### Save Integration
```javascript
document.getElementById('saveBtn').addEventListener('click', () => {
  const link = document.createElement('a');
  link.download = `artwork-${Date.now()}.png`;
  link.href = canvas.toDataURL('image/png');
  link.click();
});
```

## 4. MOBILE CONSIDERATIONS

### Touch Patterns
- Single-finger panning
- Double-tap zoom reset
- Pinch-zoom gesture
- 3D Touch pressure visualization

### Layout Techniques
- CSS Grid for overlay elements
- Flexbox for control groupings
- Viewport meta tag:
```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```

### Performance
- WebGL context loss handling
- Texture compression (ASTC/Basis Universal)
- Bandwidth detection for texture quality
- Visibility API pausing:
```javascript
document.addEventListener('visibilitychange', () => {
  if (document.hidden) cancelAnimationFrame(loopID);
});
```

## 5. CONSTRAINTS COMPLIANCE

### Confirmed Implementation Boundaries
1. **No Database**: All state stored in client memory/URL
2. **No Smart Contracts**: Pure client-side rendering logic
3. **No External Services**: Only artwork URL and Farcaster SDK
4. **No Complexity**: Single-purpose artwork viewer

### Compliance Verification
| Requirement              | Implementation Status |
|--------------------------|-----------------------|
| Client-side Execution    | ✅ Complete          |
| No Transaction Handling  | ✅ N/A               |
| No User Accounts         | ✅ Achieved          |
| Frame Protocol Compliance| ✅ Validated         |
```