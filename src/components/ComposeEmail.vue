<template>
  <div class="compose-overlay" @click.self="$emit('close')">
    <div class="compose">
      <div class="compose__header">
        <h3>Neue E-Mail</h3>
        <button class="compose__close" @click="$emit('close')">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
        </button>
      </div>
      <form @submit.prevent="send" class="compose__form">
        <div class="compose__field">
          <label>An</label>
          <input type="email" v-model="to" required placeholder="empfanger@vodafone.de" />
        </div>
        <div class="compose__field">
          <label>Betreff</label>
          <input type="text" v-model="subject" required placeholder="Betreff eingeben" />
        </div>
        <div class="compose__field compose__field--body">
          <label>Nachricht</label>
          <textarea v-model="body" placeholder="Ihre Nachricht..." rows="8"></textarea>
        </div>
        <div class="compose__actions">
          <button type="submit" class="compose__send" :disabled="sending">
            {{ sending ? 'Wird gesendet...' : 'Senden' }}
          </button>
          <button type="button" class="compose__cancel" @click="$emit('close')">Abbrechen</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['close']);

const to = ref('');
const subject = ref('');
const body = ref('');
const sending = ref(false);

async function send() {
  sending.value = true;
  await new Promise(r => setTimeout(r, 1000));
  sending.value = false;
  emit('close');
}
</script>

<style scoped>
.compose-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
}

.compose {
  background: white;
  border-radius: var(--vf-radius-lg);
  width: 100%;
  max-width: 600px;
  box-shadow: var(--vf-shadow-lg);
  animation: slideUp 0.3s ease;
}

@keyframes slideUp {
  from { transform: translateY(20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.compose__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  border-bottom: 1px solid var(--vf-gray-200);
}

.compose__header h3 {
  font-size: 16px;
  font-weight: 600;
}

.compose__close {
  border: none;
  background: none;
  color: var(--vf-gray-400);
  padding: 4px;
  border-radius: 50%;
  transition: all var(--vf-transition);
}

.compose__close:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
}

.compose__form {
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.compose__field label {
  display: block;
  font-size: 12px;
  font-weight: 500;
  color: var(--vf-gray-500);
  margin-bottom: 4px;
}

.compose__field input,
.compose__field textarea {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
  font-size: 14px;
  outline: none;
  transition: border-color var(--vf-transition);
}

.compose__field input:focus,
.compose__field textarea:focus {
  border-color: var(--vf-red);
}

.compose__field textarea {
  resize: vertical;
  min-height: 120px;
}

.compose__actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
}

.compose__send {
  padding: 8px 24px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.compose__send:hover:not(:disabled) {
  background: var(--vf-red-dark);
}

.compose__send:disabled {
  opacity: 0.6;
}

.compose__cancel {
  padding: 8px 16px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 14px;
  color: var(--vf-gray-600);
  transition: all var(--vf-transition);
}

.compose__cancel:hover {
  border-color: var(--vf-gray-400);
}
</style>
