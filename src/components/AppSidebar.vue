<template>
  <aside class="sidebar">
    <nav class="sidebar__nav">
      <button
        v-for="item in items"
        :key="item.id"
        class="sidebar__item"
        :class="{ 'sidebar__item--active': active === item.id }"
        @click="$emit('navigate', item.id)"
      >
        <span class="sidebar__icon" v-html="item.icon"></span>
        <span class="sidebar__label">{{ item.label }}</span>
        <span class="sidebar__badge" v-if="item.badge">{{ item.badge }}</span>
      </button>
    </nav>
    <div class="sidebar__footer">
      <div class="sidebar__storage">
        <div class="sidebar__storage-label">Speicher</div>
        <div class="sidebar__storage-bar">
          <div class="sidebar__storage-fill" style="width: 35%"></div>
        </div>
        <div class="sidebar__storage-text">3,5 GB von 10 GB</div>
      </div>
    </div>
  </aside>
</template>

<script setup>
defineProps({ active: String });
defineEmits(['navigate']);

const items = [
  {
    id: 'email',
    label: 'E-Mail',
    badge: '3',
    icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M22 4L12 13 2 4"/></svg>'
  },
  {
    id: 'cloud',
    label: 'Cloud',
    icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 19a2 2 0 01-2 2H4a2 2 0 01-2-2V5a2 2 0 012-2h5l2 3h9a2 2 0 012 2z"/></svg>'
  },
  {
    id: 'contacts',
    label: 'Kontakte',
    icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17 21v-2a4 4 0 00-4-4H5a4 4 0 00-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 00-3-3.87"/><path d="M16 3.13a4 4 0 010 7.75"/></svg>'
  },
  {
    id: 'calendar',
    label: 'Kalender',
    icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>'
  },
  {
    id: 'settings',
    label: 'Einstellungen',
    icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 010 2.83 2 2 0 01-2.83 0l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 01-4 0v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 01-2.83 0 2 2 0 010-2.83l.06-.06A1.65 1.65 0 004.68 15a1.65 1.65 0 00-1.51-1H3a2 2 0 010-4h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 012.83-2.83l.06.06A1.65 1.65 0 009 4.68a1.65 1.65 0 001-1.51V3a2 2 0 014 0v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 012.83 2.83l-.06.06A1.65 1.65 0 0019.4 9a1.65 1.65 0 001.51 1H21a2 2 0 010 4h-.09a1.65 1.65 0 00-1.51 1z"/></svg>'
  }
];
</script>

<style scoped>
.sidebar {
  width: 240px;
  background: var(--vf-white);
  border-right: 1px solid var(--vf-gray-200);
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  padding: 16px 12px;
}

.sidebar__nav {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.sidebar__item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 12px;
  border: none;
  background: none;
  border-radius: var(--vf-radius);
  color: var(--vf-gray-600);
  font-size: 14px;
  transition: all var(--vf-transition);
  position: relative;
}

.sidebar__item:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-900);
}

.sidebar__item--active {
  background: #fef2f2;
  color: var(--vf-red);
  font-weight: 500;
}

.sidebar__icon {
  display: flex;
  align-items: center;
  flex-shrink: 0;
}

.sidebar__label {
  flex: 1;
  text-align: left;
}

.sidebar__badge {
  background: var(--vf-red);
  color: white;
  font-size: 11px;
  font-weight: 600;
  padding: 1px 7px;
  border-radius: 10px;
  min-width: 20px;
  text-align: center;
}

.sidebar__footer {
  margin-top: auto;
  padding-top: 16px;
  border-top: 1px solid var(--vf-gray-200);
}

.sidebar__storage-label {
  font-size: 12px;
  color: var(--vf-gray-500);
  margin-bottom: 6px;
}

.sidebar__storage-bar {
  height: 4px;
  background: var(--vf-gray-200);
  border-radius: 2px;
  overflow: hidden;
}

.sidebar__storage-fill {
  height: 100%;
  background: var(--vf-red);
  border-radius: 2px;
  transition: width 0.5s ease;
}

.sidebar__storage-text {
  font-size: 11px;
  color: var(--vf-gray-400);
  margin-top: 4px;
}

@media (max-width: 768px) {
  .sidebar { display: none; }
}
</style>
