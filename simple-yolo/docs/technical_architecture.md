# Technical Architecture — To-Do App

**Project**: Single-File HTML To-Do Application  
**Version**: 1.0  
**Last Updated**: 2026-02-27

## Overview

A self-contained, single-file to-do application built with vanilla HTML, CSS, and JavaScript. The app provides task management functionality with browser-based persistence using localStorage, featuring a responsive gray-tone design with smooth animations.

## Architecture

### High-Level Structure

```
index.html (single file)
├── <head>
│   ├── Meta tags (charset, viewport)
│   └── <style> — All CSS embedded
└── <body>
    ├── Container (semantic HTML structure)
    │   ├── Header (h1 title)
    │   ├── Form (task input)
    │   └── List (task display)
    └── <script> — All JavaScript embedded
```

**Architectural Pattern**: Single-page application (SPA) with no framework, following a simple Model-View pattern:
- **Model**: In-memory JavaScript array (`todos`) synced with localStorage
- **View**: DOM manipulation via vanilla JavaScript
- **No Controller**: Direct event handlers on DOM elements

### Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Markup | HTML5 | Semantic structure, accessibility |
| Styling | CSS3 | Responsive layout, animations, gray-tone theme |
| Logic | Vanilla JavaScript (ES6+) | Task CRUD operations, state management |
| Persistence | Browser localStorage API | Client-side data persistence |

**No external dependencies** — zero npm packages, no CDN links, no build tools.

## Data Model

### Task Object Structure

```javascript
{
  id: String,        // Unique identifier (timestamp-based: Date.now().toString())
  text: String,      // Task description (user input, trimmed)
  completed: Boolean // Completion status (default: false)
}
```

### State Management

**In-Memory State:**
```javascript
let todos = []; // Array of task objects
```

**Persistent Storage:**
- **Key**: `'todos-app-data'`
- **Format**: JSON stringified array of task objects
- **Location**: Browser localStorage (5-10MB limit, varies by browser)
- **Sync Strategy**: Eager — save immediately after every mutation (add, toggle, delete)

**State Flow:**
```
Page Load → loadFromStorage() → todos array → renderTasks() → DOM

User Action → State Mutation → saveToStorage() → renderTasks() → DOM
```

## Components

### 1. LocalStorage Utilities

**Functions:**
- `loadFromStorage()` — Reads and parses todos from localStorage, returns empty array on error
- `saveToStorage()` — Stringifies and saves todos, logs errors (quota exceeded, disabled localStorage)

**Error Handling:**
- Try-catch blocks around all localStorage operations
- Graceful degradation: app continues with session-only memory if localStorage fails
- Console warnings for quota exceeded errors

### 2. Task Management Functions

**`createTask(text)`**
- Creates new task object with unique ID (Date.now())
- Returns: `{ id, text, completed: false }`

**`addTask(text)`**
- Validates input (rejects empty/whitespace-only)
- Appends new task to `todos` array
- Saves to localStorage
- Triggers re-render

**`toggleTask(taskId)`**
- Finds task by ID, flips `completed` boolean
- Saves to localStorage
- Triggers re-render

**`deleteTask(taskId)`**
- Filters task out of `todos` array
- Saves to localStorage
- Triggers re-render

### 3. Rendering Engine

**`renderTasks()`**
- **Strategy**: Clear and re-render (full DOM replacement)
- **Rationale**: Simple, effective for small lists (<1000 tasks); no virtual DOM needed
- **Process**:
  1. Clear `todoList.innerHTML`
  2. Loop through `todos` array
  3. Call `createTaskElement(task)` for each
  4. Append to DOM

**`createTaskElement(task)`**
- Creates `<li>` with `.todo-item` class
- Adds `.completed` class if task is done
- Appends task text `<span>` with click handler for toggle
- Conditionally appends delete button (trash icon) if completed
- Returns HTMLElement ready for insertion

### 4. Event Handling

**Form Submission (`handleFormSubmit`):**
- `e.preventDefault()` to avoid page reload
- Extracts input value
- Calls `addTask(text)`
- Clears input field
- Re-focuses input for continued entry

**Task Toggle (click on `.todo-text`):**
- Directly calls `toggleTask(task.id)` via inline event listener

**Task Deletion (click on `.delete-btn`):**
- `e.stopPropagation()` to prevent triggering toggle
- Calls `deleteTask(task.id)`

## Styling Architecture

### CSS Organization

```
CSS Custom Properties (color palette, spacing)
     ↓
Base Styles & Reset
     ↓
Layout Components (container, form, list)
     ↓
Interactive States (hover, focus, completed)
     ↓
Animations & Transitions
```

### Gray-Tone Color Palette

| Variable | Value | Usage |
|----------|-------|-------|
| `--color-bg` | #f5f5f5 | Page background |
| `--color-container` | #ffffff | Card/container background |
| `--color-border` | #e0e0e0 | Dividers, input borders |
| `--color-text-primary` | #212121 | Headings, active text |
| `--color-text-secondary` | #616161 | Placeholders, hints |
| `--color-accent` | #9e9e9e | Focus states, completed text |

