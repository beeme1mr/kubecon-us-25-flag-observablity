# Copilot Instructions for KubeCon Feature Flag Observability Presentation

## Project Overview
This is a **Slidev-based presentation** for KubeCon + CloudNativeCon 2025 about integrating feature flag observability into modern workflows using OpenFeature and OpenTelemetry standards. The talk is titled "From 'What Broke' to 'What Changed': Feature Flags as First-Class Signals in Observability."

## Architecture & Structure

### Core Framework: Slidev
- **Main presentation file**: `slides.md` (1500+ lines of presentation content)
- **Theme**: Seriph theme with dark color scheme
- **Interactive elements**: Vue components with `<v-clicks>` and progressive reveals
- **Build system**: Uses Vite internally, builds to `dist/` directory

### File Organization Pattern
```
slides.md           # Main presentation content with YAML frontmatter
components/         # Vue components (Counter.vue for interactive demos)
pages/              # Additional slide pages (imported-slides.md)  
snippets/          # Code examples referenced in slides (external.ts)
uno.config.ts       # UnoCSS configuration (atomic CSS framework)
tailwind.config.js  # Legacy Tailwind config (converted to UnoCSS)
ABSTRACT.md        # Conference talk abstract
NARRATIVE.md       # Detailed presentation flow and timing
LLMS.txt            # AI assistance for sli.dev
```### Development Workflow
- **Start dev server**: `pnpm dev` (opens automatically on localhost:3030)
- **Production build**: `pnpm build` → `dist/` directory
- **Export slides**: `pnpm export` (for PDF/static export)
- **Package manager**: Uses `pnpm` (with `bun.lock` for faster installs)

## Key Patterns & Conventions

### Slide Content Structure
- **YAML frontmatter** defines theme, title, layout configuration
- **Progressive disclosure** using `<v-clicks>` and `<v-after>` components
- **Image placeholders** with descriptive alt text: `[IMAGE PLACEHOLDER: description]`
- **Code blocks** with syntax highlighting and line highlighting: `{all|3|4-6|all}`
- **Mermaid diagrams** embedded directly in markdown

### Vue Component Usage
- Components in `components/` are auto-imported into slides
- Example: `<Counter />` component demonstrates interactive presentation elements
- Props passed from slide context: `<Counter :count="5" />`

### Code Snippet Management
- Reusable code examples in `snippets/` directory
- Use `#region snippet` and `#endregion snippet` to mark exportable sections
- Import with: `@@include snippets/external.ts#snippet`

### Presentation Timing & Flow
- **Total duration**: 25 minutes with 2 speakers
- **Detailed timing** documented in `NARRATIVE.md`
- **Speaker notes** in HTML comments: `<!-- Note content -->`

## Technical Context

### OpenFeature & OpenTelemetry Integration
The presentation demonstrates feature flag observability patterns:
- **OpenFeature SDK** with hook system for extensibility
- **OpenTelemetry semantic conventions** for consistent attribute naming
- **Trace-level observability** showing flag evaluations in distributed traces

### Target Audience
- Engineering teams using feature flags for releases, A/B tests, permissions
- DevOps/SRE teams interested in observability improvements
- Conference attendees familiar with cloud-native technologies

## Deployment Configuration

### Multi-Platform Support
- **Netlify**: `netlify.toml` with SPA routing and Node.js 20
- **Vercel**: `vercel.json` with similar SPA configuration
- Both platforms build with `npm run build` and serve from `dist/`

### Content Themes
The presentation follows a narrative arc:
1. Problem identification (observability gap with feature flags)
2. Evolution of solutions (manual → automated → trace-level)
3. Standards collaboration (OpenFeature + OpenTelemetry)
4. Progressive delivery transformation
5. Call to action for community involvement

## Development Guidelines

### When Editing Slides
- Maintain YAML frontmatter structure at the top of `slides.md`
- Use semantic slide layouts: `center`, `two-cols`, `section`, `default`
- Keep progressive disclosure logical with `v-click` ordering
- Update speaker notes for complex slides

### When Adding Components
- Place in `components/` directory for auto-import
- Use Vue 3 Composition API with `<script setup>`
- Follow existing styling patterns with utility classes

### When Styling Content
- **CSS Framework**: Uses UnoCSS (atomic CSS, Tailwind-compatible)
- **Configuration**: Extend `uno.config.ts` for custom utilities and shortcuts
- **Common patterns**: Use `safelist` for presentation-specific utilities
- **Custom shortcuts**: Defined for slide layouts (`slide-content`, `slide-title`)
- **Color system**: Custom gray/purple palettes with CSS variable integration

### When Modifying Deployment
- Test locally with `pnpm dev` before deploying
- Ensure both Netlify and Vercel configs stay synchronized
- Verify SPA routing works for direct slide access