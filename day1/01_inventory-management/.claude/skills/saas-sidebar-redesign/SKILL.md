---
name: saas-sidebar-redesign
description: Redesign a Vue 3 app from a horizontal top nav bar to a modern SaaS-style vertical sidebar layout with consistent spacing and polished styling. Use this skill when asked to redesign, modernize, or restyle the app's navigation and layout.
---

# SaaS Sidebar Redesign Guidelines

This skill transforms the Factory Inventory Management app from a horizontal top navbar into a modern SaaS-style layout with a fixed left sidebar, consistent spacing tokens, and a polished professional look.

## Layout Overview

**Before:** `flex-column` — top nav bar above full-width content  
**After:** `flex-row` — fixed left sidebar alongside scrollable main content area

```
┌─────────┬──────────────────────────────────┐
│  LOGO   │  [FilterBar]                     │
│         │                                  │
│  Nav    │  <router-view />                 │
│  Items  │                                  │
│         │                                  │
│─────────│                                  │
│ Profile │                                  │
│ Lang    │                                  │
└─────────┴──────────────────────────────────┘
```

## Files to Modify

| File | What Changes |
|------|-------------|
| `client/src/App.vue` | Primary target — layout, sidebar structure, global CSS |
| `client/src/components/FilterBar.vue` | Remove horizontal centering; becomes top bar inside main area |

Do NOT modify individual view files (`views/*.vue`) or other components — their internal layout stays the same.

## Step 1: Restructure the App.vue Template

Replace the current `<header class="top-nav">` + `<main>` pattern with a sidebar + content-area structure:

```vue
<template>
  <div class="app">
    <!-- Fixed left sidebar -->
    <aside class="sidebar">
      <div class="sidebar-header">
        <div class="sidebar-logo">
          <div class="logo-icon">F</div>
          <div class="logo-text">
            <span class="logo-name">{{ t('nav.companyName') }}</span>
            <span class="logo-sub">{{ t('nav.subtitle') }}</span>
          </div>
        </div>
      </div>

      <nav class="sidebar-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
          <span class="nav-icon">◻</span>
          <span class="nav-label">Reports</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <!-- Scrollable right content area -->
    <div class="content-area">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <!-- Modals stay at root -->
    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />
    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>
```

## Step 2: Replace Global CSS in App.vue

Replace the entire `<style>` block. Keep all the shared utility classes (`.card`, `.badge`, `.stat-card`, tables, etc.) but replace the layout and nav classes:

### Design Tokens

```css
:root {
  /* Sidebar */
  --sidebar-width: 240px;
  --sidebar-bg: #0f172a;
  --sidebar-border: #1e293b;
  --sidebar-text: #94a3b8;
  --sidebar-text-active: #f1f5f9;
  --sidebar-hover-bg: #1e293b;
  --sidebar-active-bg: #1d4ed8;
  --sidebar-active-glow: rgba(29, 78, 216, 0.3);

  /* Content */
  --content-bg: #f8fafc;
  --content-padding: 1.5rem 2rem;

  /* Surface */
  --card-bg: #ffffff;
  --card-border: #e2e8f0;
  --card-radius: 10px;
  --card-shadow-hover: 0 4px 12px rgba(0, 0, 0, 0.06);

  /* Typography */
  --text-primary: #0f172a;
  --text-secondary: #64748b;
  --text-muted: #94a3b8;

  /* Spacing scale */
  --space-xs: 0.375rem;
  --space-sm: 0.625rem;
  --space-md: 1.25rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
}
```

### Root and Body

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
    Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--content-bg);
  color: var(--text-primary);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

### App Shell (Sidebar + Content)

```css
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}

/* ─── Sidebar ──────────────────────────────── */
.sidebar {
  width: var(--sidebar-width);
  min-width: var(--sidebar-width);
  background: var(--sidebar-bg);
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  z-index: 200;
  border-right: 1px solid var(--sidebar-border);
}

.sidebar-header {
  padding: 1.5rem 1.25rem 1rem;
  border-bottom: 1px solid var(--sidebar-border);
}

.sidebar-logo {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.logo-icon {
  width: 34px;
  height: 34px;
  background: var(--sidebar-active-bg);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 800;
  font-size: 1rem;
  color: #fff;
  flex-shrink: 0;
}

.logo-text {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.logo-name {
  font-size: 0.875rem;
  font-weight: 700;
  color: var(--sidebar-text-active);
  letter-spacing: -0.01em;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.logo-sub {
  font-size: 0.688rem;
  color: var(--sidebar-text);
  margin-top: 1px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* ─── Sidebar Navigation ────────────────────── */
.sidebar-nav {
  flex: 1;
  padding: 0.75rem 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 2px;
  overflow-y: auto;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.875rem;
  border-radius: 8px;
  color: var(--sidebar-text);
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  transition: background 0.15s ease, color 0.15s ease;
}

.sidebar-nav a:hover {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-active);
}

.sidebar-nav a.active {
  background: var(--sidebar-active-bg);
  color: #fff;
  box-shadow: 0 0 0 1px var(--sidebar-active-glow);
}

.nav-icon {
  width: 18px;
  height: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.875rem;
  opacity: 0.8;
  flex-shrink: 0;
}

.sidebar-nav a.active .nav-icon {
  opacity: 1;
}

.nav-label {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* ─── Sidebar Footer ────────────────────────── */
.sidebar-footer {
  padding: 0.75rem;
  border-top: 1px solid var(--sidebar-border);
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

/* ─── Content Area ──────────────────────────── */
.content-area {
  margin-left: var(--sidebar-width);
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.main-content {
  flex: 1;
  padding: var(--content-padding);
}
```