**Additional Colors:**
- Red (#f44336) — Destructive action (delete button)

### Responsive Design

**Approach**: Mobile-first with single breakpoint

**Breakpoint**: `600px`
- **Below 600px** (mobile): Full-width container, no border-radius, smaller heading
- **Above 600px** (desktop): Centered 600px container, border-radius, box-shadow

**Adaptations:**
- Font sizes: 28px → 32px (h1)
- Container: full height → centered with margin
- Form: always horizontal (flex), but input/button scale naturally

### Animations

**Animation Types:**

1. **Fade-in (new tasks)**:
   - Keyframe: `fadeIn` — opacity 0→1, translateY -10px→0
   - Duration: 0.3s
   - Easing: ease-out

2. **State Transitions**:
   - Background, color, opacity: 0.3s
   - Input focus (border, shadow): 0.2s
   - Button hover (opacity, transform): 0.2s

**Performance Optimization:**
- Animating `opacity` and `transform` only (GPU-accelerated)
- Avoiding animations on `width`, `height`, `margin` (causes layout reflows)

## Data Flow

### Task Creation Flow

```
User types text → Clicks "Add" or presses Enter
         ↓
handleFormSubmit() triggered
         ↓
addTask(text) validates input
         ↓
createTask(text) generates task object
         ↓
Push to todos array
         ↓
saveToStorage() persists to localStorage
         ↓
renderTasks() updates DOM
         ↓
Input field cleared and focused
```

### Task Completion Flow

```
User clicks task text
         ↓
toggleTask(taskId) finds task in array
         ↓
Flip task.completed boolean
         ↓
saveToStorage() persists change
         ↓
renderTasks() re-creates DOM
         ↓
Completed task displays:
  - Gray background
  - Strikethrough text
  - Trash icon appears
```

### Task Deletion Flow

```
User clicks trash icon on completed task
         ↓
e.stopPropagation() prevents toggle trigger
         ↓
deleteTask(taskId) filters array
         ↓
saveToStorage() persists change
         ↓
renderTasks() removes task from DOM
```

## Persistence Strategy

### LocalStorage Schema

**Key**: `'todos-app-data'`

**Value Example**:
```json
[
  {
    "id": "1709049600000",
    "text": "Buy groceries",
    "completed": false
  },
  {
    "id": "1709049610000",
    "text": "Write documentation",
    "completed": true
  }
]
```

### Sync Behavior

**Eager Write Strategy:**
- Every mutation immediately calls `saveToStorage()`
- Trade-off: Slight performance overhead for guaranteed persistence
- Alternative (not used): Debounced writes — would save on write volume but risk data loss

**Initial Load:**
- `loadFromStorage()` called once at script initialization
- `renderTasks()` called once after initial load to display persisted tasks

**Multi-Tab Behavior:**
- Changes in one tab do NOT auto-sync to other open tabs (localStorage events not implemented)
- Refreshing any tab will load the latest data

### Error Scenarios

| Scenario | Behavior |
|----------|----------|
| localStorage disabled | Logs error, continues with session-only memory |
| Quota exceeded | Logs warning, continues with in-memory data |
| Corrupted JSON | Returns empty array, starts fresh |
| Browser private mode | Works normally (most browsers support localStorage in private mode) |

## Security Considerations

**Threats Addressed:**

1. **XSS (Cross-Site Scripting)**:
   - **Mitigation**: Using `textContent` (not `innerHTML`) for user input
   - All task text rendered via `textContent`, which auto-escapes HTML

2. **Data Privacy**:
   - **Limitation**: localStorage is NOT encrypted
   - **Recommendation**: Do not store sensitive information

3. **Storage Injection**:
   - **Risk**: Malicious script could modify localStorage directly
   - **Impact**: Limited to client-side; no backend, no other users affected

**No Authentication/Authorization**: Single-user, client-only app. No server, no user accounts.

## Performance Characteristics

### Benchmarks (Estimates)

| Metric | Value | Notes |
|--------|-------|-------|
| Initial Load Time | <50ms | Single HTML file, no network requests |
| Task Creation | ~10ms | Array push + localStorage write + re-render |
| Task Toggle | ~10ms | Array mutation + localStorage write + re-render |
| Render 100 tasks | ~20ms | Full DOM replacement with 100 `<li>` elements |
| LocalStorage Read | <5ms | Synchronous operation, fast for small data |
| LocalStorage Write | <5ms | Synchronous operation, negligible for JSON <100KB |

### Scalability

**Task Limit (Practical):**
- **UI Performance**: Smooth up to ~500 tasks
- **Storage Limit**: 5-10MB → ~10,000 tasks (assuming 500 bytes/task including JSON overhead)
- **Recommended Maximum**: 1,000 tasks

**Bottlenecks:**
- Re-rendering entire list on every change (O(n) operation)
- No virtualization (all tasks rendered even if off-screen)

**Future Optimization Paths** (not implemented):
- Virtual scrolling for large lists
- Incremental DOM updates (only modify changed elements)
- Background localStorage writes (Web Workers)

## Browser Compatibility

### Minimum Requirements

| Feature | Minimum Browser Version |
|---------|------------------------|
| ES6 (arrow functions, `let`/`const`, template literals) | Chrome 51+, Firefox 54+, Safari 10+, Edge 15+ |
| Flexbox | Chrome 29+, Firefox 28+, Safari 9+, Edge 12+ |
| localStorage API | Chrome 4+, Firefox 3.5+, Safari 4+, IE 8+ |
| CSS Custom Properties | Chrome 49+, Firefox 31+, Safari 9.1+, Edge 15+ |
| CSS Animations | Chrome 43+, Firefox 16+, Safari 9+, Edge 12+ |

**Minimum Supported**: Modern browsers from 2016+

**Not Supported**:
- IE 11 and below (no ES6, no CSS custom properties)
- Opera Mini (limited JavaScript support)

### Tested Browsers

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Future Enhancements

Potential features not currently implemented:

1. **Task Editing**: Click task to enter edit mode
2. **Task Reordering**: Drag-and-drop to reorder tasks
3. **Categories/Tags**: Organize tasks by category
4. **Due Dates**: Add date/time deadlines
5. **Filtering**: Show active/completed tasks only
6. **Export/Import**: Download/upload tasks as JSON
7. **Undo/Redo**: Action history management
8. **Cloud Sync**: Multi-device sync via backend API
9. **Dark Mode**: Toggle theme preference
10. **Accessibility Improvements**: Enhanced ARIA labels, keyboard shortcuts

## Development Guidelines

### Adding a New Feature

1. Update data model if needed (`createTask`, task object structure)
2. Add business logic function (e.g., `editTask()`)
3. Update `createTaskElement()` to include new UI elements
4. Add event handlers
5. Update `saveToStorage()` / `loadFromStorage()` if schema changes
6. Add CSS styles for new elements
7. Test manually across multiple screen sizes

### Modifying Styles

- Use CSS custom properties for colors and spacing (defined in `:root`)
- Follow existing animation durations (0.2s for hover, 0.3s for state transitions)
- Test on mobile (320px) and desktop (1920px) breakpoints

### Debugging

**Common Issues:**

| Problem | Solution |
|---------|----------|
| Tasks not persisting | Check browser console for localStorage errors |
| Animations not working | Verify browser supports CSS animations (DevTools) |
| Tasks not rendering | Check `renderTasks()` logic, inspect `todos` array in console |
| Styling broken | Validate CSS custom properties are applied |

**Developer Tools:**
- **Console**: Check for JavaScript errors, localStorage warnings
- **Application Tab**: Inspect localStorage key `'todos-app-data'`
- **Elements Tab**: Inspect DOM structure, verify classes applied
- **Device Emulation**: Test responsive layout at various sizes

## Deployment

### Hosting Options

**Zero-Configuration Hosting** (recommended):
1. GitHub Pages
2. Netlify
3. Vercel
4. Surge.sh

**Process**: Upload `index.html` as a single file — no build step required.

### Production Checklist

- [ ] Validate HTML with W3C Validator
- [ ] Test in target browsers (Chrome, Firefox, Safari, Edge)
- [ ] Test on mobile devices (iOS Safari, Chrome mobile)
- [ ] Verify localStorage functionality (add, toggle, delete, refresh)
- [ ] Test responsive layout (320px, 768px, 1920px)
- [ ] Check console for errors
- [ ] Verify animations are smooth (60fps target)

## Maintenance

**Regular Updates:**
- None required (no dependencies to update)

**Monitoring:**
- No analytics or error tracking built-in
- Consider adding Google Analytics or Plausible if usage tracking desired

**Versioning:**
- Update `<meta>` tag or add version comment in HTML
- Consider using Git tags for release versions

## Appendix

### File Structure

```
project-root/
├── index.html                  # Single-file application (all code)
└── .spec-lite/                 # Spec-lite documentation (optional)
    ├── plan_todo_app.md        # Technical plan
    ├── features/               # Feature specifications
    │   ├── feature_html_structure.md
    │   ├── feature_task_creation.md
    │   ├── feature_task_state.md
    │   └── feature_persistence_animations.md
    └── yolo_state.md           # YOLO run state
```

### Code Statistics

- **Total Lines**: ~250 lines
  - HTML: ~30 lines
  - CSS: ~120 lines
  - JavaScript: ~100 lines
- **File Size**: ~8KB uncompressed

### References

- [MDN Web Docs — localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
- [MDN Web Docs — CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations)
- [Google Material Design — Motion](https://material.io/design/motion)

---

**Document Version**: 1.0  
**Last Updated**: 2026-02-27
