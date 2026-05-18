<template>
  <div class="email">
    <div class="email__toolbar">
      <div class="email__toolbar-left">
        <h2>Posteingang</h2>
        <span class="email__count">{{ filteredEmails.length }} Nachrichten</span>
      </div>
      <div class="email__toolbar-right">
        <div class="email__search">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
          <input type="text" v-model="search" placeholder="E-Mails durchsuchen..." />
        </div>
        <button class="email__compose-btn" @click="showCompose = true">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
          Neue E-Mail
        </button>
      </div>
    </div>

    <div class="email__filters">
      <button
        v-for="f in filters"
        :key="f.id"
        class="email__filter"
        :class="{ 'email__filter--active': activeFilter === f.id }"
        @click="activeFilter = f.id"
      >{{ f.label }}</button>
    </div>

    <div class="email__list" v-if="filteredEmails.length">
      <div
        v-for="email in filteredEmails"
        :key="email.id"
        class="email__item"
        :class="{ 'email__item--unread': !email.read, 'email__item--selected': selectedId === email.id }"
        @click="selectEmail(email)"
      >
        <div class="email__item-avatar">{{ email.from.charAt(0) }}</div>
        <div class="email__item-content">
          <div class="email__item-top">
            <span class="email__item-from">{{ email.from }}</span>
            <span class="email__item-date">{{ email.date }}</span>
          </div>
          <div class="email__item-subject">{{ email.subject }}</div>
          <div class="email__item-preview">{{ email.preview }}</div>
        </div>
        <div class="email__item-star" @click.stop="email.starred = !email.starred">
          <svg :width="16" :height="16" viewBox="0 0 24 24" :fill="email.starred ? '#ffa700' : 'none'" :stroke="email.starred ? '#ffa700' : 'currentColor'" stroke-width="2"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>
        </div>
      </div>
    </div>
    <div class="email__empty" v-else>
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="var(--vf-gray-300)" stroke-width="1.5"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M22 4L12 13 2 4"/></svg>
      <p>Keine E-Mails gefunden</p>
    </div>

    <div class="email__detail" v-if="selectedEmail">
      <div class="email__detail-header">
        <button class="email__detail-back" @click="selectedId = null">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="15 18 9 12 15 6"/></svg>
          Zuruck
        </button>
        <div class="email__detail-actions">
          <button class="email__detail-action" title="Antworten">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="9 17 4 12 9 7"/><path d="M20 18v-2a4 4 0 00-4-4H4"/></svg>
          </button>
          <button class="email__detail-action" title="Weiterleiten">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="15 7 20 12 15 17"/><path d="M4 6v2a4 4 0 004 4h12"/></svg>
          </button>
          <button class="email__detail-action" title="Loschen">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 01-2 2H7a2 2 0 01-2-2V6m3 0V4a2 2 0 012-2h4a2 2 0 012 2v2"/></svg>
          </button>
        </div>
      </div>
      <h3 class="email__detail-subject">{{ selectedEmail.subject }}</h3>
      <div class="email__detail-meta">
        <div class="email__detail-avatar">{{ selectedEmail.from.charAt(0) }}</div>
        <div>
          <div class="email__detail-from">{{ selectedEmail.from }}</div>
          <div class="email__detail-to">an mich</div>
        </div>
        <div class="email__detail-date">{{ selectedEmail.date }}</div>
      </div>
      <div class="email__detail-body">{{ selectedEmail.body }}</div>
    </div>

    <ComposeEmail v-if="showCompose" @close="showCompose = false" />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import ComposeEmail from './ComposeEmail.vue';

const props = defineProps({ user: Object });

const search = ref('');
const activeFilter = ref('all');
const selectedId = ref(null);
const showCompose = ref(false);

const filters = [
  { id: 'all', label: 'Alle' },
  { id: 'unread', label: 'Ungelesen' },
  { id: 'starred', label: 'Markiert' }
];

const emails = ref([
  { id: 1, from: 'Vodafone Kundenservice', subject: 'Willkommen bei Vodafone E-Mail & Cloud', preview: 'Vielen Dank, dass Sie sich fur Vodafone E-Mail & Cloud entschieden haben...', body: 'Vielen Dank, dass Sie sich fur Vodafone E-Mail & Cloud entschieden haben. Ihr Konto ist jetzt aktiv und bereit zur Nutzung. Mit unserem Service erhalten Sie 10 GB kostenlosen Cloud-Speicher, eine professionelle E-Mail-Adresse und vieles mehr.', date: 'Heute', read: false, starred: true },
  { id: 2, from: 'Rechnungsservice', subject: 'Ihre Rechnung vom Mai 2026', preview: 'Sehr geehrter Kunde, Ihre aktuelle Rechnung steht zum Download bereit...', body: 'Sehr geehrter Kunde, Ihre aktuelle Rechnung fur den Monat Mai 2026 steht zum Download bereit. Der Gesamtbetrag betragt 39,99 EUR. Bitte uberprufen Sie die Details in Ihrem Kundenportal.', date: 'Gestern', read: false, starred: false },
  { id: 3, from: 'Sicherheitshinweis', subject: 'Wichtige Sicherheitsinformation', preview: 'Wir empfehlen Ihnen, Ihre Sicherheitseinstellungen zu uberprufen...', body: 'Wir empfehlen Ihnen, Ihre Sicherheitseinstellungen zu uberprufen. Aktivieren Sie die Zwei-Faktor-Authentifizierung fur zusatzlichen Schutz Ihres Kontos.', date: '15.05.2026', read: true, starred: false },
  { id: 4, from: 'Cloud-Team', subject: 'Neue Cloud-Funktionen verfugbar', preview: 'Entdecken Sie die neuesten Funktionen in Ihrem Cloud-Speicher...', body: 'Entdecken Sie die neuesten Funktionen in Ihrem Cloud-Speicher. Wir haben die Freigabe-Funktion verbessert und neue Organisationsmoglichkeiten hinzugefugt.', date: '12.05.2026', read: true, starred: true },
  { id: 5, from: 'Newsletter', subject: 'Vodafone News - Mai 2026', preview: 'Die neuesten Nachrichten und Angebote von Vodafone...', body: 'Die neuesten Nachrichten und Angebote von Vodafone. Erfahren Sie mehr uber unsere aktuellen Tarife und Aktionen.', date: '10.05.2026', read: true, starred: false }
]);

