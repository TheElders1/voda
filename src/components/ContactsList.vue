<template>
  <div class="contacts">
    <div class="contacts__toolbar">
      <h2>Kontakte</h2>
      <div class="contacts__toolbar-right">
        <div class="contacts__search">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
          <input type="text" v-model="search" placeholder="Kontakte suchen..." />
        </div>
        <button class="contacts__add-btn" @click="showAdd = true">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
          Neuer Kontakt
        </button>
      </div>
    </div>

    <div class="contacts__list">
      <div
        v-for="contact in filteredContacts"
        :key="contact.id"
        class="contacts__item"
      >
        <div class="contacts__avatar" :style="{ background: contact.color }">
          {{ contact.name.charAt(0) }}
        </div>
        <div class="contacts__info">
          <span class="contacts__name">{{ contact.name }}</span>
          <span class="contacts__email">{{ contact.email }}</span>
        </div>
        <div class="contacts__actions">
          <button class="contacts__action" title="E-Mail senden">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M22 4L12 13 2 4"/></svg>
          </button>
          <button class="contacts__action" title="Bearbeiten">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 4H4a2 2 0 00-2 2v14a2 2 0 002 2h14a2 2 0 002-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 013 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
          </button>
        </div>
      </div>
    </div>

    <div class="contacts__add-overlay" v-if="showAdd" @click.self="showAdd = false">
      <div class="contacts__add-card">
        <h3>Neuer Kontakt</h3>
        <form @submit.prevent="addContact" class="contacts__add-form">
          <div class="contacts__add-field">
            <label>Name</label>
            <input type="text" v-model="newName" required placeholder="Vollstandiger Name" />
          </div>
          <div class="contacts__add-field">
            <label>E-Mail</label>
            <input type="email" v-model="newEmail" required placeholder="email@vodafone.de" />
          </div>
          <div class="contacts__add-field">
            <label>Telefon</label>
            <input type="tel" v-model="newPhone" placeholder="+49 123 456789" />
          </div>
          <div class="contacts__add-actions">
            <button type="submit" class="contacts__add-submit">Speichern</button>
            <button type="button" class="contacts__add-cancel" @click="showAdd = false">Abbrechen</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const search = ref('');
const showAdd = ref(false);
const newName = ref('');
const newEmail = ref('');
const newPhone = ref('');

const colors = ['#e60000', '#006dfe', '#008744', '#ffa700', '#7c3aed', '#d97706'];

const contacts = ref([
  { id: 1, name: 'Anna Schmidt', email: 'anna.schmidt@vodafone.de', color: '#e60000' },
  { id: 2, name: 'Max Muller', email: 'max.mueller@vodafone.de', color: '#006dfe' },
  { id: 3, name: 'Lisa Weber', email: 'lisa.weber@vodafone.de', color: '#008744' },
  { id: 4, name: 'Thomas Braun', email: 'thomas.braun@vodafone.de', color: '#ffa700' },
  { id: 5, name: 'Sarah Fischer', email: 'sarah.fischer@vodafone.de', color: '#7c3aed' },
  { id: 6, name: 'Michael Wagner', email: 'michael.wagner@vodafone.de', color: '#d97706' }
]);

const filteredContacts = computed(() => {
  if (!search.value) return contacts.value;
  const s = search.value.toLowerCase();
  return contacts.value.filter(c => c.name.toLowerCase().includes(s) || c.email.toLowerCase().includes(s));
});

function addContact() {
  contacts.value.push({
    id: Date.now(),
    name: newName.value,
    email: newEmail.value,
    color: colors[Math.floor(Math.random() * colors.length)]
  });
  newName.value = '';
  newEmail.value = '';
  newPhone.value = '';
  showAdd.value = false;
}
</script>

<style scoped>
.contacts {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.contacts__toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

.contacts__toolbar h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--vf-gray-900);
}

.contacts__toolbar-right {
  display: flex;
  gap: 12px;
  align-items: center;
}

.contacts__search {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: white;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
}

.contacts__search:focus-within {
  border-color: var(--vf-red);
}

.contacts__search input {
  border: none;
  outline: none;
  font-size: 14px;
  width: 180px;
  background: none;
}

.contacts__add-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.contacts__add-btn:hover {
  background: var(--vf-red-dark);
}

.contacts__list {
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
}

.contacts__item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px 16px;
  border-bottom: 1px solid var(--vf-gray-100);
  transition: background var(--vf-transition);
}

.contacts__item:last-child {
  border-bottom: none;
}

.contacts__item:hover {
  background: var(--vf-gray-50);
}

.contacts__avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 16px;
  font-weight: 600;
  flex-shrink: 0;
}

.contacts__info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.contacts__name {
  font-size: 14px;
  font-weight: 500;
  color: var(--vf-gray-900);
}

.contacts__email {
  font-size: 12px;
  color: var(--vf-gray-400);
}

.contacts__actions {
  display: flex;
  gap: 4px;
  opacity: 0;
  transition: opacity var(--vf-transition);
}

.contacts__item:hover .contacts__actions {
  opacity: 1;
}

.contacts__action {
  padding: 6px;
  border: none;
  background: none;
  color: var(--vf-gray-400);
  border-radius: 50%;
  transition: all var(--vf-transition);
}

.contacts__action:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
}

.contacts__add-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
}

.contacts__add-card {
  background: white;
  border-radius: var(--vf-radius-lg);
  padding: 24px;
  width: 100%;
  max-width: 420px;
  box-shadow: var(--vf-shadow-lg);
}

.contacts__add-card h3 {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
}

.contacts__add-form {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.contacts__add-field label {
  display: block;
  font-size: 12px;
  font-weight: 500;
  color: var(--vf-gray-500);
  margin-bottom: 4px;
}

.contacts__add-field input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
  font-size: 14px;
  outline: none;
  transition: border-color var(--vf-transition);
}

.contacts__add-field input:focus {
  border-color: var(--vf-red);
}

.contacts__add-actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
  margin-top: 4px;
}

.contacts__add-submit {
  padding: 8px 20px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
}

.contacts__add-submit:hover {
  background: var(--vf-red-dark);
}

.contacts__add-cancel {
  padding: 8px 16px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 14px;
  color: var(--vf-gray-600);
}

@media (max-width: 768px) {
  .contacts__search input { width: 120px; }
  .contacts__actions { opacity: 1; }
}
</style>
