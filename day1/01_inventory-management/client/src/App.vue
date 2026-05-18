<template>
  <div class="app" :class="{ 'sidebar-collapsed': sidebarCollapsed }">
    <aside class="sidebar">
      <button class="sidebar-toggle" @click="toggleSidebar" :title="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'">
        <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
          <path d="M8 2L4 7L8 12" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M12 2L8 7L12 12" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>

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
        <router-link to="/" :class="{ active: $route.path === '/' }" :data-tooltip="t('nav.overview')">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <rect x="1" y="1" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
              <rect x="9" y="1" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
              <rect x="1" y="9" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
              <rect x="9" y="9" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>

        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" :data-tooltip="t('nav.inventory')">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <path d="M2 5L8 2L14 5V11L8 14L2 11V5Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
              <path d="M8 2V14M2 5L8 8L14 5" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link to="/orders" :class="{ active: $route.path === '/orders' }" :data-tooltip="t('nav.orders')">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <path d="M2 2H3.5L5.5 10H12L13.5 5H4.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <circle cx="6" cy="13" r="1" fill="currentColor"/>
              <circle cx="11" cy="13" r="1" fill="currentColor"/>
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>

        <router-link to="/spending" :class="{ active: $route.path === '/spending' }" :data-tooltip="t('nav.finance')">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <circle cx="8" cy="8" r="6.5" stroke="currentColor" stroke-width="1.5"/>
              <path d="M8 4V5M8 11V12M10 6.5C10 5.67 9.1 5 8 5C6.9 5 6 5.67 6 6.5C6 7.33 6.9 8 8 8C9.1 8 10 8.67 10 9.5C10 10.33 9.1 11 8 11C6.9 11 6 10.33 6 9.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>

        <router-link to="/demand" :class="{ active: $route.path === '/demand' }" :data-tooltip="t('nav.demandForecast')">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <path d="M2 12L6 7L9 10L14 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M11 4H14V7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link to="/reports" :class="{ active: $route.path === '/reports' }" data-tooltip="Reports">
          <span class="nav-icon">
            <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
              <rect x="2" y="1.5" width="12" height="13" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
              <path d="M5 5.5H11M5 8H11M5 10.5H8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
          </span>
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

    <div class="content-area">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

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

<script>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    const sidebarCollapsed = ref(window.innerWidth < 1024)
    const toggleSidebar = () => { sidebarCollapsed.value = !sidebarCollapsed.value }
    const handleResize = () => { if (window.innerWidth < 1024) sidebarCollapsed.value = true }

    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)
        if (isMockTask) {
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) currentUser.value.tasks.splice(index, 1)
        } else {
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)
        if (mockTask) {
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) apiTasks.value[index] = updatedTask
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(() => {
      loadTasks()
      window.addEventListener('resize', handleResize)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', handleResize)
    })

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      sidebarCollapsed,
      toggleSidebar
    }
  }
}
</script>

<style>
/* ─── Design Tokens ─────────────────────────── */
:root {
  --sidebar-width: 240px;
  --sidebar-collapsed-width: 64px;
  --sidebar-bg: #0f172a;
  --sidebar-border: #1e293b;
  --sidebar-text: #94a3b8;
  --sidebar-text-active: #f1f5f9;
  --sidebar-hover-bg: #1e293b;
  --sidebar-active-bg: #1d4ed8;
  --sidebar-active-glow: rgba(29, 78, 216, 0.25);

  --content-bg: #f8fafc;
  --card-bg: #ffffff;
  --card-border: #e2e8f0;
  --card-radius: 10px;

  --text-primary: #0f172a;
  --text-secondary: #64748b;
  --text-muted: #94a3b8;

  --space-xs: 0.375rem;
  --space-sm: 0.625rem;
  --space-md: 1.25rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
}

/* ─── Reset ──────────────────────────────────── */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--content-bg);
  color: var(--text-primary);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* ─── App Shell ──────────────────────────────── */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
  --current-sidebar-width: var(--sidebar-width);
}

.app.sidebar-collapsed {
  --current-sidebar-width: var(--sidebar-collapsed-width);
}

/* ─── Sidebar ────────────────────────────────── */
.sidebar {
  width: var(--current-sidebar-width);
  min-width: var(--current-sidebar-width);
  background: var(--sidebar-bg);
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  z-index: 200;
  border-right: 1px solid var(--sidebar-border);
  transition: width 0.25s ease, min-width 0.25s ease;
}

/* ─── Sidebar Toggle Button ──────────────────── */
.sidebar-toggle {
  position: absolute;
  top: 1.375rem;
  right: -13px;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  background: var(--sidebar-bg);
  border: 1px solid var(--sidebar-border);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: var(--sidebar-text);
  transition: background 0.15s ease, color 0.15s ease, border-color 0.15s ease;
  z-index: 10;
  flex-shrink: 0;
}

.sidebar-toggle:hover {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-active);
  border-color: #475569;
}

.sidebar-toggle svg {
  transition: transform 0.25s ease;
  flex-shrink: 0;
}

.sidebar-collapsed .sidebar-toggle svg {
  transform: rotate(180deg);
}

.sidebar-header {
  padding: 1.375rem 1.25rem 1rem;
  border-bottom: 1px solid var(--sidebar-border);
  flex-shrink: 0;
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
  letter-spacing: -0.05em;
}

.logo-text {
  display: flex;
  flex-direction: column;
  min-width: 0;
  overflow: hidden;
  max-width: 180px;
  opacity: 1;
  transition: max-width 0.25s ease, opacity 0.15s ease;
}

