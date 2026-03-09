# AGENTS.md

This guide is for agentic coding assistants working on the Firefly codebase.

## Development Commands

```bash
# Development
pnpm dev              # Start dev server
pnpm build            # Production build (icons + astro + pagefind)
pnpm preview          # Preview production build

# Code Quality
pnpm check            # Astro project check
pnpm type-check       # TypeScript type checking with noEmit
pnpm lint             # Run Biome linter with auto-fix
pnpm format           # Format code with Biome

# Utilities
pnpm new-post         # Create new blog post
pnpm icons            # Generate icon assets
```

**Note**: This project does not use automated tests. No single-test command is available.

## Code Style Guidelines

### Formatting
- **Indentation**: Tabs (configurable in Biome)
- **Quotes**: Double quotes for all strings
- **Formatter**: Biome (not Prettier)
- Run `pnpm format` before committing changes

### Biome Configuration
- Lint rules: Recommended + strict style rules
- Auto-organize imports enabled
- Key rules enforced:
  - `noParameterAssign`, `useAsConstAssertion`, `useDefaultParameterLast`
  - `useSingleVarDeclarator`, `noInferrableTypes`, `noUselessElse`
- Svelte/Astro/Vue overrides: Relaxed unused variable/import rules

### TypeScript
- Strict mode enabled (`strictNullChecks: true`)
- Target: ESNext, Module: ESNext, Module Resolution: bundler
- Declaration files generated (`declaration: true`)
- Path aliases (all start with `@`):
  - `@components/*` → `src/components/*`
  - `@utils/*` → `src/utils/*`
  - `@constants/*` → `src/constants/*`
  - `@i18n/*` → `src/i18n/*`
  - `@layouts/*` → `src/layouts/*`
  - `@/*` → `src/*`

### Naming Conventions
- **Files**: kebab-case for components and utilities (e.g., `date-utils.ts`, `Profile.astro`)
- **Components**: PascalCase for component names
- **Functions**: camelCase
- **Constants**: UPPER_SNAKE_CASE (e.g., `BANNER_HEIGHT`)
- **Types**: PascalCase, exported from `src/types/`

### Import Organization
1. Node.js built-ins (e.g., `node:crypto`, `node:fs`)
2. External packages (e.g., `astro`, `expressive-code`)
3. Astro internal packages (e.g., `astro:content`, `astro-icon`)
4. Internal packages (path alias imports, grouped by purpose)
5. Type imports (use `import type { ... }` when possible)
6. Relative imports (avoid when possible; use path aliases)

Biome auto-organizes imports. Example:
```ts
import { createCipheriv } from "node:crypto";
import type { GetStaticPaths } from "astro";
import { siteConfig } from "@/config";
import formatDateI18n from "@/utils/date-utils";
```

## File Structure Patterns

### Components
- **Astro**: Use `---` frontmatter for component scripts
- **Svelte**: Use Svelte 5 syntax with `<script lang="ts">` and `$props()`
- Icons: Use `Icon` component for Astro, inline SVG component for Svelte

### Configuration
- All configs located in `src/config/`
- Export both types and values from respective files
- Centralized exports via `src/config/index.ts`

### Utilities
- Located in `src/utils/`
- Pure functions preferred
- Named exports primarily (common named exports: `get*`, `format*`, `is*`)

### Plugins
- Custom remark/rehype plugins in `src/plugins/`
- Export default plugin functions using `export function remarkX()` or `export function rehypeX()`
- Add JSDoc comments for plugin purpose

## Framework Specifics

### Astro
- Use `getStaticPaths` for dynamic routing
- Access props via `Astro.props` in frontmatter
- Use `getCollection('posts')` for content collections
- For client-side scripts, use `<script>` blocks (regular JS, not TS)

### Svelte
- Use `$props()` for component props (Svelte 5 runes)
- Use `$derived()` for computed values
- Types interfaces defined in component scripts

### Error Handling
- Use TypeScript types for API responses
- For crypto operations, proper Buffer handling (see `crypto-utils.ts`)
- Environment checks: `if (typeof window === "undefined")` for SSR safety

## Content Architecture

### Blog Posts
- Frontmatter defined in `src/content.config.ts`
- Custom remark plugins extract data (excerpt, reading time, etc.)
- Draft posts only included in dev mode: `import.meta.env.PROD ? data.draft !== true : true`

### Internationalization
- All strings use `i18n(I18nKey)` function
- Language files: `src/i18n/languages/*.ts`
- Keys defined in `src/i18n/i18nKey.ts`

## Security Considerations
- Email protection: Base64 encoding in `rehype-email-protection.mjs`
- Content encryption: `src/utils/crypto-utils.ts` uses AES-256-GCM
- Avoid exposing secrets in commits
- Sanitize user input with `sanitize-html`

## Package Management
- Required: `pnpm@9.14.4` (enforced via preinstall hook)
- Never use npm or yarn

## Build Pipeline
1. Generate icons (`scripts/generate-icons.js`)
2. Astro build (Vite-based)
3. Pagefind search index generation
