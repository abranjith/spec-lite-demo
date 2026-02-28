# To-Do App

A beautiful, minimalist to-do application built with vanilla HTML, CSS, and JavaScript. No frameworks, no dependencies, just a single HTML file.

![Gray-tone design with smooth animations](https://img.shields.io/badge/Design-Gray%20Tone-gray)
![Single file](https://img.shields.io/badge/Architecture-Single%20File-blue)
![No dependencies](https://img.shields.io/badge/Dependencies-Zero-green)

## Features

✨ **Simple & Intuitive**
- Create tasks with a clean, minimal interface
- Mark tasks as complete with a single click
- Delete completed tasks permanently

💾 **Persistent Storage**
- Tasks automatically save to browser localStorage
- Data survives page refreshes and browser restarts
- No backend required — everything runs in your browser

🎨 **Professional Design**
- Elegant gray-tone color palette
- Smooth animations and transitions
- Responsive layout (works on mobile and desktop)

📱 **Fully Responsive**
- Mobile-first design
- Works seamlessly from 320px to 1920px+ screens
- Touch-friendly interface

## Quick Start

1. **Download**: Save [`index.html`](index.html) to your computer
2. **Open**: Double-click the file to open it in your browser
3. **Use**: Start adding tasks immediately!

That's it. No installation, no npm, no build process.

## Usage

### Creating Tasks

1. Type your task in the input field
2. Press **Enter** or click the **Add** button
3. Your task appears instantly in the list below

### Completing Tasks

- Click on any task text to mark it as complete
- Completed tasks are grayed out with a strikethrough
- A trash icon (🗑️) appears for completed tasks

### Deleting Tasks

- Click the **trash icon** on a completed task to delete it permanently
- Tasks can only be deleted after they're marked as complete

### Data Persistence

- All tasks are automatically saved to your browser's localStorage
- Refresh the page or close the browser — your tasks will be there when you return
- Data stays on your device; nothing is sent to a server

## Technical Details

### Built With

- **HTML5** — Semantic markup
- **CSS3** — Flexbox layout, animations, custom properties
- **Vanilla JavaScript (ES6+)** — No frameworks, no libraries
- **localStorage API** — Browser-based persistence

### Browser Support

Works in all modern browsers:
- Chrome 51+
- Firefox 54+
- Safari 10+
- Edge 15+

*(IE11 and below are not supported due to ES6 and CSS custom properties)*

### Architecture

- **Single-file application**: Everything contained in one HTML file
- **No external dependencies**: No CDN links, no npm packages, no build tools
- **Client-side only**: No server required, runs entirely in the browser
- **File size**: ~8KB uncompressed

### Performance

- Initial load: <50ms
- Supports hundreds of tasks without performance degradation
- Smooth 60fps animations
- Instant localStorage sync

## Customization

### Changing Colors

Edit the CSS custom properties in the `<style>` section:

```css
:root {
  --color-bg: #f5f5f5;              /* Page background */
  --color-container: #ffffff;       /* Card background */
  --color-border: #e0e0e0;          /* Borders */
  --color-text-primary: #212121;    /* Text color */
  --color-text-secondary: #616161;  /* Secondary text */
  --color-accent: #9e9e9e;          /* Accents */
}
```

### Changing Animation Speed

Adjust transition durations in the CSS:

```css
.todo-item {
  animation: fadeIn 0.3s ease-out;  /* Change 0.3s to your preference */
}
```

### Disabling Animations

Add this CSS to disable all animations:

```css
*, *::before, *::after {
  animation: none !important;
  transition: none !important;
}
```

## Privacy & Security

- **Local-only**: All data stays on your device
- **No tracking**: No analytics, no cookies, no external requests
- **No accounts**: No sign-up, no passwords, completely anonymous
- **localStorage limitation**: Data is not encrypted — don't store sensitive information

## Limitations

- **Single-user**: No multi-device sync (data stays on one browser)
- **No editing**: Can't edit tasks after creation (must delete and re-create)
- **No categories**: All tasks in a single list
- **No due dates**: Simple task list only

These are intentional design choices to keep the app simple and focused.

## Deployment

### Option 1: Local Use

Just open `index.html` in your browser. No server needed.

### Option 2: Web Hosting

Upload `index.html` to any static hosting service:

- **GitHub Pages**: Push to a repo, enable Pages in settings
- **Netlify**: Drag and drop the file to netlify.com
- **Vercel**: Import the file via vercel.com
- **Surge.sh**: `surge index.html`

No build step required — just upload the single HTML file.

## For Developers

### Project Structure

```
index.html          # Entire application (HTML + CSS + JS)
```

### Code Organization

Inside `index.html`:

1. **HTML** — Semantic structure (header, form, list)
2. **CSS** — Organized sections with custom properties
3. **JavaScript** — Modular functions with JSDoc comments

### Data Model

Tasks are stored as JSON in localStorage:

```javascript
{
  id: "1709049600000",      // Unique timestamp-based ID
  text: "Buy groceries",    // User input
  completed: false          // Completion status
}
```

### Adding Features

See [docs/technical_architecture.md](docs/technical_architecture.md) for detailed technical documentation, including:

- Architecture overview
- Data flow diagrams
- API reference
- Performance characteristics
- Browser compatibility
- Development guidelines

## License

This project is open source and available under the [MIT License](LICENSE).

## Contributing

This is a simple demonstration project. Feel free to fork and modify for your own use!

Suggested enhancements:
- Task editing functionality
- Drag-and-drop reordering
- Categories or tags
- Due dates and reminders
- Export/import functionality
- Dark mode toggle

## Acknowledgments

Built as a demonstration of:
- Clean, minimal web design
- Vanilla JavaScript best practices
- Progressive enhancement principles
- Single-file application architecture

---

**Made with ❤️ and vanilla JavaScript** | [View Documentation](docs/technical_architecture.md)
