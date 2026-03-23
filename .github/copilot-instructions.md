# Copilot Instructions — NovaTech Solutions

## Commands

```bash
npm run dev           # Start dev server at http://localhost:3000
npm run test          # Run all tests (unit + e2e)
npm run test:unit     # Unit tests only (Node built-in test runner)
npm run test:e2e      # E2E tests only (Playwright — auto-starts dev server)
npm run test:e2e:ui   # Playwright with interactive UI
npm run lint          # ESLint on src/js/
npm run lint:fix      # ESLint with auto-fix
npm run format        # Prettier on all src/ HTML/CSS/JS
```

**Run a single unit test file:**
```bash
node --test tests/unit/validation.test.js
```

**Run a single E2E test file:**
```bash
npx playwright test tests/e2e/contact-form.spec.js
```

## Architecture

Static HTML site with no build step or framework. Files are served directly from `src/` via `npx serve`.

**CSS layering order** (each page loads all of these):
`variables.css` → `base.css` → `components.css` → `src/css/pages/{page}.css`

**JS module pattern** — each module in `src/js/` exports an `init*` function and auto-initializes at the bottom via a `DOMContentLoaded` check:
```js
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', initFoo);
} else {
  initFoo();
}
```

**Validation flow** — `validation.js` is the shared utility. `contact-form.js` imports from it. `validateField` reads HTML attributes (`required`, `type`, `minLength`, `maxLength`) directly from the DOM element and updates UI in-place.

**Portfolio filtering** — uses `data-category` on `.project-card` elements and `data-filter` on `.filter-btn` buttons; no state beyond DOM classes.

## Key Conventions

**State classes** use `is-*` prefix: `is-open` (nav menu), `is-hidden` (filtered-out cards), `is-invalid` (form fields).

**Error element IDs** follow `{fieldId}-error` pattern (e.g., `#email` → `#email-error`). Validation writes directly to these elements' `textContent`.

**CSS custom properties** — all values come from `variables.css`. Never hardcode colors, spacing, shadows, or z-index values; use the tokens defined there (e.g., `--color-primary-600`, `--spacing-4`, `--z-sticky`).

**BEM naming** — CSS classes follow `.block__element--modifier`. State modifier classes use `is-*` (not BEM modifiers) for JS-toggled states.

**JS module structure** — each module defines `SELECTORS`, `CLASSES`, and `MESSAGES` constant objects at the top before any functions.

**Unit tests** use Node's built-in `node:test` + `node:assert` — not Jest or Vitest. Do not import from testing frameworks.

**E2E tests** use `baseURL: http://localhost:3000` and reference pages as `/pages/{page}.html`.

**Commit format:** `type: description` using Conventional Commits (`feat`, `fix`, `refactor`, `docs`, `style`, `perf`, `test`, `chore`).

## Current Focus Areas

- Accessibility (WCAG 2.1 AA) — ARIA labels required on interactive elements
- Form validation enhancements
- Mobile navigation refinements
