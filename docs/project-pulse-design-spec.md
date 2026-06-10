# Project Pulse — Design Specification

This document defines the UI, structure, visual tokens and accessibility requirements for the Project Pulse static dashboard. Save as: docs/project-pulse-design-spec.md

## Purpose
A compact, polished project dashboard that highlights project cards, status badges, priority, and key metadata. The design emphasizes readability, clear information hierarchy, and responsive behavior.

## Wireframe (first view)
- Page header (dashboard title, brief subtitle, global actions).
- Controls row: search input + filters (status, priority) + "New project" CTA.
- Main content: responsive card grid of project cards (primary view).
- Each project card shows:
  - Header: project title + status badge (right).
  - Subtitle / metadata row: owner, due date, tags.
  - Description: 1–2 lines summary.
  - Footer: priority pill, small progress bar, avatars or avatars placeholder, action icon(s).

Visual affordances:
- Cards have medium radius (8px), subtle shadow, white/soft-surface background on a light page background.
- Status badges are small rounded rectangles with color-coded fills and high-contrast text.
- Priority represented as a bold left-accent pill for quick scanning.
- Clear spacing and typography with readable sizes and line-heights.

## Visual design tokens

Colors (CSS variable names + hex):
- --pp-bg: #F6F8FA (page background)
- --pp-surface: #FFFFFF (card surface)
- --pp-primary: #0066FF (primary / interactive)
- --pp-accent: #7C5DFF (accent)
- --pp-text: #0B1A2B (body text)
- --pp-muted: #6B7A8A (muted text / meta)
- --pp-success: #1EAD66 (success)
- --pp-warning: #FF9F1A (warning)
- --pp-danger: #E53E3E (danger)
- --pp-shadow: rgba(11, 26, 43, 0.08) (card shadow)
- --pp-border: #E6EDF3 (subtle border)
- --pp-focus: #FFB84D (focus ring)

Typography
- Font stack: "Inter", system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif
- Type scale:
  - --type-h1: 20px / 28px (dashboard title)
  - --type-h2: 16px / 22px (card title)
  - --type-body: 14px / 20px
  - --type-meta: 12px / 16px
- Use font-weight 600 for titles and labels, 400–500 for body text.

Spacing & geometry
- Border radius: 8px for cards, 999px for pills / badges
- Card padding: 16px
- Gutter: 16px (mobile) → 24px (desktop)
- Shadow: 0 6px 20px var(--pp-shadow)

## Component CSS hooks (exact class names to use)
- Page / layout:
  - .dashboard (root container)
  - .dashboard__header
  - .dashboard__controls
  - .dashboard__list
- Card:
  - .project-card
  - .project-card__header
  - .project-card__title
  - .project-card__meta
  - .project-card__description
  - .project-card__footer
  - .project-card__priority
  - .project-card__progress
- Utilities / badges:
  - .badge
  - .badge--success
  - .badge--warning
  - .badge--danger
  - .badge--neutral
  - .priority
  - .priority--high
  - .priority--medium
  - .priority--low
  - .avatars
  - .avatar
  - .sr-only
  - .btn
  - .btn--primary
  - .input
- Accessibility & states:
  - focus states use :focus and :focus-visible on interactive elements
  - .is-loading (for disabled states)

Coder notes: Use semantic HTML: header, main, nav, form, article (each .project-card can be an <article>), button elements for actions, <progress> for progress where applicable. Provide alt text for avatars.

## Breakpoints
- Mobile (default): viewport width up to 599px
  - Single column card layout, stacked header controls
- Tablet: 600px — 899px
  - 2-column grid when space allows
- Desktop: 900px and above
  - Grid with auto-fit columns, min card width 280px. Use container max-width 1200px and centered layout.

Suggested grid:
- .dashboard__list { grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 20px; }

## Accessibility checklist (must be satisfied)
- Keyboard navigable: All actions reachable via keyboard (Tab order logical). Focusable elements have visible focus styles.
- Focus visibility: Use :focus-visible with at least 3:1 contrast ring (recommend using --pp-focus).
- Semantic markup: Use header/main/article/section/aside and heading elements in descending order (h1 for page title, h2/h3 for cards as appropriate).
- ARIA labels: Use aria-label or aria-labelledby for non-text controls (icons, avatar groups).
- Color contrast: All text on primary surfaces must meet WCAG AA (4.5:1 for normal text; for large text 3:1). Status badges and priority pills must maintain readable text contrast; provide border or stroke if needed.
- Resize and responsive: Layout should remain usable at 125% and 200% zoom and with large text settings.
- Reduced motion: Honor prefers-reduced-motion: reduce animations and transitions when set.
- Images & avatars: Provide alt or aria-hidden for decorative images. Avatar initials fallback must be text-accessible.
- Form controls & labels: Inputs have associated labels, or aria-label/aria-labelledby.
- Progress accessibility: Use <progress> or an aria-valuenow/aria-valuemin/aria-valuemax attributes on custom progress.
- Announcements: Use polite ARIA live region for asynchronous updates (filters, saves).
- Skip link: Provide a skip-to-content link for keyboard users.

## Interaction patterns & microcopy
- Status badge examples: "Active", "At Risk", "On Hold", "Complete".
- Priority pills: "High", "Medium", "Low" — High uses stronger color and bold weight.
- Hover & focus: For cards, increase shadow and translateY(-2px) on hover (reduced motion applied).
- Empty state: Show friendly illustration / message with CTA to create project.

## Validation & testing recommendations
- Test with keyboard-only navigation and screen reader (NVDA/VoiceOver).
- Run automated contrast testing (e.g., axe, Lighthouse) to verify color contrast.
- Check responsive at breakpoints and at 320px width.
- Verify focus ring is visible in both light and dark UA themes.

## Files touched by implementation
- app/index.html — page structure (use provided hooks)
- app/styles.css — visual styles (this file)
- app/project-data.json — sample data (use to populate cards)
- .vscode/launch.json — dev settings
