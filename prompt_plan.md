# Farcaster Generative Art Frame Implementation Plan

## Implementation Strategy
**Core Objective**: Build mobile-first generative art viewer compliant with Farcaster Frames v2 spec  
**Stack**: Next.js (TypeScript), WebGL 2.0, CSS Modules  
**Key Components**:
1. Canvas rendering system
2. Artwork proxy API
3. View state management
4. Mobile interaction handlers
5. Download functionality

**Challenges**:
- Cross-browser WebGL support
- Touch gesture parity with mouse
- URL state synchronization
- Mobile performance optimization

---

## Phase 1: Foundation Setup

### Prompt 1: Initialize Canvas Structure
```text
Create a new page component at /app/artwork/page.tsx that:
1. Uses the existing layout template
2. Sets viewport meta tag to disable scaling
3. Creates full-screen canvas container
4. Adds basic CSS styles for mobile viewport
5. Imports core UI components from template

Include TypeScript types for canvas ref and component props.
```

### Prompt 2: WebGL Context Initialization
```text
Create a utils/webgl.ts file that:
1. Gets WebGL2 context with fallback to 2D
2. Handles context lost/restored events
3. Creates resize observer for canvas element
4. Returns cleaned-up context object

Export as getWebGLContext(canvas: HTMLCanvasElement).
```

### Prompt 3: API Proxy Route
```text
Create pages/api/artwork.ts that:
1. Reads ARTWORK_URL from environment
2. Proxies GET requests with CORS headers
3. Streams response using Node.js pipeline
4. Handles errors with appropriate status codes

Use Next.js edge runtime configuration.
```

---

## Phase 2: Core Functionality

### Prompt 4: View State Management
```text
Create hooks/useViewState.ts that:
1. Parses zoom/x/y from URL hash
2. Stores current view in useRef
3. Updates hash on view changes
4. Syncs state with localStorage
5. Handles window.hashchange events

Return [viewState, updateViewState] tuple.
```

### Prompt 5: Basic Rendering Loop
```text
Create components/CanvasRenderer.tsx that:
1. Initializes WebGL context on mount
2. Starts requestAnimationFrame loop
3. Clears canvas with dark background
4. Draws placeholder gradient
5. Implements basic pan/zoom handlers

Use useViewState hook for coordinate tracking.
```

### Prompt 6: Floating Action Button
```text
Create components/FloatingControls.tsx:
1. Position fixed button at bottom-right
2. Add SVG download icon from template
3. Implement press/hover states
4. Add mobile touch padding
5. Export controlled component props

Integrate with CSS modules for blur effect.
```

---

## Phase 3: Artwork Integration

### Prompt 7: Artwork Loading System
```text
Create hooks/useArtworkLoader.ts that:
1. Fetches from /api/artwork
2. Shows loading spinner component
3. Handles fetch errors
4. Parses texture from response
5. Updates global loading state

Return { artwork, isLoading, error } object.
```

### Prompt 8: Touch Event Handling
```text
Extend CanvasRenderer.tsx with:
1. touchstart/touchmove/touchend handlers
2. Single-finger pan calculation
3. Pinch-zoom distance detection
4. Double-tap timeout logic
5. Prevent default touch actions

Sync with view state updates.
```

### Prompt 9: Save Functionality
```text
Implement save button handler that:
1. Converts canvas to DataURL
2. Creates virtual anchor element
3. Triggers PNG download
4. Shows success toast component
5. Tracks download analytics event

Use existing UI notification system.
```

---

## Phase 4: Optimization

### Prompt 10: WebGL Shader Integration
```text
Create shaders/fragment.glsl and vertex.glsl:
1. Basic noise-based pattern
2. Uniforms for view parameters
3. Time-based animation
4. Mobile-friendly precision

Compile in useEffect hook with error handling.
```

### Prompt 11: Performance Enhancements
```text
Add to CanvasRenderer.tsx:
1. Visibility API pause/resume
2. Texture compression detection
3. Bandwidth-aware quality settings
4. Web worker for heavy math
5. Frame rate limiter

Integrate with existing view state.
```

### Prompt 12: Final Integration
```text
Wire all components together in page.tsx:
1. Artwork loading state management
2. Error boundary implementation
3. View state synchronization
4. Mobile gesture coordination
5. Accessibility labels

Export final component with TypeScript types.
```

---

## Validation Checklist
1. Mobile viewport behavior
2. Cross-browser WebGL support
3. URL state persistence
4. Touch gesture parity
5. Frame rate consistency
6. Download functionality
7. Error recovery flows

Each prompt builds on previous implementations with integrated testing points. Final step produces fully functional artwork frame ready for Farcaster integration.