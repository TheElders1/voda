<template>
  <div class="calendar">
    <div class="calendar__toolbar">
      <h2>Kalender</h2>
      <div class="calendar__nav">
        <button @click="prevMonth">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="15 18 9 12 15 6"/></svg>
        </button>
        <span class="calendar__month">{{ monthName }} {{ year }}</span>
        <button @click="nextMonth">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="9 18 15 12 9 6"/></svg>
        </button>
      </div>
      <button class="calendar__add-btn" @click="showAdd = true">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
        Neuer Termin
      </button>
    </div>

    <div class="calendar__grid">
      <div class="calendar__day-header" v-for="day in weekDays" :key="day">{{ day }}</div>
      <div
        v-for="(day, i) in calendarDays"
        :key="i"
        class="calendar__day"
        :class="{
          'calendar__day--other': day.other,
          'calendar__day--today': day.today,
          'calendar__day--selected': day.date === selectedDate
        }"
        @click="selectedDate = day.date"
      >
        <span class="calendar__day-num">{{ day.num }}</span>
        <div class="calendar__events" v-if="day.events.length">
          <div
            v-for="event in day.events"
            :key="event.id"
            class="calendar__event"
            :class="'calendar__event--' + event.type"
          >{{ event.title }}</div>
        </div>
      </div>
    </div>

    <div class="calendar__day-detail" v-if="selectedEvents.length">
      <h3>{{ formatSelectedDate }}</h3>
      <div class="calendar__detail-list">
        <div v-for="event in selectedEvents" :key="event.id" class="calendar__detail-item">
          <div class="calendar__detail-dot" :class="'calendar__detail-dot--' + event.type"></div>
          <div>
            <div class="calendar__detail-title">{{ event.title }}</div>
            <div class="calendar__detail-time">{{ event.time }}</div>
          </div>
        </div>
      </div>
    </div>

    <div class="calendar__add-overlay" v-if="showAdd" @click.self="showAdd = false">
      <div class="calendar__add-card">
        <h3>Neuer Termin</h3>
        <form @submit.prevent="addEvent" class="calendar__add-form">
          <div class="calendar__add-field">
            <label>Titel</label>
            <input type="text" v-model="newTitle" required placeholder="Terminname" />
          </div>
          <div class="calendar__add-field">
            <label>Datum</label>
            <input type="date" v-model="newDate" required />
          </div>
          <div class="calendar__add-field">
            <label>Uhrzeit</label>
            <input type="time" v-model="newTime" required />
          </div>
          <div class="calendar__add-actions">
            <button type="submit" class="calendar__add-submit">Speichern</button>
            <button type="button" class="calendar__add-cancel" @click="showAdd = false">Abbrechen</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const now = new Date();
const year = ref(now.getFullYear());
const month = ref(now.getMonth());
const selectedDate = ref('');
const showAdd = ref(false);
const newTitle = ref('');
const newDate = ref('');
const newTime = ref('');

const weekDays = ['Mo', 'Di', 'Mi', 'Do', 'Fr', 'Sa', 'So'];

const monthNames = ['Januar', 'Februar', 'Marz', 'April', 'Mai', 'Juni', 'Juli', 'August', 'September', 'Oktober', 'November', 'Dezember'];

const monthName = computed(() => monthNames[month.value]);

const events = ref([
  { id: 1, date: `${year.value}-${String(month.value + 1).padStart(2, '0')}-05`, title: 'Team-Meeting', time: '09:00', type: 'work' },
  { id: 2, date: `${year.value}-${String(month.value + 1).padStart(2, '0')}-12`, title: 'Arzttermin', time: '14:30', type: 'personal' },
  { id: 3, date: `${year.value}-${String(month.value + 1).padStart(2, '0')}-18`, title: 'Kundentermin', time: '10:00', type: 'work' },
  { id: 4, date: `${year.value}-${String(month.value + 1).padStart(2, '0')}-22`, title: 'Geburtstag', time: '18:00', type: 'personal' }
]);

