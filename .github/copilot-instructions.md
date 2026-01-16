# SvelteKit Project Instructions

## Tech Stack & Architecture

- **Svelte 5** (latest) with **SvelteKit 2** framework
- **TypeScript** with strict mode enabled
- **Vite 7** for bundling and development
- Uses `vite-plugin-devtools-json` for enhanced development experience
- File-based routing in `src/routes/`
- Component library in `src/lib/`

## Svelte 5 Specifics

**This project uses Svelte 5 - use modern Svelte 5 syntax:**
- Use runes: `$state()`, `$derived()`, `$effect()`, `$props()` instead of legacy reactive declarations
- Use `{@render children()}` instead of `<slot />`
- Props: `let { propName } = $props()` instead of `export let propName`
- See [+layout.svelte](../src/routes/+layout.svelte) for `$props()` and `{@render}` pattern

## MCP Server Integration

**IMPORTANT**: This project has access to the Svelte MCP server (see [AGENTS.md](../AGENTS.md)):
1. Always call `list-sections` FIRST when working with Svelte/SvelteKit features
2. Use `get-documentation` to fetch ALL relevant sections for the task
3. MUST use `svelte-autofixer` on ALL Svelte code before showing to user
4. Only offer `playground-link` after user confirmation and never for files written to project

## Development Workflows

```bash
npm run dev              # Start dev server (port 5173)
npm run build            # Production build
npm run preview          # Preview production build
npm run check            # Type-check with svelte-check
npm run check:watch      # Type-check in watch mode
```

## File Organization Conventions

```
src/
  lib/              # Shared components, utilities, assets
    assets/         # Static assets imported in code (e.g., favicon.svg)
    index.ts        # Library exports
  routes/           # File-based routing (SvelteKit convention)
    +page.svelte    # Route pages
    +layout.svelte  # Layout components
static/             # Public static files served at root
```

## Key Configuration Details

- **Path alias**: `$lib` resolves to `src/lib/` (SvelteKit default)
- **Adapter**: `@sveltejs/adapter-auto` (automatically detects deployment platform)
- **Preprocessing**: `vitePreprocess()` handles TypeScript, SCSS, etc.
- **TypeScript**: Extends generated `.svelte-kit/tsconfig.json` with strict settings
- **Module resolution**: Uses `"bundler"` mode (ESM imports with `.ts` extensions rewritten)

## TypeScript Patterns

- Enable `rewriteRelativeImportExtensions` - write `.ts` extensions in imports
- Strict mode enabled: null checks, proper typing required
- Use `lang="ts"` in `<script>` tags for component scripts
- Type safety enforced via `checkJs: true` and `strict: true`

## Common Patterns

- Favicon imported and set via `<svelte:head>` in root layout (see [+layout.svelte](../src/routes/+layout.svelte#L8-L9))
- Children rendered using `{@render children()}` pattern in layouts
- Props destructured from `$props()` in Svelte 5 style

## When Adding Features

1. Use MCP server tools for Svelte 5 documentation lookup
2. Run `svelte-autofixer` on all component code
3. Place shared components in `src/lib/`
4. Use SvelteKit's file-based routing for new pages
5. Run `npm run check` to verify type safety
