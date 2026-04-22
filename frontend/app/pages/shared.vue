<template>
  <div class="bs-page">

    <!-- ── Minimal header ── -->
    <header class="bs-header">
      <div class="bs-header-inner">
        <div class="bs-logo">
          <div class="app-logo-icon" style="background:linear-gradient(135deg,#6366f1,#4f46e5);font-size:20px;width:38px;height:38px;border-radius:10px;display:flex;align-items:center;justify-content:center;">🐛</div>
          <div>
            <div class="bs-logo-name">QA Bug Tracker</div>
            <div class="bs-logo-sub">Shared Projects</div>
          </div>
        </div>

        <div class="bs-header-right">
          <template v-if="currentUser">
            <div class="bs-user" ref="userMenuRef" @click.stop="userMenuOpen = !userMenuOpen">
              <img v-if="currentUser.avatar" :src="currentUser.avatar" class="bs-avatar" :alt="currentUser.name" />
              <span v-else class="bs-avatar bs-avatar-initials">{{ currentUser.name?.[0]?.toUpperCase() }}</span>
              <span class="bs-user-name">{{ currentUser.name }}</span>
              <svg width="11" height="11" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24" style="opacity:.5"><polyline points="6 9 12 15 18 9"/></svg>
              <div v-if="userMenuOpen" class="bs-user-dropdown">
                <div class="bs-user-dropdown-info">
                  <img v-if="currentUser.avatar" :src="currentUser.avatar" class="bs-dropdown-avatar" :alt="currentUser.name" />
                  <span v-else class="bs-dropdown-avatar bs-avatar-initials" style="font-size:16px;">{{ currentUser.name?.[0]?.toUpperCase() }}</span>
                  <div>
                    <div style="font-weight:600;color:#1e293b;font-size:14px;">{{ currentUser.name }}</div>
                    <div style="font-size:12px;color:#64748b;">{{ currentUser.email }}</div>
                  </div>
                </div>
                <div class="bs-user-dropdown-divider"></div>
                <button class="bs-user-dropdown-item bs-signout" @click.stop="logout">
                  <svg width="13" height="13" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>
                  Sign out
                </button>
              </div>
            </div>
          </template>
          <button v-else class="btn-google-signin" @click="login">
            <svg width="16" height="16" viewBox="0 0 48 48"><path fill="#EA4335" d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"/><path fill="#4285F4" d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.96-2.26 5.48-4.78 7.18l7.73 6c4.51-4.18 7.09-10.36 7.09-17.65z"/><path fill="#FBBC05" d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"/><path fill="#34A853" d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.73-6c-2.18 1.48-4.97 2.31-8.16 2.31-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"/></svg>
            Sign in with Google
          </button>
        </div>
      </div>
    </header>

    <!-- ── Sign-in gate ── -->
    <div v-if="!authToken && !loading" class="bs-gate">
      <div class="bs-gate-card">
        <div class="bs-gate-icon">🐛</div>
        <h2 class="bs-gate-title">Access your projects</h2>
        <p class="bs-gate-sub">Sign in with the Google account that was invited to view this project.</p>
        <button class="btn-google-signin bs-gate-btn" @click="login">
          <svg width="18" height="18" viewBox="0 0 48 48"><path fill="#EA4335" d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"/><path fill="#4285F4" d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.96-2.26 5.48-4.78 7.18l7.73 6c4.51-4.18 7.09-10.36 7.09-17.65z"/><path fill="#FBBC05" d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"/><path fill="#34A853" d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.73-6c-2.18 1.48-4.97 2.31-8.16 2.31-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"/></svg>
          Sign in with Google
        </button>
      </div>
    </div>

    <!-- ── Loading ── -->
    <div v-else-if="loading" class="bs-loading">
      <div class="bs-spinner"></div>
      <p>Loading projects…</p>
    </div>

    <!-- ── Projects list ── -->
    <div v-else class="bs-content">

      <div class="bs-page-title-row">
        <div>
          <h1 class="bs-page-title">Bug Tracker Projects</h1>
          <p class="bs-page-sub">{{ `${sharedProjects.length} project${sharedProjects.length !== 1 ? 's' : ''} shared with you` }}</p>
        </div>
        <div class="bs-search-wrap">
          <svg width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg>
          <input v-model="search" class="bs-search-input" placeholder="Search projects…" />
        </div>
      </div>

      <div v-if="sharedProjects.length === 0 && !search" class="bs-empty">
        <div class="bs-empty-icon">🐛</div>
        <div class="bs-empty-title">No shared projects yet</div>
        <div class="bs-empty-sub">Projects shared with you will appear here.</div>
      </div>

      <div v-else-if="filteredProjects.length === 0" class="bs-empty">
        <div class="bs-empty-icon">🔍</div>
        <div class="bs-empty-title">No projects match "{{ search }}"</div>
        <button class="btn btn-ghost btn-sm" style="margin-top:12px;" @click="search = ''">Clear search</button>
      </div>

      <div v-else class="bs-projects-grid">
        <div
          v-for="p in filteredProjects"
          :key="p.id"
          class="bs-project-card"
          :class="{ 'bs-card--highlighted': highlightId === p.id }"
          :style="{ '--pc-color': p.color || '#4f46e5' }"
          :ref="el => { if (highlightId === p.id && el) highlightEl = el }"
          @click="openProject(p)"
        >
          <div class="bs-card-accent"></div>
          <div class="bs-card-body">
            <div class="bs-card-top">
              <div class="bs-card-icon" :style="{ background: (p.color||'#4f46e5')+'18', color: p.color||'#4f46e5' }">
                📁
              </div>
              <div class="bs-card-name-block">
                <h3 class="bs-card-name">{{ p.name }}</h3>
                <p v-if="p.description" class="bs-card-desc">{{ p.description }}</p>
              </div>
              <div class="bs-card-badges">
                <span v-if="p.is_active !== false" class="bs-badge bs-badge-active">ACTIVE</span>
                <span v-else class="bs-badge bs-badge-inactive">INACTIVE</span>
                <span class="bs-badge bs-badge-perm">{{ p.my_permission === 'edit' ? 'Editor' : 'Viewer' }}</span>
              </div>
            </div>

            <div class="bs-card-stats">
              <div class="bs-stat">
                <div class="bs-stat-val">{{ p.bugs_count ?? 0 }}</div>
                <div class="bs-stat-label">Total</div>
              </div>
              <div class="bs-stat">
                <div class="bs-stat-val" style="color:#f59e0b;">{{ p.pending_count ?? 0 }}</div>
                <div class="bs-stat-label">Pending</div>
              </div>
              <div class="bs-stat">
                <div class="bs-stat-val" style="color:#3b82f6;">{{ p.ongoing_count ?? 0 }}</div>
                <div class="bs-stat-label">Ongoing</div>
              </div>
              <div class="bs-stat">
                <div class="bs-stat-val" style="color:#22c55e;">{{ p.completed_count ?? 0 }}</div>
                <div class="bs-stat-label">Done</div>
              </div>
              <div v-if="p.critical_count" class="bs-stat">
                <div class="bs-stat-val" style="color:#ef4444;">{{ p.critical_count }}</div>
                <div class="bs-stat-label">Critical</div>
              </div>
            </div>

            <div class="bs-card-footer">
              <button class="bs-open-btn" @click.stop="openProject(p)">
                Open project
                <svg width="13" height="13" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><polyline points="9 18 15 12 9 6"/></svg>
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'

