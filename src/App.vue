<template>
  <div class="app-layout">
    <AppHeader :user="user" @logout="handleLogout" @login="showAuth = true" />
    <div class="app-body">
      <AppSidebar :active="activeSection" @navigate="setSection" v-if="user" />
      <main class="app-main">
        <AuthForm v-if="!user && showAuth" @close="showAuth = false" @auth="handleAuth" />
        <WelcomeScreen v-else-if="!user" @login="showAuth = true" />
        <template v-else>
          <EmailInbox v-if="activeSection === 'email'" :user="user" />
          <CloudDrive v-else-if="activeSection === 'cloud'" :user="user" />
          <ContactsList v-else-if="activeSection === 'contacts'" :user="user" />
          <CalendarView v-else-if="activeSection === 'calendar'" :user="user" />
          <SettingsPanel v-else-if="activeSection === 'settings'" :user="user" />
        </template>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { supabase } from './supabase.js';
import AppHeader from './components/AppHeader.vue';
import AppSidebar from './components/AppSidebar.vue';
import AuthForm from './components/AuthForm.vue';
import WelcomeScreen from './components/WelcomeScreen.vue';
import EmailInbox from './components/EmailInbox.vue';
import CloudDrive from './components/CloudDrive.vue';
import ContactsList from './components/ContactsList.vue';
import CalendarView from './components/CalendarView.vue';
import SettingsPanel from './components/SettingsPanel.vue';

const user = ref(null);
const showAuth = ref(false);
const activeSection = ref('email');

onMounted(() => {
  supabase.auth.getSession().then(({ data: { session } }) => {
    user.value = session?.user ?? null;
  });
  supabase.auth.onAuthStateChange((_event, session) => {
    user.value = session?.user ?? null;
  });
});

function handleAuth(session) {
  user.value = session.user;
  showAuth.value = false;
}

function handleLogout() {
  supabase.auth.signOut();
  user.value = null;
  activeSection.value = 'email';
}

function setSection(section) {
  activeSection.value = section;
}
</script>

<style scoped>
.app-layout {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.app-body {
  flex: 1;
  display: flex;
  overflow: hidden;
}

.app-main {
  flex: 1;
  overflow-y: auto;
  padding: 24px;
  background: var(--vf-gray-50);
}

@media (max-width: 768px) {
  .app-main {
    padding: 16px;
  }
}
</style>