### Shared Utility Classes (keep these unchanged)

Keep all of the following from the original `App.vue` `<style>` block:

- `.page-header`, `.page-header h2`, `.page-header p`
- `.stats-grid`, `.stat-card`, `.stat-label`, `.stat-value`
- `.stat-card.warning`, `.stat-card.success`, `.stat-card.danger`, `.stat-card.info`
- `.card`, `.card-header`, `.card-title`
- `.table-container`, `table`, `thead`, `th`, `td`, `tbody tr`
- `.badge` and all badge variants (`.success`, `.warning`, `.danger`, `.info`, `.increasing`, `.decreasing`, `.stable`, `.high`, `.medium`, `.low`)
- `.loading`, `.error`

Remove these classes that no longer apply:
- `.top-nav`, `.nav-container`, `.logo`, `.subtitle`
- `.nav-tabs`, `.nav-tabs a`, `.nav-tabs a:hover`, `.nav-tabs a.active`, `.nav-tabs a.active::after`

## Step 3: Update FilterBar.vue Styling

The FilterBar now sits at the top of `.content-area` instead of being a full-width block under the old nav. Update its root styles so it looks like a clean top bar with a subtle bottom border:

```css
/* In FilterBar.vue <style scoped> */
.filters-bar {
  background: #ffffff;
  border-bottom: 1px solid #e2e8f0;
  padding: 0.75rem 2rem;
  position: sticky;
  top: 0;
  z-index: 100;
}

.filters-container {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.filters-grid {
  display: flex;
  gap: 0.75rem;
  flex-wrap: wrap;
  align-items: center;
}
```

Remove any `max-width` centering constraints from `.filters-container` since the sidebar already constrains the content width naturally.

## Step 4: Nav Icons

Replace the placeholder `◻` with SVG icons or Unicode symbols that suit each section. Good minimal options using Unicode or simple SVG:

| Route | Suggested Symbol | Meaning |
|-------|-----------------|---------|
| `/` (Overview) | `⊞` or `▦` | Dashboard grid |
| `/inventory` | `☰` or `≡` | List/boxes |
| `/orders` | `◎` or `⊡` | Order circle |
| `/spending` | `$` or `¤` | Finance |
| `/demand` | `↗` or `⟋` | Trend up |
| `/reports` | `≣` or `⊟` | Document |

For a cleaner result, use inline SVG `<svg>` elements (24×24 viewBox, `currentColor` fill/stroke) from any open icon set like Heroicons or Lucide. Wrap them in a `<span class="nav-icon">` element.

## Step 5: Profile and Language in Sidebar Footer

The `ProfileMenu` and `LanguageSwitcher` move from the top nav to `.sidebar-footer`. Both components may have styles that assume a light background — check and override as needed:

```css
/* Override dark sidebar context for ProfileMenu trigger */
.sidebar-footer :deep(.profile-trigger),
.sidebar-footer :deep(.lang-trigger) {
  color: var(--sidebar-text);
  background: transparent;
}

.sidebar-footer :deep(.profile-trigger:hover),
.sidebar-footer :deep(.lang-trigger:hover) {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-active);
}
```

Use `:deep()` to pierce scoped styles of child components.

## Quality Checklist

Before considering the redesign complete, verify:

- [ ] Sidebar is fixed and does not scroll with content
- [ ] Active nav link is highlighted with blue background
- [ ] Content area scrolls independently
- [ ] FilterBar sticks to the top of the content area (not the viewport)
- [ ] No horizontal overflow on the main content
- [ ] All six nav links are present and route correctly
- [ ] Logo and subtitle are visible and readable on dark background
- [ ] ProfileMenu and LanguageSwitcher are functional in sidebar footer
- [ ] Stat cards, tables, and badges still render correctly in all views
- [ ] No CSS class name conflicts with removed top-nav styles
- [ ] `.main-content` has correct `margin-left` offset equal to sidebar width

## Common Pitfalls

**Sidebar overlaps content**: Ensure `.content-area` has `margin-left: var(--sidebar-width)` — it must equal the sidebar's fixed width exactly.

**FilterBar scrolls away**: Give `.filters-bar` `position: sticky; top: 0` and a `z-index` lower than the sidebar's `z-index` (sidebar is 200, filter bar should be ~100).

**ProfileMenu/LanguageSwitcher look broken**: They were styled for a white background. Use `:deep()` overrides in `.sidebar-footer` rather than modifying the component files.

**Nav active state has wrong bottom border**: The old `.nav-tabs a.active::after` added a bottom underline. Remove that rule entirely — the new active state uses background color instead.

**Content gets cut off on narrow screens**: Add a `min-width: 0` to `.content-area` to prevent flex children from overflowing their container.
