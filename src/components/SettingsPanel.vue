<template>
  <div class="settings">
    <h2>Einstellungen</h2>

    <div class="settings__section">
      <h3>Profil</h3>
      <div class="settings__card">
        <div class="settings__row">
          <div class="settings__label">E-Mail-Adresse</div>
          <div class="settings__value">{{ user?.email }}</div>
        </div>
        <div class="settings__row">
          <div class="settings__label">Konto-ID</div>
          <div class="settings__value">{{ user?.id?.substring(0, 8) }}...</div>
        </div>
        <div class="settings__row">
          <div class="settings__label">Erstellt am</div>
          <div class="settings__value">{{ formatDate(user?.created_at) }}</div>
        </div>
      </div>
    </div>

    <div class="settings__section">
      <h3>Sicherheit</h3>
      <div class="settings__card">
        <div class="settings__row settings__row--action">
          <div>
            <div class="settings__label">Passwort andern</div>
            <div class="settings__desc">Aktualisieren Sie Ihr Passwort regelmaig</div>
          </div>
          <button class="settings__btn" @click="showPassword = true">Andern</button>
        </div>
        <div class="settings__row settings__row--action">
          <div>
            <div class="settings__label">Zwei-Faktor-Authentifizierung</div>
            <div class="settings__desc">Zusatzlicher Schutz fur Ihr Konto</div>
          </div>
          <span class="settings__badge settings__badge--inactive">Deaktiviert</span>
        </div>
      </div>
    </div>

    <div class="settings__section">
      <h3>E-Mail-Einstellungen</h3>
      <div class="settings__card">
        <div class="settings__row settings__row--toggle">
          <div>
            <div class="settings__label">E-Mail-Benachrichtigungen</div>
            <div class="settings__desc">Erhalten Sie Benachrichtigungen uber neue E-Mails</div>
          </div>
          <label class="settings__toggle">
            <input type="checkbox" v-model="notifications" />
            <span class="settings__toggle-slider"></span>
          </label>
        </div>
        <div class="settings__row settings__row--toggle">
          <div>
            <div class="settings__label">Automatische Weiterleitung</div>
            <div class="settings__desc">E-Mails an eine andere Adresse weiterleiten</div>
          </div>
          <label class="settings__toggle">
            <input type="checkbox" v-model="forwarding" />
            <span class="settings__toggle-slider"></span>
          </label>
        </div>
        <div class="settings__row settings__row--toggle">
          <div>
            <div class="settings__label">Spam-Filter</div>
            <div class="settings__desc">Unerwunschte E-Mails automatisch filtern</div>
          </div>
          <label class="settings__toggle">
            <input type="checkbox" v-model="spamFilter" />
            <span class="settings__toggle-slider"></span>
          </label>
        </div>
      </div>
    </div>

    <div class="settings__section">
      <h3>Speicher</h3>
      <div class="settings__card">
        <div class="settings__storage">
          <div class="settings__storage-bar">
            <div class="settings__storage-fill" style="width: 35%"></div>
          </div>
          <div class="settings__storage-text">3,5 GB von 10 GB verwendet</div>
        </div>
      </div>
    </div>

    <div class="settings__password-overlay" v-if="showPassword" @click.self="showPassword = false">
      <div class="settings__password-card">
        <h3>Passwort andern</h3>
        <form @submit.prevent="changePassword" class="settings__password-form">
          <div class="settings__password-field">
            <label>Aktuelles Passwort</label>
            <input type="password" v-model="currentPassword" required />
          </div>
          <div class="settings__password-field">
            <label>Neues Passwort</label>
            <input type="password" v-model="newPassword" required minlength="6" />
          </div>
          <div class="settings__password-field">
            <label>Passwort bestatigen</label>
            <input type="password" v-model="confirmPassword" required />
          </div>
          <div class="settings__password-error" v-if="passwordError">{{ passwordError }}</div>
          <div class="settings__password-success" v-if="passwordSuccess">{{ passwordSuccess }}</div>
          <div class="settings__password-actions">
            <button type="submit" class="settings__password-submit" :disabled="passwordLoading">
              {{ passwordLoading ? 'Wird geandert...' : 'Passwort andern' }}
            </button>
            <button type="button" class="settings__password-cancel" @click="showPassword = false">Abbrechen</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { supabase } from '../supabase.js';

defineProps({ user: Object });

const notifications = ref(true);
const forwarding = ref(false);
const spamFilter = ref(true);
const showPassword = ref(false);
const currentPassword = ref('');
const newPassword = ref('');
const confirmPassword = ref('');
const passwordError = ref('');
const passwordSuccess = ref('');
const passwordLoading = ref(false);