.sidebar-collapsed .logo-text {
  max-width: 0;
  opacity: 0;
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
  margin-top: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* ─── Sidebar Nav ────────────────────────────── */
.sidebar-nav {
  flex: 1;
  padding: 0.75rem;
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
  transition: background 0.15s ease, color 0.15s ease, padding 0.25s ease;
  position: relative;
  white-space: nowrap;
}

/* Collapsed: center icons, remove gap */
.sidebar-collapsed .sidebar-nav a {
  padding: 0.625rem;
  justify-content: center;
  gap: 0;
}

/* CSS tooltip shown only when collapsed */
.sidebar-collapsed .sidebar-nav a::after {
  content: attr(data-tooltip);
  position: absolute;
  left: calc(100% + 0.625rem);
  top: 50%;
  transform: translateY(-50%);
  background: #1e293b;
  color: #f1f5f9;
  padding: 0.375rem 0.75rem;
  border-radius: 6px;
  font-size: 0.8rem;
  font-weight: 500;
  white-space: nowrap;
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.15s ease;
  z-index: 300;
  border: 1px solid #334155;
}

.sidebar-collapsed .sidebar-nav a:hover::after {
  opacity: 1;
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
  flex-shrink: 0;
  opacity: 0.75;
}

.sidebar-nav a:hover .nav-icon,
.sidebar-nav a.active .nav-icon {
  opacity: 1;
}

.nav-label {
  overflow: hidden;
  max-width: 160px;
  opacity: 1;
  transition: max-width 0.25s ease, opacity 0.15s ease;
}

.sidebar-collapsed .nav-label {
  max-width: 0;
  opacity: 0;
}

/* ─── Sidebar Footer ─────────────────────────── */
.sidebar-footer {
  padding: 0.75rem;
  border-top: 1px solid var(--sidebar-border);
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex-shrink: 0;
}

/* Override light-bg button styles for dark sidebar (global rules target scoped children) */
.sidebar-footer .language-button,
.sidebar-footer .profile-button {
  background: transparent;
  border-color: #334155;
  color: var(--sidebar-text);
}

.sidebar-footer .language-button:hover,
.sidebar-footer .profile-button:hover {
  background: var(--sidebar-hover-bg);
  border-color: #475569;
  color: var(--sidebar-text-active);
}

.sidebar-footer .profile-name,
.sidebar-footer .language-label {
  color: var(--sidebar-text);
}

.sidebar-footer .globe-icon,
.sidebar-footer .chevron {
  color: var(--sidebar-text);
}

/* Dropdowns open upward and left-anchored from sidebar footer */
.sidebar-footer .dropdown-menu {
  top: auto;
  bottom: calc(100% + 0.5rem);
  right: auto;
  left: 0;
}

/* Collapsed footer: stack vertically, icon-only buttons */
.sidebar-collapsed .sidebar-footer {
  flex-direction: column;
  align-items: center;
  padding: 0.5rem;
  gap: 0.375rem;
}

.sidebar-collapsed .sidebar-footer .language-label,
.sidebar-collapsed .sidebar-footer .profile-name,
.sidebar-collapsed .sidebar-footer .chevron {
  display: none;
}

.sidebar-collapsed .sidebar-footer .language-button,
.sidebar-collapsed .sidebar-footer .profile-button {
  padding: 0.375rem;
  justify-content: center;
  width: 36px;
  height: 36px;
  border-radius: 8px;
}

/* Collapsed footer dropdowns open to the right */
.sidebar-collapsed .sidebar-footer .dropdown-menu {
  left: calc(100% + 0.5rem);
  bottom: 0;
  top: auto;
  transform: none;
}

/* ─── Content Area ───────────────────────────── */
.content-area {
  margin-left: var(--current-sidebar-width);
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  transition: margin-left 0.25s ease;
}

.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
}

/* ─── Page Layout Utilities ──────────────────── */
.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: var(--text-primary);
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--text-secondary);
  font-size: 0.938rem;
}

/* ─── Stats Grid ─────────────────────────────── */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: var(--card-bg);
  padding: 1.25rem;
  border-radius: var(--card-radius);
  border: 1px solid var(--card-border);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: var(--text-secondary);
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value { color: #ea580c; }
.stat-card.success .stat-value { color: #059669; }
.stat-card.danger .stat-value  { color: #dc2626; }
.stat-card.info .stat-value    { color: #2563eb; }

/* ─── Card ───────────────────────────────────── */
.card {
  background: var(--card-bg);
  border-radius: var(--card-radius);
  padding: 1.25rem;
  border: 1px solid var(--card-border);
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid var(--card-border);
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.025em;
}

/* ─── Table ──────────────────────────────────── */
.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f8fafc;
  border-top: 1px solid var(--card-border);
  border-bottom: 1px solid var(--card-border);
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #f1f5f9;
  color: #334155;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

/* ─── Badge ──────────────────────────────────── */
.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success    { background: #d1fae5; color: #065f46; }
.badge.warning    { background: #fed7aa; color: #92400e; }
.badge.danger     { background: #fecaca; color: #991b1b; }
.badge.info       { background: #dbeafe; color: #1e40af; }
.badge.increasing { background: #d1fae5; color: #065f46; }
.badge.decreasing { background: #fecaca; color: #991b1b; }
.badge.stable     { background: #e0e7ff; color: #3730a3; }
.badge.high       { background: #fecaca; color: #991b1b; }
.badge.medium     { background: #fed7aa; color: #92400e; }
.badge.low        { background: #dbeafe; color: #1e40af; }

/* ─── States ─────────────────────────────────── */
.loading {
  text-align: center;
  padding: 3rem;
  color: var(--text-secondary);
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