const config  = useRuntimeConfig()
const apiBase = config.public.apiBase.replace('/api', '')
const route   = useRoute()
const router  = useRouter()

// ── Auth ─────────────────────────────────────────────────────────────────────
const authToken    = ref(null)
const currentUser  = ref(null)
const userMenuOpen = ref(false)
const userMenuRef  = ref(null)

const apiFetch = (url, options = {}) => {
  if (authToken.value) {
    options.headers = { Authorization: `Bearer ${authToken.value}`, ...(options.headers || {}) }
  }
  return $fetch(url, options)
}

const login = () => {
  localStorage.setItem('auth_return', window.location.pathname + window.location.search)
  window.location.href = `${apiBase}/api/auth/google`
}

const logout = async () => {
  try { await apiFetch(`${config.public.apiBase}/auth/logout`, { method: 'POST' }) } catch {}
  authToken.value = null
  localStorage.removeItem('auth_token')
  currentUser.value = null
  userMenuOpen.value = false
}

const fetchCurrentUser = async () => {
  try {
    const user = await apiFetch(`${config.public.apiBase}/auth/me`)
    currentUser.value = (user && user.id) ? user : null
    if (!currentUser.value) { authToken.value = null; localStorage.removeItem('auth_token') }
  } catch {
    authToken.value = null
    localStorage.removeItem('auth_token')
    currentUser.value = null
  }
}

// ── Projects ─────────────────────────────────────────────────────────────────
const loading  = ref(false)
const projects = ref([])
const search   = ref('')

const sharedProjects = computed(() => projects.value.filter(p => p.my_permission !== 'owner'))

const filteredProjects = computed(() => {
  const q = search.value.trim().toLowerCase()
  if (!q) return sharedProjects.value
  return sharedProjects.value.filter(p =>
    p.name.toLowerCase().includes(q) || (p.description || '').toLowerCase().includes(q)
  )
})