function formatDate(dateStr) {
  if (!dateStr) return '-';
  return new Date(dateStr).toLocaleDateString('de-DE');
}

async function changePassword() {
  passwordError.value = '';
  passwordSuccess.value = '';

  if (newPassword.value !== confirmPassword.value) {
    passwordError.value = 'Passworter stimmen nicht uberein';
    return;
  }

  passwordLoading.value = true;
  const { error } = await supabase.auth.updateUser({ password: newPassword.value });
  passwordLoading.value = false;

  if (error) {
    passwordError.value = error.message;
  } else {
    passwordSuccess.value = 'Passwort erfolgreich geandert';
    currentPassword.value = '';
    newPassword.value = '';
    confirmPassword.value = '';
    setTimeout(() => { showPassword.value = false; passwordSuccess.value = ''; }, 2000);
  }
}
</script>

<style scoped>
.settings {
  max-width: 700px;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.settings h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--vf-gray-900);
}

.settings__section h3 {
  font-size: 15px;
  font-weight: 600;
  color: var(--vf-gray-700);
  margin-bottom: 10px;
}

.settings__card {
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
}

.settings__row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 16px;
  border-bottom: 1px solid var(--vf-gray-100);
}

.settings__row:last-child {
  border-bottom: none;
}

.settings__label {
  font-size: 14px;
  font-weight: 500;
  color: var(--vf-gray-900);
}

.settings__desc {
  font-size: 12px;
  color: var(--vf-gray-400);
  margin-top: 2px;
}

.settings__value {
  font-size: 14px;
  color: var(--vf-gray-500);
}

.settings__btn {
  padding: 6px 14px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 13px;
  color: var(--vf-gray-700);
  transition: all var(--vf-transition);
}

.settings__btn:hover {
  border-color: var(--vf-red);
  color: var(--vf-red);
}

.settings__badge {
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
}

.settings__badge--inactive {
  background: var(--vf-gray-100);
  color: var(--vf-gray-500);
}

.settings__toggle {
  position: relative;
  display: inline-block;
  width: 44px;
  height: 24px;
  flex-shrink: 0;
}

.settings__toggle input {
  opacity: 0;
  width: 0;
  height: 0;
}

.settings__toggle-slider {
  position: absolute;
  inset: 0;
  background: var(--vf-gray-300);
  border-radius: 12px;
  transition: background var(--vf-transition);
  cursor: pointer;
}

.settings__toggle-slider::before {
  content: '';
  position: absolute;
  width: 18px;
  height: 18px;
  left: 3px;
  bottom: 3px;
  background: white;
  border-radius: 50%;
  transition: transform var(--vf-transition);
}

.settings__toggle input:checked + .settings__toggle-slider {
  background: var(--vf-red);
}

.settings__toggle input:checked + .settings__toggle-slider::before {
  transform: translateX(20px);
}

.settings__storage {
  padding: 16px;
}

.settings__storage-bar {
  height: 8px;
  background: var(--vf-gray-200);
  border-radius: 4px;
  overflow: hidden;
}

.settings__storage-fill {
  height: 100%;
  background: var(--vf-red);
  border-radius: 4px;
  transition: width 0.5s ease;
}

.settings__storage-text {
  font-size: 12px;
  color: var(--vf-gray-400);
  margin-top: 6px;
}

.settings__password-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
}

.settings__password-card {
  background: white;
  border-radius: var(--vf-radius-lg);
  padding: 24px;
  width: 100%;
  max-width: 420px;
  box-shadow: var(--vf-shadow-lg);
}

.settings__password-card h3 {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
}

.settings__password-form {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.settings__password-field label {
  display: block;
  font-size: 12px;
  font-weight: 500;
  color: var(--vf-gray-500);
  margin-bottom: 4px;
}

.settings__password-field input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
  font-size: 14px;
  outline: none;
  transition: border-color var(--vf-transition);
}

.settings__password-field input:focus {
  border-color: var(--vf-red);
}

.settings__password-error {
  font-size: 13px;
  color: #d42121;
  background: #fef2f2;
  padding: 8px 12px;
  border-radius: var(--vf-radius);
}

.settings__password-success {
  font-size: 13px;
  color: #008744;
  background: #f0fdf4;
  padding: 8px 12px;
  border-radius: var(--vf-radius);
}

.settings__password-actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
  margin-top: 4px;
}

.settings__password-submit {
  padding: 8px 20px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
}

.settings__password-submit:hover:not(:disabled) {
  background: var(--vf-red-dark);
}

.settings__password-submit:disabled {
  opacity: 0.6;
}

.settings__password-cancel {
  padding: 8px 16px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 14px;
  color: var(--vf-gray-600);
}
</style>
