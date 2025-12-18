# 🚕 NYC Taxi Data Analysis Presentation

A professional and engaging Slidev presentation with a New York City taxi theme, featuring animated taxi transitions and sound effects.

## Features

- 🚖 **Animated Taxi Transitions** - Watch a taxi drive across the screen between slides
- 📯 **NYC Sound Effects** - Authentic taxi horn sounds on slide transitions
- 🌃 **NYC Skyline Theme** - Dark city-inspired color scheme with yellow taxi accents
- 📊 **Graph Placeholders** - Ready-to-fill areas for your data visualizations
- 🏅 **Medallion Architecture** - Visual representation of the data pipeline

## Quick Start

```bash
# Install dependencies
npm install

# Start the presentation (opens in browser)
npm run dev
```

## Controls

- **Space** or **→** - Next slide
- **←** - Previous slide
- **f** - Toggle fullscreen
- **o** - Toggle overview mode
- **d** - Toggle dark mode

## Adding Your Graphs

Look for the placeholder sections in `slides.md`:

```html
<div class="graph-placeholder">
  <span>[ Insert Your Chart Here ]</span>
</div>
```

Replace with your actual chart components or images.

## Customization

### Colors
Edit `style.css` to change the theme colors:
```css
:root {
  --nyc-yellow: #FFD700;  /* Taxi yellow */
  --nyc-orange: #FFA500;  /* Accent color */
  --nyc-dark: #1a1a2e;    /* Background */
}
```

### Sound Effects
The horn sounds are generated programmatically in `global-top.vue`. Adjust volume or disable by modifying the `gainNode.gain.setValueAtTime()` value.

### Taxi Animation
Customize the taxi animation in `components/TaxiTransition.vue`.

## Project Structure

```
data_engineering_demo/
├── slides.md           # Main presentation content
├── style.css           # Custom NYC theme styles
├── global-top.vue      # Sound effects on slide change
├── components/
│   └── TaxiTransition.vue  # Animated taxi component
├── setup/
│   └── main.ts         # Slidev setup
└── package.json
```

## Building for Production

```bash
# Build static files
npm run build

# Export to PDF
npm run export
```

## Credits

- **Team 4** - Data Engineering Demo
- Built with [Slidev](https://sli.dev)

---

🗽 *Enjoy your journey through NYC taxi data!* 🚕
