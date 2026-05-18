<template>
  <div class="auth-overlay" @click.self="$emit('close')">
    <div class="auth-card">
      <button class="auth-close" @click="$emit('close')">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
      </button>
      <div class="auth-header">
        <svg width="40" height="40" viewBox="0 0 32 32" fill="none">
          <circle cx="16" cy="16" r="16" fill="#e60000"/>
          <path d="M16 8C11.58 8 8 11.58 8 16s3.58 8 8 8 8-3.58 8-8-3.58-8-8-8zm0 14c-3.31 0-6-2.69-6-6s2.69-6 6-6 6 2.69 6 6-2.69 6-6 6z" fill="white"/>
        </svg>
        <h2>{{ isLogin ? 'Anmelden' : 'Registrieren' }}</h2>
        <p>{{ isLogin ? 'Melden Sie sich bei Ihrem Konto an' : 'Erstellen Sie ein neues Konto' }}</p>
      </div>
      <form @submit.prevent="handleSubmit" class="auth-form">
        <div class="auth-field">
          <label>E-Mail-Adresse</label>
          <input type="email" v-model="email" required placeholder="name@vodafone.de" />
        </div>
        <div class="auth-field">
          <label>Passwort</label>
          <input type="password" v-model="password" required placeholder="Passwort eingeben" minlength="6" />
        </div>
        <div class="auth-error" v-if="error">{{ error }}</div>
        <button type="submit" class="auth-submit" :disabled="loading">
          {{ loading ? 'Bitte warten...' : (isLogin ? 'Anmelden' : 'Registrieren') }}
        </button>
      </form>
      <div class="auth-switch">
        <span>{{ isLogin ? 'Noch kein Konto?' : 'Bereits registriert?' }}</span>
        <button @click="isLogin = !isLogin" class="auth-switch-btn">
          {{ isLogin ? 'Registrieren' : 'Anmelden' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { supabase } from '../supabase.js';

const emit = defineEmits(['close', 'auth']);

const email = ref('');
const password = ref('');
const isLogin = ref(true);
const loading = ref(false);
const error = ref('');

async function handleSubmit() {
  error.value = '';
  loading.value = true;

  try {
    const { data, error: authError } = isLogin.value
      ? await supabase.auth.signInWithPassword({ email: email.value, password: password.value })
      : await supabase.auth.signUp({ email: email.value, password: password.value });

    if (authError) {
      error.value = authError.message;
    } else if (data.session) {
      emit('auth', data.session);
    } else {
      error.value = 'Bitte bestatigen Sie Ihre E-Mail-Adresse.';
    }
  } catch {
    error.value = 'Ein Fehler ist aufgetreten. Bitte versuchen Sie es erneut.';
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.auth-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
  animation: fadeIn 0.2s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.auth-card {
  background: white;
  border-radius: var(--vf-radius-lg);
  padding: 32px;
  width: 100%;
  max-width: 420px;
  position: relative;
  box-shadow: var(--vf-shadow-lg);
  animation: slideUp 0.3s ease;
}

@keyframes slideUp {
  from { transform: translateY(20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.auth-close {
  position: absolute;
  top: 16px;
  right: 16px;
  border: none;
  background: none;
  color: var(--vf-gray-400);
  padding: 4px;
  border-radius: 50%;
  transition: all var(--vf-transition);
}

.auth-close:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
}

.auth-header {
  text-align: center;
  margin-bottom: 28px;
}

.auth-header h2 {
  font-size: 22px;
  font-weight: 600;
  color: var(--vf-gray-900);
  margin-top: 16px;
}

.auth-header p {
  font-size: 14px;
  color: var(--vf-gray-500);
  margin-top: 4px;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.auth-field label {
  display: block;
  font-size: 13px;
  font-weight: 500;
  color: var(--vf-gray-700);
  margin-bottom: 6px;
}

.auth-field input {
  width: 100%;
  padding: 10px 14px;
  border: 1px solid var(--vf-gray-300);
  border-radius: var(--vf-radius);
  font-size: 14px;
  transition: border-color var(--vf-transition);
  outline: none;
}

.auth-field input:focus {
  border-color: var(--vf-red);
  box-shadow: 0 0 0 3px rgba(230, 0, 0, 0.1);
}

.auth-error {
  font-size: 13px;
  color: #d42121;
  background: #fef2f2;
  padding: 8px 12px;
  border-radius: var(--vf-radius);
}

.auth-submit {
  width: 100%;
  padding: 12px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 15px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.auth-submit:hover:not(:disabled) {
  background: var(--vf-red-dark);
}

.auth-submit:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.auth-switch {
  text-align: center;
  margin-top: 20px;
  font-size: 13px;
  color: var(--vf-gray-500);
}

.auth-switch-btn {
  border: none;
  background: none;
  color: var(--vf-red);
  font-weight: 500;
  font-size: 13px;
}

.auth-switch-btn:hover {
  text-decoration: underline;
}
</style>