const filteredEmails = computed(() => {
  let result = emails.value;
  if (search.value) {
    const s = search.value.toLowerCase();
    result = result.filter(e => e.subject.toLowerCase().includes(s) || e.from.toLowerCase().includes(s));
  }
  if (activeFilter.value === 'unread') result = result.filter(e => !e.read);
  if (activeFilter.value === 'starred') result = result.filter(e => e.starred);
  return result;
});

const selectedEmail = computed(() => emails.value.find(e => e.id === selectedId.value));

function selectEmail(email) {
  email.read = true;
  selectedId.value = email.id;
}
</script>

<style scoped>
.email {
  display: flex;
  flex-direction: column;
  gap: 16px;
  height: 100%;
}

.email__toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

.email__toolbar h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--vf-gray-900);
}

.email__count {
  font-size: 13px;
  color: var(--vf-gray-500);
  margin-left: 8px;
}

.email__toolbar-right {
  display: flex;
  gap: 12px;
  align-items: center;
}

.email__search {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: white;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
  transition: border-color var(--vf-transition);
}

.email__search:focus-within {
  border-color: var(--vf-red);
}

.email__search input {
  border: none;
  outline: none;
  font-size: 14px;
  width: 200px;
  background: none;
}

.email__compose-btn {
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

.email__compose-btn:hover {
  background: var(--vf-red-dark);
}

.email__filters {
  display: flex;
  gap: 4px;
}

.email__filter {
  padding: 6px 14px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: 20px;
  font-size: 13px;
  color: var(--vf-gray-600);
  transition: all var(--vf-transition);
}

.email__filter:hover {
  border-color: var(--vf-gray-400);
}

.email__filter--active {
  background: var(--vf-red);
  border-color: var(--vf-red);
  color: white;
}

.email__list {
  display: flex;
  flex-direction: column;
  gap: 2px;
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
}

.email__item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px 16px;
  cursor: pointer;
  transition: background var(--vf-transition);
  border-bottom: 1px solid var(--vf-gray-100);
}

.email__item:last-child {
  border-bottom: none;
}

.email__item:hover {
  background: var(--vf-gray-50);
}

.email__item--selected {
  background: #fef2f2;
}

.email__item--unread .email__item-from,
.email__item--unread .email__item-subject {
  font-weight: 600;
}

.email__item-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: var(--vf-gray-200);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: 600;
  color: var(--vf-gray-600);
  flex-shrink: 0;
}

.email__item-content {
  flex: 1;
  min-width: 0;
}

.email__item-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2px;
}

.email__item-from {
  font-size: 14px;
  color: var(--vf-gray-900);
}

.email__item-date {
  font-size: 12px;
  color: var(--vf-gray-400);
  flex-shrink: 0;
}

.email__item-subject {
  font-size: 13px;
  color: var(--vf-gray-700);
  margin-bottom: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.email__item-preview {
  font-size: 12px;
  color: var(--vf-gray-400);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.email__item-star {
  flex-shrink: 0;
  padding: 4px;
  color: var(--vf-gray-300);
  cursor: pointer;
}

.email__item-star:hover {
  color: var(--vf-orange);
}

.email__empty {
  text-align: center;
  padding: 48px;
  color: var(--vf-gray-400);
}

.email__empty p {
  margin-top: 12px;
  font-size: 14px;
}

.email__detail {
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  padding: 24px;
}

.email__detail-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.email__detail-back {
  display: flex;
  align-items: center;
  gap: 4px;
  border: none;
  background: none;
  color: var(--vf-gray-600);
  font-size: 14px;
  transition: color var(--vf-transition);
}

.email__detail-back:hover {
  color: var(--vf-red);
}

.email__detail-actions {
  display: flex;
  gap: 4px;
}

.email__detail-action {
  padding: 8px;
  border: none;
  background: none;
  color: var(--vf-gray-500);
  border-radius: var(--vf-radius);
  transition: all var(--vf-transition);
}

.email__detail-action:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
}

.email__detail-subject {
  font-size: 18px;
  font-weight: 600;
  color: var(--vf-gray-900);
  margin-bottom: 16px;
}

.email__detail-meta {
  display: flex;
  align-items: center;
  gap: 12px;
  padding-bottom: 16px;
  border-bottom: 1px solid var(--vf-gray-200);
  margin-bottom: 16px;
}

.email__detail-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--vf-gray-200);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  color: var(--vf-gray-600);
}

.email__detail-from {
  font-size: 14px;
  font-weight: 500;
  color: var(--vf-gray-900);
}

.email__detail-to {
  font-size: 12px;
  color: var(--vf-gray-400);
}

.email__detail-date {
  margin-left: auto;
  font-size: 12px;
  color: var(--vf-gray-400);
}

.email__detail-body {
  font-size: 14px;
  line-height: 1.7;
  color: var(--vf-gray-700);
}

@media (max-width: 768px) {
  .email__search input { width: 120px; }
  .email__toolbar { flex-direction: column; align-items: flex-start; }
}
</style>
