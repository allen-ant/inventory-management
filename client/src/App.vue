<template>
  <div class="app-shell" :class="{ collapsed }">
    <aside class="sidebar">
      <div class="brand-row">
        <div class="brand">
          <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
          <span class="subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <button
          class="collapse-toggle"
          type="button"
          :title="collapsed ? 'Expand sidebar' : 'Collapse sidebar'"
          aria-label="Toggle sidebar"
          @click="toggleSidebar"
        >
          <svg class="chevron" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="15 18 9 12 15 6" />
          </svg>
        </button>
      </div>

      <div class="sidebar-scroll">
      <nav class="side-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }" :title="t('nav.overview')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="3" width="7" height="7" rx="1" />
            <rect x="14" y="3" width="7" height="7" rx="1" />
            <rect x="3" y="14" width="7" height="7" rx="1" />
            <rect x="14" y="14" width="7" height="7" rx="1" />
          </svg>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" :title="t('nav.inventory')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z" />
            <polyline points="3.27 6.96 12 12.01 20.73 6.96" />
            <line x1="12" y1="22.08" x2="12" y2="12" />
          </svg>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }" :title="t('nav.orders')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2" />
            <rect x="8" y="2" width="8" height="4" rx="1" />
            <line x1="9" y1="12" x2="15" y2="12" />
            <line x1="9" y1="16" x2="15" y2="16" />
          </svg>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }" :title="t('nav.finance')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="9" />
            <path d="M15 9.5c0-1.1-1.34-2-3-2s-3 .9-3 2 1.34 2 3 2 3 .9 3 2-1.34 2-3 2-3-.9-3-2" />
            <line x1="12" y1="6" x2="12" y2="7.5" />
            <line x1="12" y1="16.5" x2="12" y2="18" />
          </svg>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }" :title="t('nav.demandForecast')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="3 17 9 11 13 15 21 7" />
            <polyline points="15 7 21 7 21 13" />
          </svg>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }" title="Reports">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <line x1="6" y1="20" x2="6" y2="14" />
            <line x1="12" y1="20" x2="12" y2="8" />
            <line x1="18" y1="20" x2="18" y2="4" />
            <line x1="3" y1="20" x2="21" y2="20" />
          </svg>
          <span class="nav-label">Reports</span>
        </router-link>
      </nav>
      </div>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="main-col">
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
import { ref, onMounted, computed } from 'vue'
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

    // Sidebar collapse state, persisted across sessions
    const collapsed = ref(false)
    try {
      collapsed.value = localStorage.getItem('sidebar-collapsed') === '1'
    } catch (err) {
      // localStorage unavailable (e.g. privacy mode) - default to expanded
    }

    const toggleSidebar = () => {
      collapsed.value = !collapsed.value
      try {
        localStorage.setItem('sidebar-collapsed', collapsed.value ? '1' : '0')
      } catch (err) {
        // Persisting is best-effort
      }
    }

    // Merge mock tasks from currentUser with API tasks
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
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(loadTasks)

    return {
      t,
      collapsed,
      toggleSidebar,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
:root {
  --sp-1: 4px; --sp-2: 8px; --sp-3: 12px; --sp-4: 16px; --sp-5: 20px;
  --sp-6: 24px; --sp-8: 32px; --sp-10: 40px; --sp-12: 48px;
  --bg: #f8fafc; --surface: #ffffff;
  --ink: #0f172a; --text: #334155; --muted: #64748b;
  --line: #e2e8f0; --line-soft: #f1f5f9;
  --accent: #2563eb; --accent-soft: #eff6ff; --accent-hover: #1d4ed8;
  --radius: 8px; --radius-lg: 12px;
  --shadow-sm: 0 1px 2px rgb(0 0 0 / .05);
  --shadow-md: 0 4px 12px rgb(0 0 0 / .08);
  --sidebar-w: 240px; --sidebar-w-collapsed: 64px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--bg);
  color: var(--text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* App shell: sidebar + main column */
.app-shell {
  display: flex;
  min-height: 100vh;
}

.sidebar {
  position: sticky;
  top: 0;
  /* sticky creates a stacking context; keep dropdowns above main-col sticky elements (FilterBar z-90) */
  z-index: 100;
  height: 100vh;
  overflow: visible;
  background: var(--surface);
  border-right: 1px solid var(--line);
  width: var(--sidebar-w);
  padding: var(--sp-4) var(--sp-3);
  display: flex;
  flex-direction: column;
  gap: var(--sp-1);
  flex-shrink: 0;
  transition: width .2s ease;
}

.brand-row {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: var(--sp-2);
  padding: var(--sp-1) var(--sp-3);
  margin-bottom: var(--sp-3);
}

.brand {
  display: flex;
  flex-direction: column;
  gap: 2px;
  min-width: 0;
}

.brand-name {
  font-size: 1rem;
  font-weight: 650;
  color: var(--ink);
  letter-spacing: -0.01em;
  line-height: 1.3;
}

.subtitle {
  font-size: 0.75rem;
  color: var(--muted);
  font-weight: 400;
  line-height: 1.4;
}

.collapse-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  flex-shrink: 0;
  background: transparent;
  border: none;
  border-radius: var(--radius);
  color: var(--muted);
  cursor: pointer;
  transition: background-color .15s, color .15s;
}

.collapse-toggle:hover {
  background: var(--line-soft);
  color: var(--ink);
}

.collapse-toggle .chevron {
  transition: transform .2s ease;
}

.sidebar-scroll {
  flex: 1;
  overflow-y: auto;
  min-height: 0;
}

.side-nav {
  display: flex;
  flex-direction: column;
  gap: var(--sp-1);
}

.side-nav a {
  display: flex;
  align-items: center;
  gap: var(--sp-3);
  padding: var(--sp-2) var(--sp-3);
  border-radius: var(--radius);
  color: var(--muted);
  font-size: .875rem;
  font-weight: 500;
  text-decoration: none;
  transition: background-color .15s, color .15s;
}

.side-nav a svg {
  flex-shrink: 0;
}

.side-nav a:hover {
  background: var(--line-soft);
  color: var(--ink);
}

.side-nav a.active {
  background: var(--accent-soft);
  color: var(--accent);
  font-weight: 600;
}

.nav-label {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-footer {
  margin-top: auto;
  border-top: 1px solid var(--line);
  padding-top: var(--sp-3);
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: var(--sp-2);
}

/* Collapsed sidebar */
.app-shell.collapsed .sidebar {
  width: var(--sidebar-w-collapsed);
  padding: var(--sp-4) var(--sp-2);
}

.app-shell.collapsed .brand-name,
.app-shell.collapsed .subtitle,
.app-shell.collapsed .nav-label {
  display: none;
}

.app-shell.collapsed .brand-row {
  justify-content: center;
  padding: var(--sp-1) 0;
}

.app-shell.collapsed .side-nav a {
  justify-content: center;
  padding: var(--sp-2);
}

.app-shell.collapsed .collapse-toggle .chevron {
  transform: rotate(180deg);
}

.app-shell.collapsed .sidebar-footer {
  align-items: center;
}

/* Narrow viewports: force icons-only regardless of toggle */
@media (max-width: 1000px) {
  .sidebar {
    width: var(--sidebar-w-collapsed);
    padding: var(--sp-4) var(--sp-2);
  }

  .brand-name,
  .subtitle,
  .nav-label {
    display: none;
  }

  .brand-row {
    justify-content: center;
    padding: var(--sp-1) 0;
  }

  .side-nav a {
    justify-content: center;
    padding: var(--sp-2);
  }

  .collapse-toggle {
    display: none;
  }

  .sidebar-footer {
    align-items: center;
  }
}

.main-col {
  flex: 1;
  min-width: 0;
}

.main-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: var(--sp-8);
}

.page-header {
  margin-bottom: var(--sp-8);
}

.page-header h2 {
  font-size: 1.625rem;
  font-weight: 700;
  color: var(--ink);
  margin-bottom: var(--sp-1);
  letter-spacing: -0.02em;
}

.page-header p {
  color: var(--muted);
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: var(--sp-5);
  margin-bottom: var(--sp-6);
}

.stat-card {
  background: var(--surface);
  padding: var(--sp-6);
  border-radius: var(--radius-lg);
  border: 1px solid var(--line);
  box-shadow: var(--shadow-sm);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: var(--shadow-md);
}

.stat-label {
  color: var(--muted);
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: var(--sp-2);
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: var(--ink);
  letter-spacing: -0.025em;
  font-variant-numeric: tabular-nums;
}

.stat-card.warning .stat-value {
  color: #ea580c;
}

.stat-card.success .stat-value {
  color: #059669;
}

.stat-card.danger .stat-value {
  color: #dc2626;
}

.stat-card.info .stat-value {
  color: var(--accent);
}

.card {
  background: var(--surface);
  border-radius: var(--radius-lg);
  padding: var(--sp-6);
  border: 1px solid var(--line);
  box-shadow: var(--shadow-sm);
  margin-bottom: var(--sp-5);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--sp-4);
  padding-bottom: var(--sp-3);
  border-bottom: 1px solid var(--line);
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--ink);
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--bg);
  border-top: 1px solid var(--line);
  border-bottom: 1px solid var(--line);
}

