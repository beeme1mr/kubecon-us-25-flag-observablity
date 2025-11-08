# From 'What Broke' to 'What Changed': Feature Flags as First-Class Concept in Observability

A presentation for **KubeCon + CloudNativeCon North America 2025** demonstrating how OpenFeature and OpenTelemetry integrate to provide trace-level observability for feature flags.

**[View the presentation →](https://beeme1mr.github.io/kubecon-us-25-flag-observablity/)**

## Speakers

- **Michael Beemer** - Dynatrace
- **Parth Suthar** - DevCycle

## Overview

Feature flags are powerful tools for modern software delivery, but they're often invisible to traditional observability tools. When an incident occurs, teams can see symptoms (error spikes, latency) but not the cause (flag changes). This creates a critical observability gap.

This presentation explores:
- **The Problem**: Why feature flags are hidden from observability tools and how this impacts MTTR
- **The Maturity Model**: 5 levels of feature flag observability, from "flying blind" to trace-level insights
- **The Solution**: How OpenFeature + OpenTelemetry provide trace-level flag observability through semantic conventions
- **Progressive Delivery**: How flag observability transforms gradual feature rollouts from theory to practice
- **Real-World Examples**: Using the OpenTelemetry Demo App to demonstrate flag-related incidents

## Key Takeaways

- Feature flags should be **first-class citizens** in observability platforms
- **Trace-level observability** allows teams to slice and dice metrics by flag name and variant
- **Open standards** (OpenFeature + OpenTelemetry) enable interoperability across tools
- Proper flag observability transforms incident response from reactive firefighting to proactive innovation

## Tech Stack

- **Framework**: [Slidev](https://sli.dev/) (Vue 3-based presentation framework)
- **Styling**: UnoCSS with custom purple/blue gradient theme
- **Charts**: Chart.js v4 with interactive visualizations
- **Icons**: Carbon icons

## Development

To run the presentation locally:

```bash
# Install dependencies
bun install

# Start the dev server
bun run dev

# Visit http://localhost:3030
```

### Build for Production

```bash
bun run build
```

### Export to PDF

```bash
bun run export
```

## Project Structure

- **`slides.md`** - Main presentation content
- **`components/`** - Custom Vue components for interactive charts and visualizations
- **`public/`** - Static assets (logos, screenshots, speaker photos)
- **`NARRATIVE.md`** - Detailed presentation flow and timing
- **`STYLES.md`** - Style guide and design patterns

## Contributing

This presentation is built with [Slidev](https://sli.dev/). To make changes:

1. Edit `slides.md` for content
2. Modify components in `components/` for interactive elements
3. Update styles in `style.css` or `uno.config.ts`
4. Preview changes with `pnpm dev`