const calendarDays = computed(() => {
  const firstDay = new Date(year.value, month.value, 1);
  const lastDay = new Date(year.value, month.value + 1, 0);
  const startOffset = (firstDay.getDay() + 6) % 7;
  const days = [];

  const prevMonthLast = new Date(year.value, month.value, 0).getDate();
  for (let i = startOffset - 1; i >= 0; i--) {
    const d = prevMonthLast - i;
    const date = `${year.value}-${String(month.value).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
    days.push({ num: d, other: true, today: false, date, events: [] });
  }

  for (let d = 1; d <= lastDay.getDate(); d++) {
    const date = `${year.value}-${String(month.value + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
    const isToday = d === now.getDate() && month.value === now.getMonth() && year.value === now.getFullYear();
    const dayEvents = events.value.filter(e => e.date === date);
    days.push({ num: d, other: false, today: isToday, date, events: dayEvents });
  }

  const remaining = 42 - days.length;
  for (let d = 1; d <= remaining; d++) {
    const date = `${year.value}-${String(month.value + 2).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
    days.push({ num: d, other: true, today: false, date, events: [] });
  }

  return days;
});

const selectedEvents = computed(() => {
  if (!selectedDate.value) return [];
  return events.value.filter(e => e.date === selectedDate.value);
});

const formatSelectedDate = computed(() => {
  if (!selectedDate.value) return '';
  const d = new Date(selectedDate.value);
  return `${d.getDate()}. ${monthNames[d.getMonth()]} ${d.getFullYear()}`;
});

function prevMonth() {
  if (month.value === 0) { month.value = 11; year.value--; }
  else month.value--;
}

function nextMonth() {
  if (month.value === 11) { month.value = 0; year.value++; }
  else month.value++;
}

function addEvent() {
  events.value.push({
    id: Date.now(),
    date: newDate.value,
    title: newTitle.value,
    time: newTime.value,
    type: 'personal'
  });
  newTitle.value = '';
  newDate.value = '';
  newTime.value = '';
  showAdd.value = false;
}
</script>

<style scoped>
.calendar {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.calendar__toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

.calendar__toolbar h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--vf-gray-900);
}

.calendar__nav {
  display: flex;
  align-items: center;
  gap: 12px;
}

.calendar__nav button {
  padding: 6px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: 50%;
  color: var(--vf-gray-600);
  display: flex;
  align-items: center;
  transition: all var(--vf-transition);
}

.calendar__nav button:hover {
  border-color: var(--vf-red);
  color: var(--vf-red);
}

.calendar__month {
  font-size: 16px;
  font-weight: 500;
  color: var(--vf-gray-900);
  min-width: 140px;
  text-align: center;
}

.calendar__add-btn {
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

.calendar__add-btn:hover {
  background: var(--vf-red-dark);
}

.calendar__grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
}

.calendar__day-header {
  padding: 10px;
  text-align: center;
  font-size: 12px;
  font-weight: 600;
  color: var(--vf-gray-500);
  background: var(--vf-gray-50);
  border-bottom: 1px solid var(--vf-gray-200);
}

.calendar__day {
  min-height: 80px;
  padding: 6px;
  border-bottom: 1px solid var(--vf-gray-100);
  border-right: 1px solid var(--vf-gray-100);
  cursor: pointer;
  transition: background var(--vf-transition);
}

.calendar__day:nth-child(7n) {
  border-right: none;
}

.calendar__day:hover {
  background: var(--vf-gray-50);
}

.calendar__day--other {
  opacity: 0.3;
}

.calendar__day--today .calendar__day-num {
  background: var(--vf-red);
  color: white;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.calendar__day--selected {
  background: #fef2f2;
}

.calendar__day-num {
  font-size: 13px;
  font-weight: 500;
  color: var(--vf-gray-700);
  margin-bottom: 4px;
}

.calendar__events {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.calendar__event {
  font-size: 10px;
  padding: 2px 4px;
  border-radius: 3px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.calendar__event--work {
  background: #eff6ff;
  color: #006dfe;
}

.calendar__event--personal {
  background: #f0fdf4;
  color: #008744;
}

.calendar__day-detail {
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  padding: 20px;
}

.calendar__day-detail h3 {
  font-size: 16px;
  font-weight: 600;
  color: var(--vf-gray-900);
  margin-bottom: 12px;
}

.calendar__detail-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.calendar__detail-item {
  display: flex;
  align-items: center;
  gap: 10px;
}

.calendar__detail-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  flex-shrink: 0;
}

.calendar__detail-dot--work { background: #006dfe; }
.calendar__detail-dot--personal { background: #008744; }

.calendar__detail-title {
  font-size: 14px;
  font-weight: 500;
  color: var(--vf-gray-900);
}

.calendar__detail-time {
  font-size: 12px;
  color: var(--vf-gray-400);
}

.calendar__add-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
}

.calendar__add-card {
  background: white;
  border-radius: var(--vf-radius-lg);
  padding: 24px;
  width: 100%;
  max-width: 420px;
  box-shadow: var(--vf-shadow-lg);
}

.calendar__add-card h3 {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
}

.calendar__add-form {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.calendar__add-field label {
  display: block;
  font-size: 12px;
  font-weight: 500;
  color: var(--vf-gray-500);
  margin-bottom: 4px;
}

.calendar__add-field input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
  font-size: 14px;
  outline: none;
  transition: border-color var(--vf-transition);
}

.calendar__add-field input:focus {
  border-color: var(--vf-red);
}

.calendar__add-actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
  margin-top: 4px;
}

.calendar__add-submit {
  padding: 8px 20px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
}

.calendar__add-submit:hover {
  background: var(--vf-red-dark);
}

.calendar__add-cancel {
  padding: 8px 16px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 14px;
  color: var(--vf-gray-600);
}

@media (max-width: 768px) {
  .calendar__day { min-height: 50px; }
  .calendar__event { display: none; }
}
</style>
