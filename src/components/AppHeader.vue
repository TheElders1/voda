<template>
  <header class="header" :class="{ 'header--scrolled': scrolled }">
    <div class="header__inner">
      <div class="header__brand">
        <svg class="header__logo" viewBox="0 0 32 32" fill="none">
          <circle cx="16" cy="16" r="16" fill="#e60000"/>
          <path d="M16 8C11.58 8 8 11.58 8 16s3.58 8 8 8 8-3.58 8-8-3.58-8-8-8zm0 14c-3.31 0-6-2.69-6-6s2.69-6 6-6 6 2.69 6 6-2.69 6-6 6z" fill="white"/>
        </svg>
        <span class="header__title">E-Mail & Cloud</span>
      </div>
      <nav class="header__nav" v-if="user">
        <button class="header__nav-btn" :class="{ active: true }">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M22 4L12 13 2 4"/></svg>
          <span>E-Mail</span>
        </button>
        <button class="header__nav-btn">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 19a2 2 0 01-2 2H4a2 2 0 01-2-2V5a2 2 0 012-2h5l2 3h9a2 2 0 012 2z"/></svg>
          <span>Cloud</span>
        </button>
      </nav>
      <div class="header__actions">
        <template v-if="user">
          <div class="header__user">
            <div class="header__avatar">{{ initials }}</div>
            <span class="header__username">{{ user.email }}</span>
          </div>
          <button class="header__logout" @click="$emit('logout')">Abmelden</button>
        </template>
        <template v-else>
          <button class="header__login" @click="$emit('login')">Anmelden</button>
        </template>
      </div>
    </div>
  </header>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';

const props = defineProps({ user: Object });
defineEmits(['logout', 'login']);

const scrolled = ref(false);

const initials = computed(() => {
  if (!props.user?.email) return '?';
  return props.user.email.charAt(0).toUpperCase();
});

function onScroll() {
  scrolled.value = window.scrollY > 10;
}

onMounted(() => window.addEventListener('scroll', onScroll));
onUnmounted(() => window.removeEventListener('scroll', onScroll));
</script>

<style scoped>
.header {
  background: var(--vf-white);
  border-bottom: 1px solid var(--vf-gray-200);
  position: sticky;
  top: 0;
  z-index: 100;
  transition: box-shadow var(--vf-transition);
}

.header--scrolled {
  box-shadow: var(--vf-shadow);
}

.header__inner {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 24px;
  height: 64px;
  display: flex;
  align-items: center;
  gap: 24px;
}

.header__brand {
  display: flex;
  align-items: center;
  gap: 10px;
  flex-shrink: 0;
}

.header__logo {
  width: 32px;
  height: 32px;
}

.header__title {
  font-size: 18px;
  font-weight: 600;
  color: var(--vf-gray-900);
  white-space: nowrap;
}

.header__nav {
  display: flex;
  gap: 4px;
  margin-left: 24px;
}

.header__nav-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 14px;
  border: none;
  background: none;
  border-radius: var(--vf-radius);
  color: var(--vf-gray-600);
  font-size: 14px;
  transition: all var(--vf-transition);
}

.header__nav-btn:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-900);
}

.header__nav-btn.active {
  background: var(--vf-gray-100);
  color: var(--vf-red);
}

.header__actions {
  margin-left: auto;
  display: flex;
  align-items: center;
  gap: 16px;
}

.header__user {
  display: flex;
  align-items: center;
  gap: 8px;
}

.header__avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: var(--vf-red);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 600;
}

.header__username {
  font-size: 13px;
  color: var(--vf-gray-600);
  max-width: 180px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.header__logout {
  padding: 6px 16px;
  border: 1px solid var(--vf-gray-300);
  background: var(--vf-white);
  border-radius: 20px;
  font-size: 13px;
  color: var(--vf-gray-700);
  transition: all var(--vf-transition);
}

.header__logout:hover {
  border-color: var(--vf-red);
  color: var(--vf-red);
}

.header__login {
  padding: 8px 20px;
  background: var(--vf-red);
  border: none;
  border-radius: 20px;
  color: white;
  font-size: 14px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.header__login:hover {
  background: var(--vf-red-dark);
}

@media (max-width: 768px) {
  .header__nav { display: none; }
  .header__username { display: none; }
  .header__inner { padding: 0 16px; }
}
</style>