th {
  text-align: left;
  padding: var(--sp-3) var(--sp-4);
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: var(--sp-3) var(--sp-4);
  border-top: 1px solid var(--line-soft);
  color: var(--text);
  font-size: 0.875rem;
  font-variant-numeric: tabular-nums;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: var(--bg);
}

.badge {
  display: inline-block;
  padding: 2px var(--sp-2);
  border-radius: 999px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: #d1fae5;
  color: #065f46;
}

.badge.warning {
  background: #fed7aa;
  color: #92400e;
}

.badge.danger {
  background: #fecaca;
  color: #991b1b;
}

.badge.info {
  background: #dbeafe;
  color: #1e40af;
}

.badge.increasing {
  background: #d1fae5;
  color: #065f46;
}

.badge.decreasing {
  background: #fecaca;
  color: #991b1b;
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: #fecaca;
  color: #991b1b;
}

.badge.medium {
  background: #fed7aa;
  color: #92400e;
}

.badge.low {
  background: #dbeafe;
  color: #1e40af;
}

/* Button baseline for views to adopt */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--sp-2);
  background: var(--accent);
  color: #fff;
  border: 1px solid var(--accent);
  border-radius: var(--radius);
  padding: var(--sp-2) var(--sp-4);
  font-family: inherit;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: background-color .15s, border-color .15s;
}

.btn:hover:not(:disabled) {
  background: var(--accent-hover);
  border-color: var(--accent-hover);
}

.btn-secondary {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--sp-2);
  background: var(--surface);
  color: var(--text);
  border: 1px solid var(--line);
  border-radius: var(--radius);
  padding: var(--sp-2) var(--sp-4);
  font-family: inherit;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: background-color .15s, border-color .15s, color .15s;
}

.btn-secondary:hover:not(:disabled) {
  background: var(--line-soft);
  border-color: #cbd5e1;
  color: var(--ink);
}

.btn:focus-visible,
.btn-secondary:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
}

.loading {
  text-align: center;
  padding: var(--sp-12);
  color: var(--muted);
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: var(--sp-4);
  border-radius: var(--radius);
  margin: var(--sp-4) 0;
  font-size: 0.938rem;
}
</style>