const fetchProjects = async () => {
  loading.value = true
  try {
    projects.value = await apiFetch(`${config.public.apiBase}/projects`)
  } catch {
    projects.value = []
  } finally {
    loading.value = false
  }
}

const openProject = (p) => {
  router.push(`/?project=${p.id}&from=shared`)
}

// ── Highlight specific project from ?project= query param ────────────────────
const highlightId = computed(() => {
  const id = route.query.project
  return id ? Number(id) : null
})
const highlightEl = ref(null)

// ── Click outside to close user menu ─────────────────────────────────────────
const clickOutside = (e) => {
  if (userMenuRef.value && !userMenuRef.value.contains(e.target)) {
    userMenuOpen.value = false
  }
}

// ── Lifecycle ─────────────────────────────────────────────────────────────────
onMounted(async () => {
  const stored = localStorage.getItem('auth_token')
  if (stored) authToken.value = stored

  if (authToken.value) {
    await fetchCurrentUser()
    await fetchProjects()

    if (highlightId.value) {
      await nextTick()
      if (highlightEl.value) {
        highlightEl.value.scrollIntoView({ behavior: 'smooth', block: 'center' })
      }
    }
  }

  document.addEventListener('click', clickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', clickOutside)
})
</script>

<style scoped>
.bs-page {
  min-height: 100vh;
  background: #f8fafc;
  font-family: Inter, system-ui, sans-serif;
}

/* ── Header ── */
.bs-header {
  background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%);
  border-bottom: 1px solid rgba(255,255,255,.06);
  padding: 0 24px;
  height: 56px;
  display: flex;
  align-items: center;
  position: sticky;
  top: 0;
  z-index: 50;
}
.bs-header-inner {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
}
.bs-logo { display: flex; align-items: center; gap: 10px; }
.bs-logo-name { font-size: 15px; font-weight: 700; color: #fff; line-height: 1.2; }
.bs-logo-sub  { font-size: 11px; color: #c7d2fe; line-height: 1; }
.bs-header-right { display: flex; align-items: center; gap: 12px; }

/* ── User menu ── */
.bs-user {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 8px;
  transition: background .15s;
  position: relative;
}
.bs-user:hover { background: rgba(255,255,255,.08); }
.bs-avatar {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
}
.bs-avatar-initials {
  background: #4f46e5;
  color: #fff;
  font-size: 12px;
  font-weight: 700;
  display: flex;
  align-items: center;
  justify-content: center;
}
.bs-user-name {
  font-size: 13px;
  font-weight: 500;
  color: rgba(255,255,255,.9);
  max-width: 140px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.bs-user-dropdown {
  position: absolute;
  top: calc(100% + 6px);
  right: 0;
  min-width: 210px;
  background: #fff;
  border: 1px solid #e2e8f0;
  border-radius: 10px;
  box-shadow: 0 8px 24px rgba(0,0,0,.12);
  z-index: 100;
  overflow: hidden;
}
.bs-user-dropdown-info {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 14px 14px 10px;
}
.bs-dropdown-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
}
.bs-user-dropdown-divider { height: 1px; background: #f1f5f9; }
.bs-user-dropdown-item {
  display: flex;
  align-items: center;
  gap: 8px;
  width: 100%;
  padding: 10px 14px;
  font-size: 13px;
  color: #374151;
  background: none;
  border: none;
  cursor: pointer;
  text-align: left;
  transition: background .12s;
}
.bs-user-dropdown-item:hover { background: #f8fafc; }
.bs-signout { color: #ef4444; }
.bs-signout:hover { background: #fef2f2; }

/* ── Sign-in gate ── */
.bs-gate {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: calc(100vh - 56px);
  padding: 40px 24px;
}
.bs-gate-card {
  background: #fff;
  border: 1px solid #e2e8f0;
  border-radius: 16px;
  padding: 48px 40px;
  max-width: 400px;
  width: 100%;
  text-align: center;
  box-shadow: 0 4px 20px rgba(0,0,0,.06);
}
.bs-gate-icon  { font-size: 40px; margin-bottom: 16px; }
.bs-gate-title { font-size: 22px; font-weight: 700; color: #1e293b; margin: 0 0 10px; }
.bs-gate-sub   { font-size: 14px; color: #64748b; margin: 0 0 28px; line-height: 1.5; }
.bs-gate-btn   { font-size: 14px; padding: 10px 22px; margin: 0 auto; }

/* ── Loading ── */
.bs-loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: calc(100vh - 56px);
  gap: 12px;
  color: #64748b;
  font-size: 14px;
}
.bs-spinner {
  width: 32px;
  height: 32px;
  border: 3px solid #e2e8f0;
  border-top-color: #4f46e5;
  border-radius: 50%;
  animation: bs-spin .7s linear infinite;
}
@keyframes bs-spin { to { transform: rotate(360deg); } }

/* ── Content area ── */
.bs-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px 24px 60px;
}

/* ── Page title row ── */
.bs-page-title-row {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 16px;
  margin-bottom: 28px;
  flex-wrap: wrap;
}
.bs-page-title { font-size: 22px; font-weight: 800; color: #1e293b; margin: 0 0 4px; }
.bs-page-sub   { font-size: 13px; color: #94a3b8; margin: 0; }
.bs-search-wrap {
  display: flex;
  align-items: center;
  gap: 8px;
  background: #fff;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 8px 12px;
  color: #94a3b8;
  min-width: 220px;
}
.bs-search-input {
  border: none;
  outline: none;
  font-size: 13px;
  color: #1e293b;
  background: transparent;
  width: 100%;
}
.bs-search-input::placeholder { color: #94a3b8; }

/* ── Empty states ── */
.bs-empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 80px 24px;
  text-align: center;
}
.bs-empty-icon  { font-size: 40px; margin-bottom: 14px; }
.bs-empty-title { font-size: 16px; font-weight: 600; color: #334155; margin-bottom: 6px; }
.bs-empty-sub   { font-size: 13px; color: #94a3b8; }

/* ── Projects grid ── */
.bs-projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 18px;
}

/* ── Project card ── */
.bs-project-card {
  background: #fff;
  border: 1.5px solid #e2e8f0;
  border-radius: 14px;
  overflow: hidden;
  cursor: pointer;
  transition: box-shadow .18s, border-color .18s, transform .15s;
  display: flex;
  flex-direction: column;
}
.bs-project-card:hover {
  box-shadow: 0 8px 28px rgba(0,0,0,.09);
  border-color: var(--pc-color, #4f46e5);
  transform: translateY(-2px);
}
.bs-card--highlighted {
  border-color: var(--pc-color, #4f46e5);
  box-shadow: 0 0 0 3px color-mix(in srgb, var(--pc-color, #4f46e5) 20%, transparent);
}
.bs-card-accent {
  height: 4px;
  background: var(--pc-color, #4f46e5);
}
.bs-card-body {
  padding: 18px 20px 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  flex: 1;
}
.bs-card-top {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}
.bs-card-icon {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  font-size: 20px;
}
.bs-card-name-block { flex: 1; min-width: 0; }
.bs-card-name {
  font-size: 15px;
  font-weight: 700;
  color: #1e293b;
  margin: 0 0 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.bs-card-desc {
  font-size: 13px;
  color: #64748b;
  margin: 0;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
.bs-card-badges {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 4px;
  flex-shrink: 0;
}
.bs-badge {
  font-size: 10px;
  font-weight: 700;
  padding: 2px 8px;
  border-radius: 99px;
  letter-spacing: .3px;
  white-space: nowrap;
}
.bs-badge-active   { background: #d1fae5; color: #065f46; }
.bs-badge-inactive { background: #fee2e2; color: #b91c1c; }
.bs-badge-perm     { background: #ede9fe; color: #5b21b6; }

/* ── Stats row ── */
.bs-card-stats { display: flex; gap: 16px; }
.bs-stat { text-align: center; }
.bs-stat-val   { font-size: 18px; font-weight: 800; color: #1e293b; line-height: 1.1; }
.bs-stat-label { font-size: 10px; font-weight: 600; color: #94a3b8; text-transform: uppercase; letter-spacing: .4px; margin-top: 2px; }

/* ── Card footer ── */
.bs-card-footer {
  padding-top: 4px;
  border-top: 1px solid #f1f5f9;
  display: flex;
  justify-content: flex-end;
}
.bs-open-btn {
  display: flex;
  align-items: center;
  gap: 5px;
  font-size: 12.5px;
  font-weight: 600;
  color: var(--pc-color, #4f46e5);
  background: none;
  border: none;
  cursor: pointer;
  padding: 4px 0;
  transition: opacity .12s;
}
.bs-open-btn:hover { opacity: .75; }

/* ── Google sign-in button ── */
.btn-google-signin {
  display: flex;
  align-items: center;
  gap: 8px;
  background: #fff;
  color: #374151;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  padding: 8px 16px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition: background .15s, box-shadow .15s;
}
.btn-google-signin:hover { background: #f9fafb; box-shadow: 0 1px 4px rgba(0,0,0,.08); }

/* ── Responsive ── */
@media (max-width: 640px) {
  .bs-content { padding: 20px 16px 40px; }
  .bs-page-title-row { flex-direction: column; }
  .bs-search-wrap { width: 100%; }
  .bs-projects-grid { grid-template-columns: 1fr; }
  .bs-gate-card { padding: 36px 24px; }
}
</style>
